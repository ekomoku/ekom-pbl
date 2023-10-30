
## PROJECT 10: LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS

In this project we will register our website with LetsEnrcypt Certificate Authority, to automate certificate issuance we will use a shell client recommended by LetsEncrypt – cetrbot.

#### Task
This project consists of two parts:

1. Configure Nginx as a Load Balancer
2. Register a new domain name and configure secured connection using SSL/TLS certificates

Our target architecture will look like this:


![Screenshot from 2023-02-01 21-16-33](https://user-images.githubusercontent.com/66005935/216153868-91f42be1-1441-43f4-840b-98f8f9488e3e.png)


By now we have learned what Load Balancing is used for and have configured an LB solution using Apache, but a DevOps engineer must be a versatile professional and know different alternative solutions for the same problem. That is why, in this project we will configure an Nginx Load Balancer solution.

It is also extremely important to ensure that connections to our Web solutions are secure and information is encrypted in transit – we will also cover connection over secured HTTP (HTTPS protocol), its purpose and what is required to implement it.

When data is moving between a client (browser) and a Web Server over the Internet – it passes through multiple network devices and, if the data is not encrypted, it can be relatively easy intercepted by someone who has access to the intermediate equipment. This kind of information security threat is called Man-In-The-Middle (MIMT) attack.

This threat is real – users that share sensitive information (bank details, social media access credentials, etc.) via non-secured channels, risk their data to be compromised and used by cybercriminals.

SSL and its newer version, TSL – is a security technology that protects connection from MITM attacks by creating an encrypted session between browser and Web server. Here we will refer this family of cryptographic protocols as SSL/TLS – even though SSL was replaced by TLS, the term is still being widely used.

SSL/TLS uses digital certificates to identify and validate a Website. A browser reads the certificate issued by a Certificate Authority (CA) to make sure that the website is registered in the CA so it can be trusted to establish a secured connection.

There are different types of SSL/TLS certificates.


### CONFIGURE NGINX AS A LOAD BALANCER

We can either uninstall Apache from the existing Load Balancer server, or create a fresh installation of Linux for Nginx.

1. Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections)
2. Register a new domain name and configure secured connection using SSL/TLS certificates


![Screenshot from 2023-10-29 23-27-08](https://github.com/ekomoku/ekom-pbl/assets/66005935/f769c9d1-e590-4f00-be8c-3169047c7cf1)


To create a domain, I used freehostia.com to create free domain. After creating domain, Goto Route 53 in AWS and create Hosted Zones in public domain using the domain you created earlier on Freehostia.com


![Screenshot from 2023-10-29 23-35-32](https://github.com/ekomoku/ekom-pbl/assets/66005935/86b25af1-4c00-40c5-b911-fafc3a0ae855)


On freehostia.com, goto 'My Domains' > DNS Records, click on the domain you registered, you'll see the dropdown.

Copy the DNS/NS server address on AWS and edit the one on frehostia.com



![Screenshot from 2023-10-29 23-47-14](https://github.com/ekomoku/ekom-pbl/assets/66005935/681e05b7-edda-429d-8acd-86fb081474fa)



![Screenshot from 2023-10-29 23-57-26](https://github.com/ekomoku/ekom-pbl/assets/66005935/fb067a74-6b7c-4548-a9df-1d04ea89b5ab)


Goto > AWS > Route 53> Hosted Zones, click on the Hosted Zone Name you eralier created, click on 'create record'


![Screenshot from 2023-10-30 00-07-58](https://github.com/ekomoku/ekom-pbl/assets/66005935/a3bc940e-0267-4204-b56f-4419f44070c3)


Copy the public IPv4 IP or elestic IP of the NGINX LB and paste in Route 53 record in the 'value' field. then click 'create record' leaving other fields as default


![Screenshot from 2023-10-30 00-13-54](https://github.com/ekomoku/ekom-pbl/assets/66005935/67f38193-61a5-42e8-ae35-131dc6339c0d)


On AWS, create another record for 'www' with the same public IP or elastic addres of the Nginx LB


Your AWS Route 53 looks like this;


![Screenshot from 2023-10-30 00-18-40](https://github.com/ekomoku/ekom-pbl/assets/66005935/22deb856-2274-466a-8ce5-09aa5526dc5a)

Now, connect to your Nginx LB server, update and install nginx


~~~
sudo apt update
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx
~~~

Create configuration for reverse proxy settings

On Nginx LB, update the /etc/hosts file with local IP addresses of the webservers in project 7

~~~
sudo vi /etc/hosts
~~~


![Screenshot from 2023-10-30 10-30-30](https://github.com/ekomoku/ekom-pbl/assets/66005935/751e612e-155e-4655-a762-cd2c1247d6f3)


Open the default nginx configuration file and insert the following lines of code, change the server names to suit your case and the domain

~~~
sudo vi /etc/nginx/nginx.conf
~~~


~~~
#insert following configuration into http section

 upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
  }

server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }

#comment out this line
#       include /etc/nginx/sites-enabled/*;
~~~


![Screenshot from 2023-10-30 11-05-43](https://github.com/ekomoku/ekom-pbl/assets/66005935/0e82583f-0b96-4ef4-b211-5c0e03052eaf)
