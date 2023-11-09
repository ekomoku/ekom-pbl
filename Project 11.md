
## Project 11: Ansible – Automate Project 7 to 10

#### INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE

1. Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.
2. In your GitHub account create a new repository and name it ansible-config-mgt.
3. Instal Ansible

~~~
sudo apt update

sudo apt install ansible
~~~


Check your Ansible version by running ansible --version
4. Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9.

4a. Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config’ repository.


![Screenshot from 2023-11-07 22-07-37](https://github.com/ekomoku/ekom-pbl/assets/66005935/1a8ca138-0eb9-4768-abae-8e6a7bf40ab7)

In Jenkins, Goto>Ansible>Configuration, under source code Management, check the 'Git' radio utton and copy the url of the ‘ansible-config’ repository in Git and paste it there.


![Screenshot from 2023-11-07 22-36-45](https://github.com/ekomoku/ekom-pbl/assets/66005935/a0a1ed51-1a32-425e-8ac8-c10ad1efe9e3)

In jenkins, under 'Build Triggers' select 'GitHub hook trigger for GITScm polling?'

![Screenshot from 2023-11-07 22-39-27](https://github.com/ekomoku/ekom-pbl/assets/66005935/3f9a4ef5-677c-4990-a8ac-11923f3a4c77)


![Screenshot from 2023-11-07 22-37-33](https://github.com/ekomoku/ekom-pbl/assets/66005935/cabd5efd-39cd-4155-9e57-e7509782293a)


4b. Configure Webhook in GitHub and set webhook to trigger ansible build.

To configure webhook in Git, Goto > ansible-config > settings > webhooks > Add Webhooks, copy the IPv4 IP address of the Jenkins-ansible server and paste un 'Payload URL as shown below

~~~
http://35.183.17.65:8080/github-webhook/
~~~

change the content type to 'application/json'


![Screenshot from 2023-11-07 22-57-32](https://github.com/ekomoku/ekom-pbl/assets/66005935/e1ff193e-4518-48a2-8619-1c3753d15a78)



4c. Configure a Post-build job to save all (**) files, like you did it in Project 9.

In jenkins, goto > Post-build Actions> select 'Archive the artifacts and enter ** in the field. 

#### Remember to change the branch from /master to /main in jenkins


![Screenshot from 2023-11-07 23-02-24](https://github.com/ekomoku/ekom-pbl/assets/66005935/bf44d5e4-979b-4d84-9a2e-af250820bbad)


5. Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder.


~~~
ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
~~~

Note: Trigger Jenkins project execution only for /main (master) branch.

Tip: Every time you stop/start your Jenkins-Ansible server – you have to reconfigure GitHub webhook to a new IP address, in order to avoid it, it makes sense to allocate an Elastic IP to your Jenkins-Ansible server (you have done it before to your LB server in Project 10). Note that Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.


### Step 2 - Prepare your development environment using Visual Studio Code

1. Install visual studio code. Install the extension ( Remote Development)

On VS Code, Goto > Extensions and search for 'Remote Development' then install it.


3. After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

4. Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance
In VS code, click explorer, click 'clone repository', in the search bar, enter/select the github directory of for the repo from the dropdown.

~~~
git clone <ansible-config-mgt repo link>
~~~

OR clone from VS code



### BEGIN ANSIBLE DEVELOPMENT

1. In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature.

~~~
git status
git checkout branch -b prj-11
~~~


Tip: Give your branches descriptive and comprehensive names, for example, if you use Jira or Trello as a project management tool – include ticket number (e.g. PRJ-145) in the name of your branch and add a topic and a brief description what this branch is about – a bugfix, hotfix, feature, release (e.g. feature/prj-145-lvm)

2. Checkout the newly created feature branch to your local machine and start building your code and directory structure
3. Create a directory and name it playbooks – it will be used to store all your playbook files.

~~~
mkdir playbooks
~~~


5. Create a directory and name it inventory – it will be used to keep your hosts organised.


~~~
mkdir inventory
~~~


7. Within the playbooks folder, create your first playbook, and name it common.yml


~~~
cd playbooks
touch common.yml
~~~



9. Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.


~~~
cd ..
cd inventory
touch dev.yml staging.yml uat.yml prod.yml
~~~

![Screenshot from 2023-02-09 10-34-57](https://user-images.githubusercontent.com/66005935/218161243-a4de9bbe-00be-407d-9fc2-9f184fff109b.png)



### Step 4 – Set up an Ansible Inventory


An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.


Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:

To learn how to setup SSH agent and connect VS Code to your Jenkins-Ansible instance, please see this video:

For Windows users –  https://www.youtube.com/watch?v=OplGrY74qog
For Linux users –  https://www.youtube.com/watch?v=OplGrY74qog


To add the private key, open terminal on your PC, cd into the directory/folder where your private key is (e.g Downloads folder) and run the following commands


~~~
eval `ssh-agent -s`
ssh-add <path-to-private-key>
~~~


#### Note: The above commands becomes


![Screenshot from 2023-02-10 19-01-56](https://user-images.githubusercontent.com/66005935/218163680-01310f7d-0762-47b7-aef9-66f308deb415.png)



Still on your local terminal, Confirm the key has been added with the command below, you should see the name of your key. Also connect to your server with the commands below


~~~
ssh-add -l

~~~


Now, ssh into your Jenkins-Ansible server using ssh-agent



~~~
ssh -A <server_name>@<public_IP_address_of_your_server>
~~~


Also notice, that your Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.
#### Note: server name can be ubutu( for ubuntu servers), ec2-user(for RHEL servers), etc.


![Screenshot from 2023-02-10 19-14-31](https://user-images.githubusercontent.com/66005935/218166556-4d648d0d-e18c-4881-a23f-f7ce091993d2.png)



Update your inventory/dev.yml file with this snippet of code:

~~~
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
~~~


![Screenshot from 2023-02-10 19-56-20](https://user-images.githubusercontent.com/66005935/218174854-40fd7208-967c-4aa4-8eee-5ad444b6861d.png)



### Step 5 – Create a Common Playbook


It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in inventory/dev.


In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.


Update your playbooks/common.yml file with following code:


~~~
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
~~~



Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.



For a better understanding of Ansible playbooks – watch this video from RedHat( https://www.youtube.com/watch?v=ZAdJ7CdN7DY)  and read this article ( https://www.redhat.com/en/topics/automation/what-is-an-ansible-playbook)


### Step 6 – Update GIT with the latest code


Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.

In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes – it is also called "Four eyes principle".

Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.


Commit your code into GitHub:

1. use git commands to add, commit and push your branch to GitHub.

~~~
git status

git add .

git commit -m "commit message"
git push origin prj-11
~~~


![Screenshot from 2023-02-11 00-19-08](https://user-images.githubusercontent.com/66005935/218220491-2691091e-e5df-4bb8-b708-ae6fd37826c9.png)


2. Create a Pull request (PR)


To create a Pull Request, Goto to github repository as shown after git push e.g 

remote: Create a pull request for 'prj-11' on GitHub by visiting:
remote:      https://github.com/ekomoku/ansible-config-mgt/pull/new/prj-11



![Screenshot from 2023-02-11 00-24-31](https://user-images.githubusercontent.com/66005935/218221043-9acd473a-8fd7-444a-beaa-b93b227675bc.png)




3. Wear a hat of another developer for a second, and act as a reviewer.

4. If the reviewer is happy with your new feature development, merge the code to the master branch.

5. Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.


~~~
git checkout main
git pull
~~~


Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.



### Step 7 – Run first Ansible test


Now, it is time to execute ansible-playbook command and verify if your playbook actually works:


~~~
cd ansible-config-mgt
ansible-playbook -i inventory/dev.yml playbooks/common.yml
~~~

On the VS code, goto the lower left corner of the window, click 'Open a Remote Window'


![Screenshot from 2023-02-11 10-01-58](https://user-images.githubusercontent.com/66005935/218249837-a6388735-d1b0-403f-8800-206dfc83f85e.png)


on the top of the VS code window, under the search bar, select 'open ssh configuration file' and select /home/ekom/.ssh/config

Edit the file with code below where Host name is the public IP of the Jenkins - Ansible. 

IdentifyFile : is the path to where the private key is located


~~~
Host Jenkins - Ansible
    HostName 13.41.159.207
    User ubuntu
    IdentityFile /home/ekom/Downloads/jenkinskeypair.pem
    ForwardAgent yes
    ControlPath /tmp/ansible-ssh-%h-%p-%r
    ControlMaster auto
    ControlPersist 10m
~~~


Then on VS code, click 'Open a Remote Window' on the lower left corner of the window. select 'connect to a host'. At this point, you should see your host listed. Select and connect to it.


==============================
#### Note: if you encounter error when trying to connect again, do the following
1. Open the command panel on VS code (Ctrl+Shift+P for Windows and Cmd+Shift+P for Mac).
2. Search for Kill VS Code Server on Host, click it - it will be automatically deleted.
3. Reload VS Code and establish connection again.
==============================



When you coonect to the Ansible-Jenkins server through ssh, on the terminal run the following command:


~~~
ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/<build-number>/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/<build-number>/archive/playbooks/common.yml
~~~


#### Note: build-number is the one jenkins server e.g #4 will be 4


GIT commands: after making changes to your file, push with the following commands 

~~~
git add  playbooks/common.yml
git commit -m "edited common.yml"
git push origin prj-11
~~~


#### Then proceed to github to create merge request and merge. Note: in the above codes, common.yml is the file that is edited inside the folder playbooks and the current branch is prj-11.


