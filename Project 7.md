
## Project 7: DEVOPS TOOLING WEBSITE SOLUTION

#### Setup and technologies used in Project 7

As a member of a DevOps team, you will implement a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible.

In this project you will implement a solution that consists of following components:

1. Infrastructure: AWS
2. Webserver Linux: Red Hat Enterprise Linux 8
3. Database Server: Ubuntu 20.04 + MySQL
4. Storage Server: Red Hat Enterprise Linux 8 + NFS Server
5. Programming Language: PHP
6. Code Repository: GitHub



On the diagram below you can see a common pattern where several stateless Web Servers share a common database and also access the same files using Network File Sytem (NFS) as a shared file storage. Even though the NFS server might be located on a completely separate hardware – for Web Servers it look like a local file system from where they can serve the same files.





![Screenshot from 2023-01-18 12-45-09](https://user-images.githubusercontent.com/66005935/213163418-2becf969-57eb-4015-bcd7-a3bb73837b1e.png)



### Step 1 – Prepare NFS Server

1. Spin up an EC2 instance with RHEL Linux 8 Operating System. Create and attach three EBS volumes (10G each)


2. Configure LVM on the server
    1. a. Create partitions on each of the volumes/disk with 'gdisk' command, e.g sudo gdisk /dev/xvdf /dev/xvdg /dev/xvdg
    2. b. Install lvm package using the command 
    
    ~~~
    sudo yum install lvm2 -y
    ~~~
    
    
    3. c. Check for available partitions

    ~~~
    lvdiskscan
    ~~~
    
    #### Create physical volumes in xvdf1, xvdg1 and xvdg1 using pvcreate commands
    
    
    ~~~
    sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1
    ~~~
    
    
    #### Create volume group for the physical volumes created above, name it as webdata-vg
    
    
    ~~~
    sudo vgcreate webdata-vg /dev/xvdf1 /dev/xvdg1 /dev/xvdh1
    ~~~
    
    
#### 3.Create 3 Logical volumes lv,name them as: lv-opt, lv-apps, and lv-logs



    ~~~
    sudo lvcreate -n lv-opt -L 9G webdata-vg
    sudo lvcreate -n lv-apps -L 9G webdata-vg
    sudo lvcreate -n lv-logs -L 9G webdata-vg
    ~~~
    
    
    
    #### Format the disks as xfs instead of ext4
    
    
    
    ~~~
    sudo mkfs -t xfs /dev/webdata-vg/lv-apps
    sudo mkfs -t xfs /dev/webdata-vg/lv-logs
    sudo mkfs -t xfs /dev/webdata-vg/lv-opt
    ~~~
    
    
    
    #### Create mount points on /mnt directory for the logical volumes as follow:
    Mount lv-apps on /mnt/apps – To be used by webservers
    Mount lv-logs on /mnt/logs – To be used by webserver logs
    Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8
    
    
    
    ~~~
    sudo mkdir /mnt/apps
    sudo mkdir /mnt/logs
    sudo mkdir /mnt/opt
    ~~~
    
    
    
    #### Mount the logical volumes on the newly created mount points
    
    
    
    ~~~
    sudo mount /dev/webdata-vg/lv-apps /mnt/apps
    sudo mount /dev/webdata-vg/lv-logs /mnt/logs
    sudo mount /dev/webdata-vg/lv-opt /mnt/opt
    ~~~
    
    
    
    
    
#### 4. Install NFS server, configure it to start on reboot and make sure it is u and running
    
    
    ~~~
    sudo yum -y update
    sudo yum install nfs-utils -y
    sudo systemctl start nfs-server.service
    sudo systemctl enable nfs-server.service
    sudo systemctl status nfs-server.service
    ~~~
    
    
    
#### 5. Export the mounts for webservers’ subnet cidr to connect as clients. For simplicity, you will install your all three Web Servers inside the same subnet, but in production set up you would probably want to separate each tier inside its own subnet for higher level of security.
To check your subnet cidr – open your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link:  


Make sure we set up permission that will allow our Web servers to read, write and execute files on NFS:


On the NFS Server, run the following commands;



~~~
sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt

sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt

sudo systemctl restart nfs-server.service
~~~



##### Configure access to NFS for clients within the same subnet 

##### NOTE: the subnet-CIDR in the code below is that of the web servers, so configure the following on the NFS server



~~~
sudo vi /etc/exports

/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)

Esc + :wq!

sudo exportfs -arv
~~~



#### 6. Check which port is used by NFS and open it using Security Groups (add new Inbound Rule)


~~~
rpcinfo -p | grep nfs
~~~


##### Important note: In order for NFS server to be accessible from your client, you must also open following ports on NFS: TCP 111, UDP 111, UDP 2049, NFS 2049. The source IP is the subnet CIDR of the webservers








## STEP 2 — CONFIGURE THE DATABASE SERVER


#### 1. Install mysql

~~~
sudo apt update
sudo apt install mysql-server -y
~~~


2. Create a database and name it tooling.  



~~~
sudo mysql
>CREATE DATABASE tooling;
~~~

3. Create a database user and name it webaccess. Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidr


~~~
>create user 'webaccess'@'webservers-subnet-CIDR' identified by 'password';
>grant all privileges on tooling.* to 'webaccess'@'webservers-subnet-CIDR';
>flush privileges;
~~~



## Step 2: Prepare the Web Servers


We need to make sure that our Web Servers can serve the same content from shared storage solutions, in our case – NFS Server and MySQL database.
You already know that one DB can be accessed for reads and writes by multiple clients. For storing shared files that our Web Servers will use – we will utilize NFS and mount previously created Logical Volume lv-apps to the folder where Apache stores files to be served to the users (/var/www).

This approach will make our Web Servers stateless, which means we will be able to add new ones or remove them whenever we need, and the integrity of the data (in the database and on NFS) will be preserved.

#### During the next steps we will do following:

1. Configure NFS client (this step must be done on all three servers)
2. Deploy a Tooling application to our Web Servers into a shared NFS folder
3. Configure the Web Servers to work with a single MySQL database


1. Launch a new EC2 instance with RHEL 8 Operating System


2. Install NFS client


~~~
sudo yum install nfs-utils nfs4-acl-tools -y
~~~


3. Mount /var/www/ and target the NFS server’s export for apps


~~~
sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
~~~


4. Verify that NFS was mounted successfully by running df -h. Make sure that the changes will persist on Web Server after reboot:


~~~
sudo vi /etc/fstab
~~~



add following line

~~~
<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
~~~



5.Install Remi’s repository, Apache and PHP


~~~
sudo yum install httpd -y

sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

sudo dnf module reset php

sudo dnf module enable php:remi-8.0.....change the version to the latest from https://www.tecmint.com/install-lamp-on-centos-8/

sudo dnf install php php-opcache php-gd php-curl php-mysqlnd

sudo systemctl start php-fpm

sudo systemctl enable php-fpm

setsebool -P httpd_execmem 1
~~~



#### Repeat steps 1-5 for another 2 Web Servers.


6. Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. If you see the same files – it means NFS is mounted correctly. You can try to create a new file touch test.txt from one server and check if the same file is accessible from other Web Servers.


On the web server, create a test file in the /var/www directory as below;


~~~
sudo touch /var/www/test.txt
ls /var/www
~~~


7. Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs. Repeat step №4 to make sure the mount point will persist after reboot.


The Log folder for Apache on the webserver is located on /var/www/httpd


~~~
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/logs /var/log/httpd
~~~


~~~
sudo vi /etc/fstab
~~~

add the following


~~~
<NFS-Server-Private-IP-Address>:/mnt/logs /var/log/httpd nfs defaults 0 0
~~~


8. Fork the tooling source code from Darey.io Github Account ( https://github.com/darey-io/tooling.git) to your Github account.

To do that, first install git on the web server



~~~
sudo yum install git -y
git init
git clone <paste_the_link_to_darey_github_account_here>
~~~


1. Run ls -l to see the tooling app folder
2. cd into the tooling folder


9. Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

In the toolong folder, run the command below


~~~
sudo cp -R html/. /var/www/html
~~~


##### Note 1: 
Do not forget to open TCP port 80 on the Web Server.

##### Note 2: 
If you encounter 403 Error – check permissions to your /var/www/html folder and also disable SELinux sudo setenforce 0
To make this change permanent – open following config file sudo vi /etc/sysconfig/selinux and set SELINUX=disabledthen restrt httpd.

from the tooling follder, run 


~~~
cd ..
sudo setenforce 0
sudo vi /etc/sysconfig/selinux
~~~

change SELINUX=enforcing ..... to .... SELINUX=disabled

Then start apache, if its not running


~~~
sudo systemctl start httpd
sudo systemctl enable http
~~~




10. Update the website’s configuration to connect to the database (in /var/www/html/functions.php file). Apply tooling-db.sql script to your database using this command mysql -h <databse-private-ip> -u <db-username> -p <db-pasword> < tooling-db.sql


~~~
sudo vi /var/www/html/functions.php
~~~
check under
//connect to database ..... and edit as 
$db=mysqli.connect(<'db-private-ip-address'>, 'webaccess', 'password', 'toolong');

       
##### Note: webaccess is the db user, 'password' is the pssword that was set  
    
    
#### To apply tooling-db.sql run the following command
1. First, cd into the tooling folder   
2. Also ensure mysql is install, if not install it with ~~~sudo yum install mysql~~~
~~~
mysql -h <databse-private-ip> -u <db-username> -p <db-pasword> < tooling-db.sql
~~~~

=======================================================================================                                                                               
### Note: Before continuing, go back to the DB server and edit the conf.d file as follows
                                                                               
                                                                               
~~~
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
~~~
                                                          
      
change the bind addresses from 127.0.0.1 to 0.0.0.0
                                                                               
 Restart mysql
                                                                               
 ~~~
 sudo systemctl restart mysql
 ~~~                                                                              
 
                                                                               
 #### Also, on the Database server on AWS, open TCP port 3306 with Type: Mysql/Aurora
 =======================================================================================   
    
    
    
   
    
    
![Screenshot from 2023-01-21 15-11-46](https://user-images.githubusercontent.com/66005935/213870754-5a08de29-1af2-4693-b713-fcc9de58bc58.png)






