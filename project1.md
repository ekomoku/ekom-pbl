
### Provisioning Ubuntu Server on AWS

![provisioning ubuntu server](https://user-images.githubusercontent.com/66005935/210137117-c4c3ce07-7836-47d9-a871-cfda5a6d69c2.png)

### Connecting to EC2 instance

![Screenshot from 2022-12-31 13-31-01](https://user-images.githubusercontent.com/66005935/210137145-c8bbb67b-0081-42fd-a87a-bfbdcbc9b34d.png)

### Installing Apache using Ubuntu's package manager 'apt'

~~~
update a list of packages in package manager
sudo apt update

#run apache2 package installation
sudo apt install apache2
~~~


![Screenshot from 2022-12-31 15-14-47](https://user-images.githubusercontent.com/66005935/210139884-a941014d-b655-452d-8c91-1873c998b114.png)


#### To verify apache2 is running 

~~~
sudo systemctl status apache2
~~~


![Screenshot from 2022-12-31 15-23-38](https://user-images.githubusercontent.com/66005935/210139949-d87351c0-8c03-4a54-b3e0-82368b1c8ecf.png)

### Accessing the web server

To test the web server, access it either by 'curl' command or through the browser


~~~
url http://localhost:80
or
 curl http://127.0.0.1:80
~~~


Web
~~~
http://34.203.227.185:80
~~~


![Screenshot from 2022-12-31 15-42-42](https://user-images.githubusercontent.com/66005935/210140532-18b425d7-c916-4f20-80e8-3723a54d7450.png)

## Step 2 - Installing MySQL
Use the command

~~~
$ sudo apt install mysql-server
~~~


![Screenshot from 2022-12-31 16-24-41](https://user-images.githubusercontent.com/66005935/210142639-a0e569b5-2490-444a-ac1f-c46104a956ae.png)

Setting 'root' user password suing mysql_native_password as default authentication method. The password here is PassWord.1


~~~
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
~~~


Remove some insecure default settings and lock down access to the database system

~~~
$ sudo mysql_secure_installation
~~~


### Logon to MySQL

~~~
sudo mysql -p
~~~


![Screenshot from 2022-12-31 16-37-56](https://user-images.githubusercontent.com/66005935/210148573-998d0c96-1aba-4ea9-9f89-dfe18455d965.png)

![Screenshot from 2022-12-31 16-38-45](https://user-images.githubusercontent.com/66005935/210148629-f1fa7373-27dd-463f-9de5-8e195fdbd882.png)



![Screenshot from 2022-12-31 16-43-11](https://user-images.githubusercontent.com/66005935/210148821-4cd13d5e-5489-4ec2-b9d7-16cabe5e1c4c.png)


![Screenshot from 2022-12-31 16-43-47](https://user-images.githubusercontent.com/66005935/210148815-26a6184a-822e-49ef-9ee4-b0e86e19e081.png)



## Step 3: Installing PHP

Run the following commands to install PHP, php-mysql and libapache2-mod-php


~~~
sudo apt install php libapache2-mod-php php-mysql
~~~


![Screenshot from 2022-12-31 17-11-21](https://user-images.githubusercontent.com/66005935/210149317-f1fcbbf2-7983-462a-ab93-27b7a17eddc2.png)


## Step 4: Creating virtual host for websites

Create directory for 'projectnetrill' using the 'mkdir' command
Assign ownership with the current system user


~~~
sudo mkdir /var/www/projectlamp

sudo chown -R $USER:$USER /var/www/projectlamp
~~~


Create and open configuration file in apache's site-availiable directory

~~~
sudo vi /etc/apache2/sites-available/projectlamp.conf
~~~


![Screenshot from 2022-12-31 18-11-49](https://user-images.githubusercontent.com/66005935/210150890-d35f7fe2-dbea-4a59-abe9-eb6156c0313d.png)


![Screenshot from 2022-12-31 21-06-18](https://user-images.githubusercontent.com/66005935/210154681-344eeafd-696e-49be-aa99-556480b5623c.png)

Use the following commands to check

~~~
sudo ls /etc/apache2/sites-available

sudo a2ensite projectlamp

sudo a2dissite 000-default

sudo apache2ctl configtest

sudo systemctl reload apache2
~~~



## Enable PHP on the website

Run the follwoing command to change the order and behaviour of PHP

~~~sudo vim /etc/apache2/mods-enabled/dir.conf
~~~

Edit the dir.conf file with the following


~~~
<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
~~~


Create a new file named index.php inside your custom web root folder:
~~~
vim /var/www/projectlamp/index.php

~~~


Add the following valid PHP code


~~~
<?php
phpinfo();

~~~




![Screenshot from 2022-12-31 21-36-22](https://user-images.githubusercontent.com/66005935/210155279-3d816e13-05f6-4a3c-b827-65cd70fd56a6.png)







