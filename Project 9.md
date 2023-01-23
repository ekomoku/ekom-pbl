
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
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
~~~


Make sure Jenkins is running


~~~
sudo systemctl status Jenkins
~~~


4. By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

5. Perform initial Jenkins setup.

From your browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

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
    #### Note: payload url is the jenkins server's public address
    
    
![Screenshot from 2023-01-23 19-02-27](https://user-images.githubusercontent.com/66005935/214116050-1b543c12-1031-480f-a648-d3038845d2d4.png)
    
    
 
    
    
  
  
