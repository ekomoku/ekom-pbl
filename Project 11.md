
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

4a. Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.
4b. Configure Webhook in GitHub and set webhook to trigger ansible build.
4c. Configure a Post-build job to save all (**) files, like you did it in Project 9.

5. Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder.


~~~
ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
~~~

Note: Trigger Jenkins project execution only for /main (master) branch.

Tip: Every time you stop/start your Jenkins-Ansible server – you have to reconfigure GitHub webhook to a new IP address, in order to avoid it, it makes sense to allocate an Elastic IP to your Jenkins-Ansible server (you have done it before to your LB server in Project 10). Note that Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.


### Step 2 - Prepare your development environment using Visual Studio Code

1. Install visual studio code. Install the extension ( Remote Development)
2. After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

3. Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance
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








