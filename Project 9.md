
## Project 9: Continous Integration Pipeline For Tooling Website

#### TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS

#### Step 1 – Install Jenkins server

1. Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

2. Install JDK (since Jenkins is a Java-based application)


~~~
sudo apt update
sudo apt install default-jdk-headless
~~~

3. Install Jenkins


~~~
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
~~~


~~~
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'

sudo apt update

sudo apt install jenkins
~~~


If you encounter error e.g NO PUBKEY,  run the following commands

~~~
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys <PUBKEY>
~~~

where <PUBKEY> is your missing public key for repository, e.g. 8BAF9A6F


Then update

~~~
sudo apt-get update
~~~


Make sure Jenkins is running


~~~
sudo systemctl status jenkins
~~~



4. By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

5. Perform initial Jenkins setup.

From your browser access http:// < Jenkins-Server-Public-IP-Address-or-Public-DNS-Name > :8080

You will be prompted to provide a default admin password
  
Retrieve it from your server:
  

 
~~~
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
~~~
  
  
Then you will be asked which plugings to install – choose suggested plugins.

Once plugins installation is done – create an admin user and you will get your Jenkins server address.

The installation is completed!
  
    
    
  
![Screenshot from 2023-01-23 18-49-56](https://user-images.githubusercontent.com/66005935/214112862-bfde4be9-8e88-4006-9c81-c61cf63eff53.png)
    
    

    
### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks
    
    
    
In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.
    
    
#### 1. Enable webhooks in your GitHub repository settings (see picture below). GOTO: Settings>Webhook

    
#### Note: payload url is  :jenkins server's public address:8080/github-webhook/ 
    
    
![Screenshot from 2023-01-23 19-02-27](https://user-images.githubusercontent.com/66005935/214116050-1b543c12-1031-480f-a648-d3038845d2d4.png)
    
    
### 2. Go to Jenkins web console, click "New Item" and create a "Freestyle project" 
    
    
    
![Screenshot from 2023-01-23 19-15-58](https://user-images.githubusercontent.com/66005935/214118173-f5366d7c-c8e7-4427-9d24-de08972fcb79.png)

To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself   
 
On Jenkins, do the following;
 1. After creating freestyle project, open the project, Goto> configure> under source code, select Git> paste the URL of the repo from Git in the space provided. If you encounter any error(e.g 403 no valid crum.....), Goto Jenkins Dashboard> manage jenkins> security> under CSRF Protection, check the box for" Enable Proxy Compatibility"> save.
 
    
    
##### Save the configuration and let us try to run the build. For now we can only do it manually.
Click "Build Now" button, if you have configured everything correctly, the build will be successfull and you will see it under #1
    
  

 ![Screenshot from 2023-01-25 20-16-11](https://user-images.githubusercontent.com/66005935/214664874-2788bc31-13da-4b7c-b82c-6f59eaaff66e.png)

    
    
    
 You can open the build and check in "Console Output" if it has run successfully.

If so – congratulations! You have just made your very first Jenkins build!

But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.
 
    
    
### 3. Click "Configure" your job/project and add these two configurations
Configure triggering the job from GitHub webhook:
    
Under your project>Goto Configure>Build Triggers>Check the box "GitHub hook trigger for GITScm polling"   
    
  
 
    
![Screenshot from 2023-01-25 21-25-24](https://user-images.githubusercontent.com/66005935/214683549-156abe75-dad9-4715-8297-4563a683b1ef.png)
    
    
    
#### Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".
    
Uder Build Triggers, Goto> Post-build Actions, click the dropdown and select "Archive the artifcts". In 'Files to archive' field enter ** and click save

    
Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.    
    

    
    
![Screenshot from 2023-01-25 21-51-36](https://user-images.githubusercontent.com/66005935/214689030-9056ad9e-7748-466d-9545-3f95e9f6f139.png)

    
    
 We have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/   
    
    
    
    
### Step 3 – Configure Jenkins to copy files to NFS server via SSH    
    
  
    
Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.
We will need a plugin that is called "Publish Over SSH".
    
    
#### 1. Install "Publish Over SSH" plugin.

    
On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.

On "Available" tab search for "Publish Over SSH" plugin and install it
    
 
    
#### Configure the job/project to copy artifacts over to NFS server.
On main dashboard select "Manage Jenkins" and choose "Configure System" menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:
    
    
1. Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
    
![Screenshot from 2023-02-21 22-07-38](https://user-images.githubusercontent.com/66005935/220459007-003c85a3-c99a-4420-98b5-27461383ff92.png)
    

![Screenshot from 2023-02-21 22-07-38](https://user-images.githubusercontent.com/66005935/220459110-c154f294-9659-42f6-be29-1dcb6b45cec1.png)


2. Arbitrary name
3. Hostname – can be private IP address of your NFS server
4. Username – ec2-user (since NFS server is based on EC2 with RHEL 8)
5. Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server
    
![Screenshot from 2023-02-21 22-14-24](https://user-images.githubusercontent.com/66005935/220460100-9bca2cf8-a161-4e9f-9a07-da50b539f208.png)

    
Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.    
       
    
    
![Screenshot from 2023-02-01 19-13-13](https://user-images.githubusercontent.com/66005935/216133501-40400f54-f90f-458a-85c4-a3bf536ccad6.png)

    
    
 GoTo> Post-build Actions>click the dropdown and select "Archive the artifacts". Under "Files to archive, enter ** and click save
   
    
 ![Screenshot from 2023-02-01 19-57-47](https://user-images.githubusercontent.com/66005935/216137853-7cfe2b2a-b931-4969-9b8a-239c197ab9eb.png)
   

![Screenshot from 2023-02-01 19-38-17](https://user-images.githubusercontent.com/66005935/216135634-15c28c70-d57c-4323-a8d2-9bbc208c1fba.png)      
    
    
Save the configuration, open your Jenkins job/project configuration page and add another one "Post-build Action" ( Open your project>Configure> Post Build Actions- click the dropdown> Select "Send build artifacts over SSH". Uder Source files, enter ** and click save.  
    

    
    

![Screenshot from 2023-02-01 20-03-20](https://user-images.githubusercontent.com/66005935/216139079-18b65efe-76d1-4e5e-b83c-bdf5ca6d6ffb.png)
    
    
    
  
    
Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository.

Webhook will trigger a new job and in the "Console Output" of the job you will find something like this:

SSH: Transferred 25 file(s)
Finished: SUCCESS
To make sure that the files in /mnt/apps have been udated – connect via SSH/Putty to your NFS server and check README.MD file

~~~
cat /mnt/apps/README.md
~~~
    

    
    
#### Note:if yuo have error like the one below
    
    
![Screenshot from 2023-02-21 22-40-42](https://user-images.githubusercontent.com/66005935/220464843-cc50b0fb-8b92-4a4b-a7df-e644ead267d7.png)
   

 It has to do with permission issue on the NFS server (no permission on the directory /mnt/apps/).
    
    
 To fix this run the following commands on the NFS server
    
    
~~~
sudo chown -R nobody: /mnt/apps
sudo chmod -R 777 /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chmod -R 777 /mnt/logs    
sudo chown -R nobody: /mnt/opt
sudo chmod -R 777 /mnt/opt
~~~



![Screenshot from 2023-10-12 22-04-07](https://github.com/ekomoku/ekom-pbl/assets/66005935/cf06b954-b38e-4bf1-9431-3d8d9ed24886)

