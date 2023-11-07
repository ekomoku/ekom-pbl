## Automate Infrastructure With IaC using Terraform. Part 2

Before we go deeper into automating other parts of our infrastructure on AWS, it is very important to fully understand certain concepts around Networking (in case this is completely new area to you). Networking is a very broad topic and some of internals of Terraform modules related to Networking, like cidrsubnet(), may still not be fully clear to you.

To fully clear your understanding, we highly recommend you watching Networking videos by Eli the Computer Guy on YouTube, and in addition to that, read following golden articles on Networking Terminology, Interfaces, Protocols, IP Address, Subnets, and CIDR Notation by Justin Ellingwood from Digital Ocean.


If you are interested to dive deeper into Networking domain, you can watch the entire playlist here ( https://www.youtube.com/playlist?list=PLF360ED1082F6F2A5).


![Screenshot from 2023-10-11 19-47-11](https://github.com/ekomoku/ekom-pbl/assets/66005935/1e91bc4e-36f8-4f6d-8638-f1092846371d)


![Screenshot from 2023-10-11 19-48-00](https://github.com/ekomoku/ekom-pbl/assets/66005935/e2060231-c471-432e-99a3-287c39bfdb92)


![Screenshot from 2023-10-11 19-48-38](https://github.com/ekomoku/ekom-pbl/assets/66005935/6764692c-1c80-466e-a5ec-032a89b61c31)


Now use similar approach to create the NAT Gateways in a new file called natgateway.tf.

Note: We need to create an Elastic IP for the NAT Gateway, and you can see the use of depends_on to indicate that the Internet Gateway resource must be available before this should be created. Although Terraform does a good job to manage dependencies, but in some cases, it is good to be explicit.

You can read more on dependencies here ( https://developer.hashicorp.com/terraform/language/meta-arguments/depends_on )


![Screenshot from 2023-10-11 19-50-32](https://github.com/ekomoku/ekom-pbl/assets/66005935/580bc8b8-0fd8-465d-adb3-7348d50f39a8)


![Screenshot from 2023-10-11 19-52-09](https://github.com/ekomoku/ekom-pbl/assets/66005935/c7bde393-78e5-46f7-a3a3-71c2c5da4dd3)


![Screenshot from 2023-10-11 19-52-47](https://github.com/ekomoku/ekom-pbl/assets/66005935/ab9539e4-b242-4a4b-872a-5937ea35b3a3)


##### AWS Identity and Access Management

##### IaM and Roles


We want to pass an IAM role our EC2 instances to give them access to some specific resources, so we need to do the following:

1. Create AssumeRole ( https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html)

![Screenshot from 2023-10-11 19-58-37](https://github.com/ekomoku/ekom-pbl/assets/66005935/4b1e7882-4b8e-426f-bd73-2979b183dfd8)


2. Create IAM policy ( https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html)  for this role 


![Screenshot from 2023-10-11 20-03-47](https://github.com/ekomoku/ekom-pbl/assets/66005935/c7e71cdd-54b6-447c-9ecb-cb6c39d07dbe)


4. Create an Instance Profile ( https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html) and interpolate the IAM Role


![Screenshot from 2023-10-11 20-06-05](https://github.com/ekomoku/ekom-pbl/assets/66005935/1814c1cf-14db-48d1-afe3-e9cef4385e24)


### Create Security Groups

We are going to create all the security groups in a single file, then we are going to refrence this security group within each resources that needs it.

IMPORTANT:

    Check out the terraform documentation for security group ( https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group)

    Check out the terraform documentation for security group rule ( https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group_rule) 


![Screenshot from 2023-10-11 20-20-27](https://github.com/ekomoku/ekom-pbl/assets/66005935/51925ab4-baeb-4889-97f3-458434115300)


![Screenshot from 2023-10-11 20-22-22](https://github.com/ekomoku/ekom-pbl/assets/66005935/a775ad34-6b52-4e1b-a939-53da65cb659a)


![Screenshot from 2023-10-11 20-23-25](https://github.com/ekomoku/ekom-pbl/assets/66005935/94c4da48-dbc3-44b3-902b-658578d9a3b2)


![Screenshot from 2023-10-11 20-24-07](https://github.com/ekomoku/ekom-pbl/assets/66005935/67e90137-91a4-4714-a9eb-b30cdc0757da)


![Screenshot from 2023-10-11 20-24-55](https://github.com/ekomoku/ekom-pbl/assets/66005935/a4ca6669-0942-403f-bff6-109e8a41fc4b)


![Screenshot from 2023-10-11 20-26-00](https://github.com/ekomoku/ekom-pbl/assets/66005935/35e7d705-d505-4ace-8257-5e67866147eb)


![Screenshot from 2023-10-11 20-26-43](https://github.com/ekomoku/ekom-pbl/assets/66005935/794f266e-2b4b-432d-9fb3-b26e5c99df13)


![Screenshot from 2023-11-07 14-13-13](https://github.com/ekomoku/ekom-pbl/assets/66005935/3123c85c-a1af-4bcf-aabb-ef822c1d5eb6)


![Screenshot from 2023-11-07 14-14-07](https://github.com/ekomoku/ekom-pbl/assets/66005935/f1d017f9-8243-4057-b8c5-57ac3ab1af81)


![Screenshot from 2023-11-07 14-15-03](https://github.com/ekomoku/ekom-pbl/assets/66005935/14944a7c-d11b-415b-be8a-e81ca23446f0)


![Screenshot from 2023-11-07 14-15-50](https://github.com/ekomoku/ekom-pbl/assets/66005935/e93edcef-694d-47d4-b3d1-ad5efa65cd9c)


![Screenshot from 2023-11-07 14-17-14](https://github.com/ekomoku/ekom-pbl/assets/66005935/3e20b384-dac3-43ef-a782-adb7da6d322d)


![Screenshot from 2023-11-07 14-18-24](https://github.com/ekomoku/ekom-pbl/assets/66005935/1e1238e4-6b49-44f4-b61f-789180c52024)


![Screenshot from 2023-11-07 14-19-38](https://github.com/ekomoku/ekom-pbl/assets/66005935/87cd1536-a990-40bc-9100-bb766408c4fc)


![Screenshot from 2023-11-07 14-20-21](https://github.com/ekomoku/ekom-pbl/assets/66005935/52d23c75-efbc-441a-bfc2-e9043f3b0996)



### Creating Austoaling Groups

This Section we will create the Auto Scaling Group (ASG)

Now we need to configure our ASG to be able to scale the EC2s out and in depending on the application traffic.

Before we start configuring an ASG, we need to create the launch template and the the AMI needed. For now we are going to use a random AMI from AWS, then in project 19, we will use Packerto create our ami.

Based on our Architetcture we need for Auto Scaling Groups for bastion, nginx, wordpress and tooling, so we will create two files; asg-bastion-nginx.tf will contain Launch Template and
Austoscaling froup for Bastion and Nginx, then asg-wordpress-tooling.tf will contain Launch Template and Austoscaling group for wordpress and tooling.

Useful Terraform Documentation, go through this documentation and understand the arguement needed for each resources:

    SNS-topic ( https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/sns_topic)

    
    SNS-notification ( https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/autoscaling_notification)

    
    Austoscaling ( https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/autoscaling_group)
    

    Launch-template ( https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/launch_template)


 ![Screenshot from 2023-11-07 14-31-32](https://github.com/ekomoku/ekom-pbl/assets/66005935/29f810b0-1984-4a37-b37b-f8128653d647)



![Screenshot from 2023-11-07 14-32-25](https://github.com/ekomoku/ekom-pbl/assets/66005935/621ad0c7-01b9-4152-81cc-e8bf74d8536f)


![Screenshot from 2023-11-07 14-34-30](https://github.com/ekomoku/ekom-pbl/assets/66005935/f095603b-257e-4362-b51e-6acc5c23b4c3)


![Screenshot from 2023-11-07 14-35-16](https://github.com/ekomoku/ekom-pbl/assets/66005935/7de83d28-90a2-4f60-a85d-4a82077fbb85)


![Screenshot from 2023-11-07 14-35-57](https://github.com/ekomoku/ekom-pbl/assets/66005935/1df1188f-d2e5-48cf-b112-074f49af1fc7)


![Screenshot from 2023-11-07 14-41-09](https://github.com/ekomoku/ekom-pbl/assets/66005935/67f29528-3d28-4c20-af66-59e39fb9b332)


![Screenshot from 2023-11-07 14-42-07](https://github.com/ekomoku/ekom-pbl/assets/66005935/62d6a475-41dc-4c90-8805-e3131c5db2a3)


![Screenshot from 2023-11-07 14-42-56](https://github.com/ekomoku/ekom-pbl/assets/66005935/f79254a8-67ae-41aa-ba29-84217b6e49bd)


![Screenshot from 2023-11-07 14-43-34](https://github.com/ekomoku/ekom-pbl/assets/66005935/c0e2fbc7-cba6-4668-89fe-6b3b030f8e33)



### Storage and Database

Useful Terraform Documentation, go through this documentation and understand the arguement needed for each resources:

    RDS ( https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_subnet_group)

    
    EFS ( https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/efs_file_system)

    
    KMS ( https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/kms_key)

    
   #### Create Elastic File System (EFS)


In order to create an EFS you need to create a KMS key.


AWS Key Management Service (KMS) makes it easy for you to create and manage cryptographic keys and control their use across a wide range of AWS services and in your applications.


Add the following code to efs.tf


![Screenshot from 2023-11-07 14-46-56](https://github.com/ekomoku/ekom-pbl/assets/66005935/04f9b818-31b0-48a8-82c5-31bf86656021)


![Screenshot from 2023-11-07 14-47-42](https://github.com/ekomoku/ekom-pbl/assets/66005935/cfe9b3e2-405e-44c2-9a10-7eeaf0a2584a)


![Screenshot from 2023-11-07 14-48-14](https://github.com/ekomoku/ekom-pbl/assets/66005935/b6240866-e445-4b00-8830-8dfd4131ddfd)


![Screenshot from 2023-11-07 14-49-05](https://github.com/ekomoku/ekom-pbl/assets/66005935/784f8bfc-fe2c-4306-ae84-fae1fe19b421)


![Screenshot from 2023-11-07 14-49-59](https://github.com/ekomoku/ekom-pbl/assets/66005935/20757d44-4eaa-4443-85a4-f18479a87284)


![Screenshot from 2023-11-07 14-50-29](https://github.com/ekomoku/ekom-pbl/assets/66005935/a9accbf1-ee28-4552-8965-03e941061f6b)


![Screenshot from 2023-11-07 14-51-06](https://github.com/ekomoku/ekom-pbl/assets/66005935/90b7849d-2800-4310-83d6-d60906156719)


![Screenshot from 2023-11-07 14-51-37](https://github.com/ekomoku/ekom-pbl/assets/66005935/1728e891-c6dc-4672-bd0c-add3030b4c14)
