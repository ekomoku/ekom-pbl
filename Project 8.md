
## PROJECT 8: LOAD BALANCER SOLUTION WITH APACHE


![Screenshot from 2023-01-22 11-03-23](https://user-images.githubusercontent.com/66005935/213910248-4b6cb8df-301e-450c-b199-fc2edfe91fac.png)


In this project we will enhance our Tooling Website solution by adding a Load Balancer to disctribute traffic between Web Servers and allow users to access our website using a single URL.

Task
Deploy and configure an Apache Load Balancer for Tooling Website solution on a separate Ubuntu EC2 intance. Make sure that users can be served by Web servers through the Load Balancer.

To simplify, I'll implement this solution with 2 Web Servers, the approach will be the same for 3 and more Web Servers.


#### Prerequisites
Make sure that you have following servers installed and configured within Project-7:

1. Two RHEL8 Web Servers
2. One MySQL DB Server (based on Ubuntu 20.04)
3. One RHEL8 NFS server



![Screenshot from 2023-01-22 20-45-47](https://user-images.githubusercontent.com/66005935/213937001-0d0987f7-a80e-407e-9930-49d98565930f.png)



### Configure Apache As A Load Balancer


1. Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb, so your EC2 list will look like this:

2. Open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in Security Group.

3. Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers:



~~~
#Install apache2
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev

#Enable following modules:
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic

#Restart apache2 service
sudo systemctl restart apache2
~~~


Make sure apache2 is up and running


~~~
sudo systemctl restart apache2
sudo systemctl restart apache2
~~~



### Configure load balancing


~~~
sudo vi /etc/apache2/sites-available/000-default.conf

#Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

<Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/

#Restart apache server

sudo systemctl restart apache2
~~~~



![Screenshot from 2023-01-22 21-44-07](https://user-images.githubusercontent.com/66005935/213939466-ad0a8ff2-779e-4e41-98e1-4e3f2abf740e.png)


'bytraffic' balancing method will distribute incoming load between your Web Servers according to current traffic load. We can control in which proportion the traffic must be distributed by loadfactor parameter.


4. Verify that our configuration works – try to access your LB’s public IP address or Public DNS name from your browser:


~~~
http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php
~~~



Note: If in the Project-7 you mounted /var/log/httpd/ from your Web Servers to the NFS server – unmount them and make sure that each Web Server has its own log directory.On each webservers, run the following command to unmount


~~~
sudo umount -f /var/log/httpd
~~~



Open ssh/Putty consoles for all Web Servers and run following command:


~~~
sudo tail -f /var/log/httpd/access_log
~~~


#### Try to refresh your browser page

several times and make sure that both servers receive HTTP GET requests from your LB – new records must appear in each server’s log file. The number of requests to each server will be approximately the same since we set loadfactor to the same value for both servers – it means that traffic will be disctributed evenly between them.


~~~
http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php
~~~

If you have configured everything correctly – your users will not even notice that their requests are served by more than one server.





