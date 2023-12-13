# Ansible Refactoring & Static Assignments (Imports and Roles)

In this project you will continue working with `ansible-config-mgt` repository and make some improvements of your code. Now you need to refactor your Ansible code, create assignments, and learn how to use the imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook - it allows you to organize your tasks and reuse them when needed.

**Side Self Study:** For better understanding or Ansible artifacts re-use - [read this article](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse.html).

#### Instructions On How To Submit Your Work For Review And Feedback

To submit your work for review and feedback - follow [**this instruction**](https://starter-pbl.darey.io/en/latest/submission.html).

#### Code Refactoring 

[Refactoring](https://en.wikipedia.org/wiki/Code_refactoring) is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.

In your case, you will move things around a little bit in the code, but the overal state of the infrastructure remains the same.

Let us see how you can improve your Ansible code!


#### Step 1 - Jenkins job enhancement

Before we begin, let us make some changes to our Jenkins job - now every new change in the codes creates a separate directory which is not very convenient when we want to run some commands from one place. Besides, it consumes space on Jenkins serves with each subsequent change. Let us enhance it by introducing a new Jenkins project/job - we will require [`Copy Artifact`](https://plugins.jenkins.io/copyartifact/) plugin.

1. Go to your `Jenkins-Ansible` server and create a new directory called `ansible-config-artifact` - we will store there all artifacts after each build.

```
sudo mkdir /home/ubuntu/ansible-config-artifact

```

2. Change permissions to this directory, so Jenkins could save files there -



~~
sudo chmod -R 0777 ansible-config-artifact
~~


![Screenshot from 2023-12-12 16-35-13](https://github.com/ekomoku/ekom-pbl/assets/66005935/9f5285c3-4aeb-4acd-b0e4-bdf1f08f1279)



4. Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on `Available` tab search for `Copy Artifact` and install this plugin without restarting Jenkins


![Screenshot from 2023-12-12 16-37-48](https://github.com/ekomoku/ekom-pbl/assets/66005935/c4da2390-cea8-4238-88c0-d7a226abfd7a)



![Screenshot from 2023-12-12 16-40-30](https://github.com/ekomoku/ekom-pbl/assets/66005935/54fb9124-fcb2-4371-bcac-0ee7bf449891)




<img src="https://darey-io-nonprod-pbl-projects.s3.eu-west-2.amazonaws.com/project12/copy_artifact_install.png" width="936px" height="550px">

4. Create a new Freestyle project (you have done it in [Project 9](https://professional-pbl.darey.io/en/latest/project9.html)) and name it `save_artifacts`.


![Screenshot from 2023-12-12 16-42-20](https://github.com/ekomoku/ekom-pbl/assets/66005935/4af00c05-ece6-40c5-87cb-d3f1c6c201e0)



5. This project will be triggered by completion of your existing `ansible` project. Configure it accordingly:


**Note**: You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your `ansible` job.


![Screenshot from 2023-12-12 16-44-13](https://github.com/ekomoku/ekom-pbl/assets/66005935/ca991e78-9888-4465-b9c8-824be9f5e9c1)


We update the Source Code Management section.



![Screenshot from 2023-12-12 16-46-52](https://github.com/ekomoku/ekom-pbl/assets/66005935/6e6a516f-af96-4221-bfd5-0084e1645187)




6. The main idea of `save_artifacts` project is to save artifacts into `/home/ubuntu/ansible-config-artifact` directory. To achieve this, create a `Build` step and choose `Copy artifacts from other project`, specify `ansible` as a source project and `/home/ubuntu/ansible-config-artifact` as a target directory.



![Screenshot from 2023-12-12 16-48-18](https://github.com/ekomoku/ekom-pbl/assets/66005935/5b3a70e0-ca3d-4009-837b-9b9b407a8592)



![Screenshot from 2023-12-12 16-50-11](https://github.com/ekomoku/ekom-pbl/assets/66005935/eda995bd-9f8c-4c21-b9a4-4f7d88942a41)



Then click on apply and save.



![](https://darey-io-nonprod-pbl-projects.s3.eu-west-2.amazonaws.com/project12/copy_artifact_build.png)
<img src="https://darey-io-nonprod-pbl-projects.s3.eu-west-2.amazonaws.com/project11/ansible_version.png" width="936px" height="550px">

7. Test your set up by making some change in README.MD file inside your `ansible-config-mgt` repository (right inside `master` branch).


![Screenshot from 2023-12-12 17-46-15](https://github.com/ekomoku/ekom-pbl/assets/66005935/abebd85a-7423-48c8-82c6-9112f339e89e)



![Screenshot from 2023-12-12 17-46-55](https://github.com/ekomoku/ekom-pbl/assets/66005935/0936cc37-3182-4ef9-9af8-8810023798a2)



![Screenshot from 2023-12-12 17-47-37](https://github.com/ekomoku/ekom-pbl/assets/66005935/61db25c7-c6d4-485e-8d4d-c13a3979179e)



If both Jenkins jobs have completed one after another - you shall see your files inside `/home/ubuntu/ansible-config-artifact` directory and it will be updated with every commit to your `master` branch.

Now your Jenkins pipeline is more neat and clean.

#### Step 2 - Refactor Ansible code by importing other playbooks into `site.yml`

Before starting to refactor the codes, ensure that you have pulled down the latest code from `master` (main) branch, and create a new branch, name it `refactor`.

DevOps philosophy implies constant iterative improvement for better efficiency - refactoring is one of the techniques that can be used, but you always have an answer to question "why?". Why do we need to change something if it works well?

In [Project 11](https://professional-pbl.darey.io/en/latest/project11.html) you wrote all tasks in a single playbook `common.yml`, now it is pretty simple set of instructions for only 2 types of OS, but imagine you have many more tasks and you need to apply this playbook to other servers with different requirements. In this case, you will have to read through the whole playbook to check if all tasks written there are applicable and is there anything that you need to add for certain server/OS families. Very fast it will become a tedious exercise and your playbook will become messy with many commented parts. Your DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use your playbook.

Most Ansible users learn the one-file approach first. However, breaking tasks up into different files is an excellent way to organize complex sets of tasks and reuse them.

Let see code re-use in action by importing other playbooks.


Before starting to refactor the code, We pull down the latest code from main branch and creat a new branch refactor.


![Screenshot from 2023-12-12 17-55-14](https://github.com/ekomoku/ekom-pbl/assets/66005935/fb2b76c0-19bb-482c-80e4-11eda21e8af1)



1. Within `playbooks` folder, create a new file and name it `site.yml` - *This file will now be considered as an entry point into the entire infrastructure configuration*. Other playbooks will be included here as a reference. In other words, `site.yml` will become a parent to all other playbooks that will be developed. Including `common.yml` that you created previously. *Dont worry, you will understand more what this means shortly*.

2. Create a new folder in root of the repository and name it `static-assignments`. The **static-assignments** folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of **static** very soon. For now, just follow along.


![Screenshot from 2023-12-12 18-01-26](https://github.com/ekomoku/ekom-pbl/assets/66005935/9a746e89-5230-4544-9051-c1e07b3a487f)


3. Move `common.yml` file into the newly created `static-assignments` folder.


![Screenshot from 2023-12-12 18-04-44](https://github.com/ekomoku/ekom-pbl/assets/66005935/586ae7e1-f082-415a-ba6a-b8d144f3dcfd)



4. Inside `site.yml` file, import `common.yml` playbook.

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml
```


![Screenshot from 2023-12-12 18-10-58](https://github.com/ekomoku/ekom-pbl/assets/66005935/5d8bcd91-58d7-46a4-97bc-c9f0c57a39b4)



The code above uses built in [import_playbook](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_playbook_module.html) Ansible module.

Your folder structure should look like this;

```
├── static-assignments
│   └── common.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml
```

5. Run `ansible-playbook` command against the `dev` environment

Since you need to apply some tasks to your `dev` servers and `wireshark` is already installed - you can go ahead and create another playbook under `static-assignments` and name it `common-del.yml`. In this playbook, configure deletion of `wireshark` utility.



![Screenshot from 2023-12-12 18-31-26](https://github.com/ekomoku/ekom-pbl/assets/66005935/b3fdc7c2-96d0-4152-bcbe-a12416455167)



In the static-assignment/common-del.yml we paste the snippet


```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    apt:
      name: wireshark-qt
      state: absent
      autoremove: yes
      purge: yes
      autoclean: yes
```


![Screenshot from 2023-12-13 11-31-56](https://github.com/ekomoku/ekom-pbl/assets/66005935/e21e9e90-c213-46b7-a813-6f8659e91100)





Update playbooks/site.yml with the following snippet replacing common.yml with common-del.yml.




~~~
---
- hosts: all
- import_playbook: ../static-assignments/common-del.yml
~~~



![Screenshot from 2023-12-12 18-44-09](https://github.com/ekomoku/ekom-pbl/assets/66005935/e8c74b47-a10f-4453-bdde-d2275aaa9db8)




To push the codes to github and merge to the main branch

We run the commands

$ git status

$ git add .



![Screenshot from 2023-12-12 18-48-14](https://github.com/ekomoku/ekom-pbl/assets/66005935/d70e33f2-ef50-4914-bff1-a12353f21ba9)


then

$ git commit -m "<commit-message>"

then

$ git push origin refactor



![Screenshot from 2023-12-12 18-51-52](https://github.com/ekomoku/ekom-pbl/assets/66005935/6557de6c-5f4a-466c-8ad9-7e7e92546dc0)



We then go to the github and create a pull request



![Screenshot from 2023-12-12 18-54-03](https://github.com/ekomoku/ekom-pbl/assets/66005935/88f8a68a-c993-4be9-8459-960303f5a607)



![Screenshot from 2023-12-12 18-55-45](https://github.com/ekomoku/ekom-pbl/assets/66005935/c56678db-d046-4c31-8933-f9935d4b4889)



We can see the Jenkins build as soon as the code is merged to the main branch



![Screenshot from 2023-12-12 18-57-30](https://github.com/ekomoku/ekom-pbl/assets/66005935/3b44a171-58bc-4988-af97-fb865d51f8c2)



The artifact is saved in the ansible-config-artifact directory in the jenkins server.

Then run the playbook

~~~
$ ansible-playbook ansible-config-artifact/playbooks/site.yml -i ansible-config-artifact/inventory/dev.yml
~~~



![Screenshot from 2023-12-13 11-34-57](https://github.com/ekomoku/ekom-pbl/assets/66005935/4bc69e1a-bae5-4016-a370-ae756bfb74f2)





Make sure that `wireshark` is deleted on all the servers by running `wireshark --version`


![Screenshot from 2023-12-13 11-39-15](https://github.com/ekomoku/ekom-pbl/assets/66005935/131e103c-e5b2-4f5b-95f0-027fa2df7c57)




Now you have learned how to use `import_playbooks` module and you have a ready solution to install/delete packages on multiple servers with just one command.


#### Step 3 - Configure UAT Webservers with a role 'Webserver'

We have our nice and clean `dev` environment, so let us put it aside and configure 2 new Web Servers as `uat`. We could write tasks to configure Web Servers in the same playbook, but it would be too messy, instead, we will use a dedicated [role](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html) to make our configuration reusable.

1. Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our `uat` servers, so give them names accordingly - `Web1-UAT` and `Web2-UAT`. 

**Tip:** Do not forget to stop EC2 instances that you are not using at the moment to avoid paying extra. For now, you only need 2 new RHEL 8 servers as Web Servers and 1 existing `Jenkins-Ansible` server up and running.

2. To create a role, you must create a directory called `roles/`, relative to the playbook file or in `/etc/ansible/` directory. 

There are two ways how you can create this folder structure:


Checkout to a new branch and name it 'uat'


- Use an Ansible utility called `ansible-galaxy` inside `ansible-config-mgt/roles` directory (you need to create `roles` directory upfront)


```
mkdir roles
cd roles
```


![Screenshot from 2023-12-13 12-28-27](https://github.com/ekomoku/ekom-pbl/assets/66005935/51274be6-9766-45d7-9a56-f23b3590b97a)


We edit the roles path: goto the Jenkins-Ansible server and run the code below;


~~~
$ sudo vi /etc/ansible/ansible.cfg
~~~


Then search for roles path ; replace the path with /home/ubuntu/ansible-config11/roles


![Screenshot from 2023-12-13 12-59-05](https://github.com/ekomoku/ekom-pbl/assets/66005935/347c5a13-8933-4385-b9aa-530d74df2a99)

Then run


~~~
$ sudo apt install ansible-core  # in the ansible-config11 directory
ansible-galaxy init webserver
~~~


![Screenshot from 2023-12-13 13-39-40](https://github.com/ekomoku/ekom-pbl/assets/66005935/18c5e88d-99d5-4c02-b50e-e2f31dab9902)



![Screenshot from 2023-12-13 13-49-10](https://github.com/ekomoku/ekom-pbl/assets/66005935/e19404d2-7e55-473c-9ac4-b611ff47619d)





To install tree

~~~
$ sudo apt install tree -y
~~~



![Screenshot from 2023-12-13 14-10-26](https://github.com/ekomoku/ekom-pbl/assets/66005935/392e5898-614d-4377-9e77-9335975f1e2f)



- Create the directory/files structure manually

**Note:** You can choose either way, but since you store all your codes in GitHub, it is recommended to create folders and files there rather than locally on `Jenkins-Ansible` server.

The entire folder structure should look like below, but if you create it manually - you can skip creating `tests`, `files`, and `vars` or remove them if you used `ansible-galaxy`



```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml
```


![Screenshot from 2023-12-13 13-44-35](https://github.com/ekomoku/ekom-pbl/assets/66005935/d09837db-30a9-4846-8d8e-26a72da49aad)





After removing unnecessary directories and files, the `roles` structure should look like this


```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    └── templates
```



![Screenshot from 2023-12-13 14-27-15](https://github.com/ekomoku/ekom-pbl/assets/66005935/91619343-5ed6-4242-afcb-debe5a08e3e3)




3. Update your inventory `ansible-config-mgt/inventory/uat.yml` file with IP addresses of your 2 UAT Web servers



```
[uat-webservers]
<Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
```


![Screenshot from 2023-12-13 13-53-53](https://github.com/ekomoku/ekom-pbl/assets/66005935/9e06ab3a-eec2-4a2d-aebe-3c617ae4ca35)



![Screenshot from 2023-12-13 14-32-08](https://github.com/ekomoku/ekom-pbl/assets/66005935/2f0bb1b8-062f-4d59-9670-c81eab69a70b)




4. In `/etc/ansible/ansible.cfg` file uncomment `roles_path` string and provide a full path to your roles directory `roles_path    = /home/ubuntu/ansible-config-mgt/roles`, so Ansible could know where to find configured roles.


Update the jenkins /etc/hosts directory with the UAT webservers.

Goto Jenkins-Ansible server and run the command below;


~~~
$ sudo vi /etc/hosts
~~~


![Screenshot from 2023-12-13 14-38-38](https://github.com/ekomoku/ekom-pbl/assets/66005935/a73e2431-42cc-4c7b-8533-b0683e9e3d2d)



![Screenshot from 2023-12-13 14-42-05](https://github.com/ekomoku/ekom-pbl/assets/66005935/f459f8c3-a509-421b-ad2f-352828c8f1e7)



Still on the Jenkins-Ansible server, Go to /etc/ansible/ansible.cfg and scroll down to 'privilege_escalation' and remove the comment "become=True"



![Screenshot from 2023-12-13 14-52-44](https://github.com/ekomoku/ekom-pbl/assets/66005935/5caed4c4-6828-4663-b798-ff42f32d5bcf)



5. It is time to start adding some logic to the webserver role. Go into `tasks` directory, and within the `main.yml` file, start writing configuration tasks to do the following:

- Install and configure Apache (`httpd` service)
- Clone **Tooling website** from GitHub `https://github.com/<your-name>/tooling.git`.
- Ensure the tooling website code is deployed to `/var/www/html` on each of 2 UAT Web servers.
- Make sure `httpd` service is started

Your `main.yml` may consist of following tasks:

```
---
- name: install apache
  become: true
  ansible.builtin.yum:
    name: "httpd"
    state: present

- name: install git
  become: true
  ansible.builtin.yum:
    name: "git"
    state: present

- name: clone a repo
  become: true
  ansible.builtin.git:
    repo: https://github.com/<your-name>/tooling.git
    dest: /var/www/html
    force: yes

- name: copy html content to one level up
  become: true
  command: cp -r /var/www/html/html/ /var/www/

- name: Start service httpd, if not started
  become: true
  ansible.builtin.service:
    name: httpd
    state: started

- name: recursively remove /var/www/html/html/ directory
  become: true
  ansible.builtin.file:
    path: /var/www/html/html
    state: absent
```



![Screenshot from 2023-12-13 15-00-19](https://github.com/ekomoku/ekom-pbl/assets/66005935/9b841ca1-af77-4d93-bf5b-a0e3f5d46465)




#### Step 4 - Reference 'Webserver' role 

Within the `static-assignments` folder, create a new assignment for **uat-webservers** `uat-webservers.yml`. This is where you will reference the role.



![Screenshot from 2023-12-13 15-15-41](https://github.com/ekomoku/ekom-pbl/assets/66005935/eeae1da3-00b5-4d6a-b2ea-f802a6e4c5fc)



Update the uat-webservers.yml file


```
---
- hosts: uat-webservers
  roles:
     - webserver
```



![Screenshot from 2023-12-13 15-18-15](https://github.com/ekomoku/ekom-pbl/assets/66005935/034ba861-8df4-49a2-bc0e-2c7830cb51a4)



Remember that the entry point to our ansible configuration is the `site.yml` file. Therefore, you need to refer your `uat-webservers.yml` role inside `site.yml`.

So, we should have this in `site.yml`

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml

- hosts: uat-webservers
- import_playbook: ../static-assignments/uat-webservers.yml

```



![Screenshot from 2023-12-13 15-22-34](https://github.com/ekomoku/ekom-pbl/assets/66005935/51a2bda3-e25e-4524-a026-7c126c4d07db)




#### Step 5 - Commit & Test

Commit your changes, create a Pull Request and merge them to `master` branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your `Jenkins-Ansible` server into `/home/ubuntu/ansible-config-mgt/` directory.



![Screenshot from 2023-12-13 15-28-35](https://github.com/ekomoku/ekom-pbl/assets/66005935/35149568-98aa-45dc-bfc6-ed9ffbd80024)




![Screenshot from 2023-12-13 15-30-05](https://github.com/ekomoku/ekom-pbl/assets/66005935/7fba0d19-d9b6-4092-9d35-9d0c23b98116)



![Screenshot from 2023-12-13 15-31-19](https://github.com/ekomoku/ekom-pbl/assets/66005935/61d2add4-e4e2-4511-9017-415b3eec891c)



![Screenshot from 2023-12-13 15-33-16](https://github.com/ekomoku/ekom-pbl/assets/66005935/6120a060-96c4-4576-af95-397f04f5728c)




Now run the playbook against your `uat` inventory and see what happens:




~~~
$ ansible-playbook ansible-config-artifact/playbooks/site.yml -i ansible-config-artifact/inventory/uat.yml
~~~


![Screenshot from 2023-12-13 23-39-01](https://github.com/ekomoku/ekom-pbl/assets/66005935/ee4048e0-2185-4805-9a33-d7eb2287d4fd)




![Screenshot from 2023-12-13 23-40-33](https://github.com/ekomoku/ekom-pbl/assets/66005935/6d4c9d4c-c8b8-41e3-a326-d3ac88a91838)




```
cd /home/ubuntu/ansible-config-mgt

ansible-playbook -i /inventory/uat.yml playbooks/site.yaml
```

You should be able to see both of your UAT Web servers configured and you can try to reach them from your browser:

`http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php`

or

`http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php`

Your Ansible architecture now looks like this:
<img src="https://darey-io-nonprod-pbl-projects.s3.eu-west-2.amazonaws.com/project12/project12_architecture.png" width="936px" height="550px">

In Project 13, you will see the difference between **Static** and **Dynamic** assignments.

#### Congratulations!

You have learned how to deploy and configure UAT Web Servers using Ansible `imports` and `roles`!

<img src="https://darey-io-nonprod-pbl-projects.s3.eu-west-2.amazonaws.com/project12/good_job12.jpg" width="936px" height="550px">

