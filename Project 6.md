
## Project 6: WEB SOLUTION WITH WORDPRESS

### Step 1: Prepare a Web Server

1. Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB



![Screenshot from 2023-01-10 19-24-37](https://user-images.githubusercontent.com/66005935/211632269-3035648a-1b14-4943-abc7-3800ae7bd5a5.png)



2. Attach all three volumes one by one to your Web Server EC2 instance



3.  Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.


