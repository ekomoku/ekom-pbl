
#### CONFIGURING THE JENKINS SERVER FOR DEPLOYMENT

Provision a RHEL 8 instance on AWS and name it jenkins-ansible


Connect to our vscode through remote SSH:

On the VS code, goto the lower left corner of the window, click 'Open a Remote Window' ( we want to connect to a remote host which is the 'jenkins-ansible' server on AWS) and on the drop down, click "connect to Host ...."

then click " Configure SSH Hosts ...", select /home/ekom/.ssh/config and add the following code to the config file 



~~~
# Read more about SSH config files: https://linux.die.net/man/5/ssh_config
Host Jenkins-Ansible
    HostName 3.85.170.246
    User ubuntu
    IdentityFile /home/ekom/Downloads/devopskeypair.pem
    ForwardAgent yes
    ControlPath /tmp/ansible-ssh-%h-%p-%r
    ControlMaster auto
    ControlPersist 10m
~~~




Edit the "HostName" IP address to the public IP address of your server 

"IdentityFile" to the correct path of your keypair.

"User" = name of the server type ( ubuntu or ec2-user)


Then on VS code, click 'Open a Remote Window' on the lower left corner of the window. select 'connect current window to a host'. At this point, you should see your host listed. Select and connect to it.


To setup our jenkins and clone the ansible-config-mgt repository from our github, I created a file

$ sudo vi jenkins-install.sh



![Screenshot from 2023-12-18 10-22-25](https://github.com/ekomoku/ekom-pbl/assets/66005935/d06ef955-bd94-40a6-afc8-b8f4c4a81bd7)



add the bash script below



~~~
#!/bin/bash

echo "#### Install git ####"
sudo yum install git -y

echo "#### Cloning Git-Repo ####"
git clone https://github.com/ekomoku/ansible-config-mgt-dybran.git

echo "#### Install wget and unzip -y ####"
sudo yum install wget unzip -y

echo "#### Download Repo from URL ####"
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key

echo "#### Upgrading Repo ####"
sudo yum upgrade -y

echo "#### Installing Java 11 and Jenkins ####"
sudo yum install java-11-openjdk -y
sudo yum install jenkins -y

echo "#### Reload Deamon ####"
sudo systemctl daemon-reload

echo "#### Start and Enable Jenkins ####"
sudo systemctl start jenkins
sudo systemctl enable jenkins
~~~



![Screenshot from 2023-12-18 10-20-51](https://github.com/ekomoku/ekom-pbl/assets/66005935/eb75cddc-3e1b-4293-aa7d-5ac20caa1e7a)


Then we make the script executable by running the command

$ sudo chmod +x jenkins-install.sh

and run the script

$ ./jenkins-install.sh













