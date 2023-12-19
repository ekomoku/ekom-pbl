
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
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

echo "#### Upgrading Repo ####"
sudo yum upgrade -y

echo "#### Installing Java 11 and Jenkins ####"
sudo yum install fontconfig java-17-openjdk
sudo yum install jenkins -y

echo "#### Reload Deamon ####"
sudo systemctl daemon-reload

echo "#### Start and Enable Jenkins ####"
sudo systemctl start jenkins
sudo systemctl enable jenkins
~~~


Note: Always visit jenkins website for offical documentation on guides to install jenkins e.g https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos


![Screenshot from 2023-12-18 10-20-51](https://github.com/ekomoku/ekom-pbl/assets/66005935/eb75cddc-3e1b-4293-aa7d-5ac20caa1e7a)


Then we make the script executable by running the command

$ sudo chmod +x jenkins-install.sh

and run the script

$ ./jenkins-install.sh





![Screenshot from 2023-12-18 14-27-14](https://github.com/ekomoku/ekom-pbl/assets/66005935/80ef39dd-ec9e-48ff-a16f-33471142dede)




![Screenshot from 2023-12-18 14-32-31](https://github.com/ekomoku/ekom-pbl/assets/66005935/0e836fe6-9ec9-490c-bd26-4d38aeaf431f)




![Screenshot from 2023-12-18 14-35-59](https://github.com/ekomoku/ekom-pbl/assets/66005935/ecf96106-c1de-4979-b710-0999437b5b47)



![Screenshot from 2023-12-18 14-40-08](https://github.com/ekomoku/ekom-pbl/assets/66005935/d273d293-e98a-46f8-bff8-9b3c9ee9efe6)




![Screenshot from 2023-12-18 14-48-56](https://github.com/ekomoku/ekom-pbl/assets/66005935/f295fc58-ed49-40d1-8b3c-8231b18ad39d)



![Screenshot from 2023-12-18 14-49-30](https://github.com/ekomoku/ekom-pbl/assets/66005935/aeae41fe-2336-498d-a398-0bdf3fc4b399)



Blue Ocean now shows up on the Dashboard



![Screenshot from 2023-12-18 14-51-16](https://github.com/ekomoku/ekom-pbl/assets/66005935/a8e15253-0d55-446c-8ef5-ab0b67870bb1)




Click on the Blue Ocean to create a new pipeline



![Screenshot from 2023-12-18 15-02-55](https://github.com/ekomoku/ekom-pbl/assets/66005935/4388a41f-7d77-4e7f-9430-46b63a557a54)



Select Github



![Screenshot from 2023-12-18 15-07-15](https://github.com/ekomoku/ekom-pbl/assets/66005935/690a2206-fc38-43a5-83b1-1957926a47f4)




![Screenshot from 2023-12-18 15-08-10](https://github.com/ekomoku/ekom-pbl/assets/66005935/f1b66297-0860-4eb3-94c7-dd15e2935009)




Connect jenkins with Github and Login to GitHub & Generate an Access token




![Screenshot from 2023-12-18 15-10-12](https://github.com/ekomoku/ekom-pbl/assets/66005935/87705285-dd69-401e-a24c-0fc2ad6a7441)




![Screenshot from 2023-12-18 15-11-25](https://github.com/ekomoku/ekom-pbl/assets/66005935/78d30672-6277-48ea-a4ae-cfe2f055b225)




![Screenshot from 2023-12-18 15-13-46](https://github.com/ekomoku/ekom-pbl/assets/66005935/87d8b816-b444-4c3c-847a-67aeba6b7b70)




Generate access token, Copy Access Token and paste in the space provided to connect to github.

Then select the ansible-config-mgt-dybran repository and click on create pipeline.




![Screenshot from 2023-12-18 15-17-22](https://github.com/ekomoku/ekom-pbl/assets/66005935/e3e24769-990a-4b55-9010-c13d1a1a2987)





![Screenshot from 2023-12-18 15-19-23](https://github.com/ekomoku/ekom-pbl/assets/66005935/a4ca3596-fc45-41b3-bf56-a7243dd8d733)




At this point we do not have a Jenkinsfile in the ansible-config-mgt-dybran repository.

Click on "Administration" to exit the Blue Ocean console.

Here is our newly created pipeline. It takes the name of your GitHub repository





![Screenshot from 2023-12-18 15-21-21](https://github.com/ekomoku/ekom-pbl/assets/66005935/11275e22-7a65-49e5-816c-a14e3e220bf1)




Inside the ansible-config-mgt-dybran, create a new directory deploy and start a new Jenkinsfile inside the directory.





![Screenshot from 2023-12-18 15-45-33](https://github.com/ekomoku/ekom-pbl/assets/66005935/7d1849b3-b53a-4854-9ab8-2d3614fb0bd1)




Add the code snippet below to start building the Jenkinsfile gradually. This pipeline currently has just one stage called Build and the only thing we are doing is using the shell script module to echo Building Stage.




~~~
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
    }
}
~~~




![Screenshot from 2023-12-18 17-24-49](https://github.com/ekomoku/ekom-pbl/assets/66005935/04f3e16b-931c-493e-9789-171cc37dc53f)




Go into the ansible-config_mgt in the jenkins UI, and click on "configure" to configure the jenkins.




![Screenshot from 2023-12-18 17-27-40](https://github.com/ekomoku/ekom-pbl/assets/66005935/843774d3-4059-4896-a3bc-d6a5158001bd)




Click on Build Configuration and edit the script path to deploy/Jenkinsfile



Back to the pipeline again and click "Build now"

This will trigger a build and you will be able to see the effect of our basic Jenkinsfile configuration by going through the console output of the build.



![Screenshot from 2023-12-19 15-05-29](https://github.com/ekomoku/ekom-pbl/assets/66005935/ad183ae4-15b7-4258-bc9b-d235769b42e8)





![Screenshot from 2023-12-19 15-06-13](https://github.com/ekomoku/ekom-pbl/assets/66005935/f46c7489-5388-48c6-9517-3ed84c3d0378)




![Screenshot from 2023-12-19 15-09-50](https://github.com/ekomoku/ekom-pbl/assets/66005935/ec01e6bf-3b66-4719-8587-af259fdc5fbb)




Click on Blue Ocean and seen the build



![Screenshot from 2023-12-19 15-10-31](https://github.com/ekomoku/ekom-pbl/assets/66005935/6671546f-593a-404c-82f1-67760650ad5d)



This pipeline has multibranchs. This means, if there were more than one branch in GitHub, Jenkins would have scanned the repository to discover them all and we would have been able to trigger a build for each branch.


To see this in action, we will create a new git branch and name it feature/jenkinspipeline-stages

$ git checkout -b feature/jenkinspipeline-stages


Currently we only have the Build stage. Let us add other stages. Paste the code snippet below.





~~~
   pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}
~~~





![Screenshot from 2023-12-19 15-12-31](https://github.com/ekomoku/ekom-pbl/assets/66005935/f8a20e36-222a-45fd-a6f8-4c8562a65caf)




Push the new changes to GitHub.

$ git status

$ git add .

$ git commit -m "<commit-message>"

$  git push origin feature/jenkinspipeline-stages




![Screenshot from 2023-12-19 15-13-27](https://github.com/ekomoku/ekom-pbl/assets/66005935/4eb917f9-7a32-49db-bf9a-7ca6f48ce2e2)



To make your new branch show up in Jenkins, we need to tell Jenkins to scan the repository.




![Screenshot from 2023-12-19 15-14-15](https://github.com/ekomoku/ekom-pbl/assets/66005935/198e47d2-6059-4ff5-94a3-2f4544eb93ee)





![Screenshot from 2023-12-19 15-14-52](https://github.com/ekomoku/ekom-pbl/assets/66005935/e96ad043-0186-463d-baa8-7db3f029c6f8)




![Screenshot from 2023-12-19 15-15-16](https://github.com/ekomoku/ekom-pbl/assets/66005935/19eeedc4-827e-4ad9-93ca-823050882f0a)




Click on the ansible-config-mgt job and then click on "Administration" button to go back to the jenkins UI.



#### RUNNING ANSIBLE PLAYBOOK FROM JENKINS


Now that we have a broad overview of a typical Jenkins pipeline. Let us get the actual Ansible deployment to work.


We will be setting up database and nginx on two different instances using ansible playbook on jenkins UI.




