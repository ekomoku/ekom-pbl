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
