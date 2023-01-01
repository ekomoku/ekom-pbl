## Project 2: LEMP Stack Implementation

### Step: Installing the NGINX Web Server
Use the following commands

~~~
sudo apt update
sudo apt install nginx
~~~


![Screenshot from 2023-01-01 14-36-28](https://user-images.githubusercontent.com/66005935/210172525-2e45cdd3-9863-4c3b-b17d-8de0c28d2b6b.png)

Testing to see that nginx is installed

~~~
http://34.201.58.87:80
~~~


![Screenshot from 2023-01-01 14-42-15](https://user-images.githubusercontent.com/66005935/210172708-b3f28a2b-575d-45b5-8e80-b7eb8e6acc0c.png)


### Step 2: Installing MySQL

~~~
sudo apt install mysql-server
~~~

Login into MySQL

![Screenshot from 2023-01-01 14-45-46](https://user-images.githubusercontent.com/66005935/210172806-4ccae109-5044-4ae6-858e-7c7456e297ca.png)


Security Settings

Set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1, and run the interactive script

~~~
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
mysql> exit
sudo mysql_secure_installation
~~~

### Step 3: Installing PHP

Run the following command toinstall php-fpm and php-mysql

~~~
sudo apt install php-fpm php-mysql
~~~

Create the root web directory for your_domain as follows: use 'projectLEMP' as domain, and assign ownership of the directory with the $USER environment variable, which will reference your current system user:


~~~
sudo mkdir /var/www/projectLEMP
sudo chown -R $USER:$USER /var/www/projectLEMP
~~~


Open a new configuration file in Nginx’s sites-available directory using nano


~~~
sudo nano /etc/nginx/sites-available/projectLEMP
~~~



![Screenshot from 2023-01-01 16-00-55](https://user-images.githubusercontent.com/66005935/210175293-88157f72-7d2d-4805-8959-1548a60d0100.png)


Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:


~~~
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
~~~




![Screenshot from 2023-01-01 16-00-55](https://user-images.githubusercontent.com/66005935/210175468-c9112f6a-abe7-40f2-a2a6-824870bb3dda.png)





![Screenshot from 2023-01-01 16-08-02](https://user-images.githubusercontent.com/66005935/210175489-ee77aa48-223c-4b2f-9914-40f32d7dad03.png)



Disable default nginx host that is currently configured to listen on port 80

~~~
sudo unlink /etc/nginx/sites-enabled/default
~~~


Create an index.html file in the web root /var/www/projectLEMP so that we can test that our new server block works as expected


~~~
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
~~~




![Screenshot from 2023-01-01 16-17-36](https://user-images.githubusercontent.com/66005935/210175738-f533f3b4-1fee-427c-8141-8db8cbb9cb55.png)


### Step 5: Testing PHP with NGINX
~~~
sudo nano /var/www/projectLEMP/info.php
~~~

Paste PHP info page command

~~~
<?php
phpinfo();
~~~


Accessing the web page by adding /info.php



![Screenshot from 2023-01-01 16-38-51](https://user-images.githubusercontent.com/66005935/210176531-5405ab05-05af-4e0a-9475-ada411aae26b.png)



### Step 6: Retrieving data from MySQL database with PHP

To create a database with name 'netrill'


~~~
mysql> CREATE DATABASE `netrill`;
~~~


Create a database user(admin) and grant him full privileges on the database you have just created, using mysql_native_password as default authentication, set the password as 'Wordpass.1'
Also, grant permission tothe 'netrill' database

~~~
CREATE USER 'admin'@'%' IDENTIFIED WITH mysql_native_password BY 'Wordpass.1'
GRANT ALL ON example_database.* TO 'example_user'@'%';
~~~

To show the new user can log into the netrill database

~~~
mysql -u example_user -p
~~~



![Screenshot from 2023-01-01 19-37-32](https://user-images.githubusercontent.com/66005935/210181417-61dceb26-339b-4bc7-8a8e-c6581ced190a.png)


Create a test table named 'todo_list'


~~~
CREATE TABLE netrill.todo_list ( item_id INT AUTO_INCREMENT,content VARCHAR(255),
PRIMARY KEY(item_id)
);
~~~


Insert rows and content, repeating the command below to add more contents


~~~
mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
~~~




![Screenshot from 2023-01-01 20-11-48](https://user-images.githubusercontent.com/66005935/210183518-3162817c-086d-427b-8727-bff60aaf3e87.png)



Create a PHP script that will connect to MySQL and query for your content.

~~~
nano /var/www/projectLEMP/todo_list.php
~~~



![Screenshot from 2023-01-01 22-45-43](https://user-images.githubusercontent.com/66005935/210185514-ef6f104f-8743-496e-913f-83219aea2b5e.png)









![Screenshot from 2023-01-01 22-44-35](https://user-images.githubusercontent.com/66005935/210185525-6c3cdd0b-55fd-4079-b570-092fc525b7e9.png)


















