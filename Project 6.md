
## Project 6: WEB SOLUTION WITH WORDPRESS

### Step 1: Prepare a Web Server

#### 1. Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB



![Screenshot from 2023-01-10 19-24-37](https://user-images.githubusercontent.com/66005935/211632269-3035648a-1b14-4943-abc7-3800ae7bd5a5.png)



#### 2. Attach all three volumes one by one to your Web Server EC2 instance



#### 3.  Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.


![Screenshot from 2023-01-10 19-30-27](https://user-images.githubusercontent.com/66005935/211633660-bdeaa86b-8c2b-4537-8027-78a5d27e849d.png)

#### 4. Use df -h command to see all mounts and free space on your server


5. Use gdisk utility to create a single partition on each of the 3 disks


~~~
sudo gdisk /dev/xvdf
~~~

> GPT fdisk (gdisk) version 1.0.3

>Partition table scan:
  > MBR: not present
  > BSD: not present
  > APM: not present
  > GPT: not present

> Creating new GPT entries.

> Command (? for help branch segun-edits: p
> Disk /dev/xvdf: 20971520 sectors, 10.0 GiB
> Sector size (logical/physical): 512/512 bytes
> Disk identifier (GUID): D936A35E-CE80-41A1-B87E-54D2044D160B
>Partition table holds up to 128 entries
> Main partition table begins at sector 2 and ends at sector 33
> First usable sector is 34, last usable sector is 20971486
> Partitions will be aligned on 2048-sector boundaries
> Total free space is 2014 sectors (1007.0 KiB)

> Number  Start (sector)    End (sector)  Size       Code  Name
  > 1            2048        20971486   10.0 GiB    8E00  Linux LVM

> Command (? for help): w

> Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
> PARTITIONS!!

> Do you want to proceed? (Y/N): yes
> OK; writing new GUID partition table (GPT) to /dev/xvdf.
> The operation has completed successfully.
> Now,  your changes has been configured succesfuly, exit out of the gdisk console and do the same for the remaining disks.


#### 5. Use lsblk utility to view the newly configured partition on each of the 3 disks.



![Screenshot from 2023-01-10 19-56-14](https://user-images.githubusercontent.com/66005935/211637815-a6b62082-8c60-484a-ba6c-e450616bf9d9.png)



#### 6. Install lvm2 package using sudo yum install lvm2. Run sudo lvmdiskscan command to check for available partitions.


Note: Previously, in Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.


#### 7. Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM



~~~
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
~~~


#### 8. Verify that your Physical volume has been created successfully by running sudo pvs


![Screenshot from 2023-01-10 20-09-20](https://user-images.githubusercontent.com/66005935/211640200-58421150-dbc5-4303-af86-e60a45c98baa.png)


#### 9. Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg



~~~
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
sudo vgs
~~~



#### 10. Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.



~~~
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
~~~


#### 11. Verify that your Logical Volume has been created successfully by running sudo lvs




![Screenshot from 2023-01-10 20-20-19](https://user-images.githubusercontent.com/66005935/211642270-747eb2db-1947-4e77-b288-fbf221bed1f1.png)


#### 12. Verify the entire setup


~~~
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk 
~~~

#### 13. Use mkfs.ext4 to format the logical volumes with ext4 filesystem


~~~
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
~~~



#### 14. Create /var/www/html directory to store website files



~~~
sudo mkdir -p /var/www/html
~~~


#### 15. Create /home/recovery/logs to store backup of log data



~~~
sudo mkdir -p /home/recovery/logs
~~~


#### 16. Mount /var/www/html on apps-lv logical volume


~~~
sudo mount /dev/webdata-vg/apps-lv /var/www/html/
~~~


#### 17. Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)


~~~
sudo rsync -av /var/log/. /home/recovery/logs/
~~~~



#### 18. Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very
important)


~~~
sudo mount /dev/webdata-vg/logs-lv /var/log
~~~



#### 19. Restore log files back into /var/log directory


~~~
sudo rsync -av /home/recovery/logs/. /var/log
~~~


#### 20. Update /etc/fstab file so that the mount configuration will persist after restart of the server.

The UUID of the device will be used to update the /etc/fstab file;




~~~
sudo blkid
~~~


Copy the UUID for the webdata-app --lv and webdata-logs--lv




![Screenshot from 2023-01-10 20-44-35](https://user-images.githubusercontent.com/66005935/211647563-aa95c17f-1842-4659-8f65-0767ff62469e.png)



Run the command


~~~
sudo vi /etc/fstab
~~~



## UPDATE THE `/ETC/FSTAB` FILE

#### Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.


UUUID = 68fb04ff-0e53-479f-ade7-ee0fd0aba01c
UUUID = 27d15c21-82eb-418f-9b0f-f92166bf99c3


#### 1. Test the configuration and reload the daemon



~~~
 sudo mount -a
 sudo systemctl daemon-reload
 ~~~
 
 


2. Verify your setup by running df -h, output must look like this:




![Screenshot from 2023-01-10 21-11-05](https://user-images.githubusercontent.com/66005935/211652117-f3406961-833e-4ffe-b4fe-2e7cdae78da5.png)



## Step 3 — Install WordPress on your Web Server EC2

Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/



Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

## Codes

~~~
sudo vgcreate db-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
sudo lvcreate -n db-lv -L 20G db-vg
sudo mkdir /db
sudo mkfs -t ext4 /dev/db-vg/db-lv
sudo mount /dev/db-vg/db-lv /db
sudo vi /etc/fstab  and copy and paste the UUID like thise >UID=66390432-bce4-4798-880f-3030020921ba /db ext4 defaults 0 0

sudo mount -a
sudo systemctl daemon-reload
~~~



## Step 3:  Install WordPress on your Web Server EC2

1. Update the repository

~~~
sudo yum -y update
~~~



2. Install wget, Apache and it’s dependencies



~~~
sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
~~~



3. Start Apache


~~~
sudo systemctl enable httpd
sudo systemctl start httpd
~~~



4. ### Note: RedHat requires installation of latest version of PHP. You can get from  https://www.tecmint.com/install-lamp-on-centos-8/

Run the following commands 


~~~
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
~~~


5. Restart Apache


~~~
sudo systemctl restart httpd
~~~


6. Download wordpress and copy wordpress to var/www/html


  ~~~
  mkdir wordpress
  cd   wordpress
  sudo wget http://wordpress.org/latest.tar.gz
  sudo tar xzvf latest.tar.gz
  sudo rm -rf latest.tar.gz
  sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
  sudo cp -R wordpress /var/www/html/
  ~~~
  
  
  
  
7. Configure SELinux Policies

  ~~~
  sudo chown -R apache:apache /var/www/html/wordpress
  sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
  sudo setsebool -P httpd_can_network_connect=1
  ~~~
  
  


## Step 4 — Install MySQL on your DB Server EC2


~~~
sudo yum update
sudo yum install mysql-server
~~~



Verify that the service is up and running by using sudo systemctl status mysqld, if it is not running, restart the service and enable it so it will be running even after reboot:



~~~
sudo systemctl restart mysqld
sudo systemctl enable mysqld
~~~


## Step 5 — Configure DB to work with WordPress



~~~
sudo mysql
CREATE DATABASE wordpress;
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
~~~






## Step 6 — Configure WordPress to connect to remote database.

Hint: Do not forget to open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY from your Web Server’s IP address, so in the Inbound Rule configuration specify source as /32
  
![Screenshot from 2023-09-16 20-57-11](https://github.com/ekomoku/ekom-pbl/assets/66005935/58093cdf-e9ea-4429-84c0-ac72fb652fd9)

  
  
1. Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client



~~~
sudo yum install mysql
sudo mysql -u admin -p -h <DB-Server-Private-IP-address>
~~~


On DB server, create database, create security settings, create user with privileges, then continue below in the db server

mysql> sudo vi /etc/my.cnf  #( insert the following codes)
      [mysqld]
      bind-address=0.0.0.0 #(o.o.o.o meanse from any IP appdress. But if you specified any IP address before; in places of '%', please use the IP address                              here instead)
      


Restart the service 


~~~
sudo systemctl restart mysqld
~~~



#### Removing/renaming apache Welcome Page


~~~
sudo mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf_backup
~~~

  
  
  
  
  
![Screenshot from 2023-01-17 01-13-43](https://user-images.githubusercontent.com/66005935/212911026-c5e8cfd8-3a5f-4d5d-94f4-5424c2a626c5.png)







![Screenshot from 2023-01-17 14-15-47](https://user-images.githubusercontent.com/66005935/212911478-6b3ade03-9afe-4346-9c22-41c477af7e83.png)







![Screenshot from 2023-01-17 14-23-08](https://user-images.githubusercontent.com/66005935/212911399-2a80a103-422b-4b3d-bbe6-dbcefa7fbec3.png)















