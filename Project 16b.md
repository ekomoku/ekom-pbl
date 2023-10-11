# Automate Infrastructure With IaC using Terraform Part 1

After you have built AWS infrastructure for 2 websites manually, it is time to automate the process using Terraform.

Let us start building the same set up with the power of Infrastructure as Code (IaC)

### Prerequisites before you begin writing Terraform code



    You must have completed Terraform course from the Learning dashboard
    
    Create an IAM user, name it terraform (ensure that the user has only programatic access to your AWS account) and grant this user AdministratorAccess permissions.
    
    Copy the secret access key and access key ID. Save them in a notepad temporarily.
    
    Configure programmatic access from your workstation to connect to AWS using the access keys copied above and a Python SDK (boto3). You must have Python 3.6 or higher on your workstation.

If you are on Windows, use gitbash, if you are on a Mac, you can simply open a terminal. Read here ( https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html) to configure the Python SDK properly. 


For easier authentication configuration – use AWS CLI ( https://aws.amazon.com/cli/)  with aws configure command.

Create an S3 bucket ( https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)  to store Terraform state file. You can name it something like <yourname>-dev-terraform-bucket (Note: S3 bucket names must be unique unique within a region partition, you can read about S3 bucken naming in this article ( https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html) ). We will use this bucket from Project-17 onwards.


![Screenshot from 2023-10-10 10-54-39](https://github.com/ekomoku/ekom-pbl/assets/66005935/37d70176-fc0c-41d0-8120-ecd54f4947cd)



    Your understanding of your goal (desired AWS infrastructure end state)
    Your knowledge of the IaC technology used (in this case – Terraform)
    Your ability to effectively use up to date Terraform documentation here ( https://www.terraform.io/docs/configuration) 

    ![Screenshot from 2023-10-11 15-21-13](https://github.com/ekomoku/ekom-pbl/assets/66005935/61e9e8ff-084f-4e4b-bbfe-dd405164b8ce)



![Screenshot from 2023-10-11 15-23-14](https://github.com/ekomoku/ekom-pbl/assets/66005935/8a6ad6bc-25b5-42c8-8ce2-eba611ef1739)


##### To setup Terraform CLI, follow this instruction here ( https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli) 



![Screenshot from 2023-10-11 15-24-09](https://github.com/ekomoku/ekom-pbl/assets/66005935/216a8e8b-b9d6-428c-99f5-bbd2b262a0cc)







![Screenshot from 2023-10-11 15-24-59](https://github.com/ekomoku/ekom-pbl/assets/66005935/8f6e8943-08b1-4ac1-a86e-d89fde3cdb8c)


![Screenshot from 2023-10-11 15-25-43](https://github.com/ekomoku/ekom-pbl/assets/66005935/3dbdef63-7085-4a35-ab22-4844e2312025)


![Screenshot from 2023-10-11 15-26-30](https://github.com/ekomoku/ekom-pbl/assets/66005935/bcae71f2-b243-4d79-a3a9-45aee77b0bcf)


![Screenshot from 2023-10-11 15-27-10](https://github.com/ekomoku/ekom-pbl/assets/66005935/c771a213-b112-4e57-8e88-9eceeb23c409)


![Screenshot from 2023-10-11 15-35-10](https://github.com/ekomoku/ekom-pbl/assets/66005935/adaff7cb-a97f-4370-9b25-4fd778a652fb)


![Screenshot from 2023-10-11 15-35-44](https://github.com/ekomoku/ekom-pbl/assets/66005935/d8e9f346-e4e8-45de-ada1-01c5eb3ea17f)


![Screenshot from 2023-10-11 15-36-39](https://github.com/ekomoku/ekom-pbl/assets/66005935/ff4b94da-4449-4256-8b02-9567c25d6e03)


![Screenshot from 2023-10-11 15-37-15](https://github.com/ekomoku/ekom-pbl/assets/66005935/e5905178-61f7-48a8-8055-b04fc71f0966)


![Screenshot from 2023-10-11 15-37-55](https://github.com/ekomoku/ekom-pbl/assets/66005935/1beeaeae-8a1c-4552-adb5-bfff7d45b741)


![Screenshot from 2023-10-11 15-38-34](https://github.com/ekomoku/ekom-pbl/assets/66005935/31bdac65-00eb-47e8-983d-84fc83bfef3d)


![Screenshot from 2023-10-11 15-39-15](https://github.com/ekomoku/ekom-pbl/assets/66005935/51d5ca71-26f1-415f-af71-0bcb33ffd292)


![Screenshot from 2023-10-11 15-39-58](https://github.com/ekomoku/ekom-pbl/assets/66005935/cb3bfd60-681f-4508-ba87-42bed6354f53)


![Screenshot from 2023-10-11 15-40-52](https://github.com/ekomoku/ekom-pbl/assets/66005935/b0e8c107-4810-48b1-8e0d-4a14ec79e3d0)


![Screenshot from 2023-10-11 15-41-33](https://github.com/ekomoku/ekom-pbl/assets/66005935/7ae5b9bd-ab14-4eea-acce-4a79b47407b5)


![Screenshot from 2023-10-11 15-42-12](https://github.com/ekomoku/ekom-pbl/assets/66005935/b02a01cd-8515-438e-9fb0-ea351223ab56)
