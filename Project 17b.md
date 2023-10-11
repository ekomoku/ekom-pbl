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
