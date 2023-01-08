
## Project 5: IMPLEMENT A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS)

1. Create and configure two Linux-based virtual servers (EC2 instances in AWS).

Server A name - `mysql server`
Server B name - `mysql client`

#### 2. On mysql server Linux Server install MySQL Server software.
#### 3. On mysql client Linux Server install MySQL Client software.
#### 4. By default, both of your EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using local IP addresses. Use mysql server's local IP address to connect from mysql client. MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups. For extra security, do not allow all IP addresses to reach your ‘mysql server’ – allow access only to the specific local IP address of your ‘mysql client’.

#### Run the security script on mysql server

~~~
sudo mysql_secure_installation
~~~

Then create a user


~~~
CREATE USER 'remote_user'@'%' IDENTIFIED WIT msql_native_password BY 'Password_1139';
~~~

Next, create Database


~~~
CREATE DATABASE Project5_db;
~~~

Grant all privileges to the user


~~~
GRANT ALL PRIVILEGES ON * . * TO 'remote_user'@'%' WIT GRANT OPTION;
FLUSH PRIVILEGES;
~~~

#### 5. You might need to configure MySQL server to allow connections from remote hosts.


~~~
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
~~~

Replace ‘127.0.0.1’ with ‘0.0.0.0’ 

Restart the service


~~~
sudo systemctl restart mysql
~~~


#### 6. From mysql client Linux Server connect remotely to mysql server Database Engine without using SSH. You must use the mysql utility to perform this action.



From the mysql client terminal, run the following command to connect using mysql server's private IP ( 172.31.88.40)


~~~
sudo mysql -u remote_user -h 172.31.88.40 -p
~~~





![Screenshot from 2023-01-08 17-03-28](https://user-images.githubusercontent.com/66005935/211208587-3bd6b167-b06f-4b4c-bdb6-871af1376a37.png)














