
# PROJECT 6: Web Solution With WordPress
## Launch an EC2 instance that will serve as “Web Server”.
Step 1 — Prepare a Web Server
Launch an EC2 instance that will serve as “Web Server”. Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.

![Screenshot from 2023-09-11 22-34-30](https://github.com/ekomoku/ekom-pbl/assets/66005935/d2e6131c-8add-48ab-b4c9-0858bc4e61d7)

### 2.0 Attach all three volumes one by one to your Web Server EC2 instance

![Screenshot from 2023-09-11 22-36-28](https://github.com/ekomoku/ekom-pbl/assets/66005935/3807e2ae-f5b6-4836-acc6-324676a52277)



### 3.0 Open up the Linux terminal to begin configuration

### 4.Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.


![Screenshot from 2023-09-11 22-40-46](https://github.com/ekomoku/ekom-pbl/assets/66005935/dd7178be-ac6c-47ac-b356-89cd13e8c662)

![Screenshot from 2023-09-11 22-42-19](https://github.com/ekomoku/ekom-pbl/assets/66005935/da492103-bc29-4398-a9b3-454153ddbefd)

![Screenshot from 2023-09-11 22-42-19](https://github.com/ekomoku/ekom-pbl/assets/66005935/4aa12e3f-d9c7-4d3f-9631-bdb665a721b4)

![Screenshot from 2023-09-11 22-44-42](https://github.com/ekomoku/ekom-pbl/assets/66005935/4c2838fd-f43c-40f8-9261-e36e94769ab6)


![Screenshot from 2023-09-11 22-45-39](https://github.com/ekomoku/ekom-pbl/assets/66005935/b6ea4f97-5545-4339-a285-b4773943e3f9)


![Screenshot from 2023-09-11 22-47-02](https://github.com/ekomoku/ekom-pbl/assets/66005935/aa63854a-6782-4571-8ad6-cb96b50d2f67)

![Screenshot from 2023-09-11 22-48-01](https://github.com/ekomoku/ekom-pbl/assets/66005935/952087b7-7aa0-449b-9b8a-77de8195047d)


![Screenshot from 2023-09-11 22-48-55](https://github.com/ekomoku/ekom-pbl/assets/66005935/b47f5b34-3efe-4b7f-b557-78a97c088958)


![Screenshot from 2023-09-11 22-49-46](https://github.com/ekomoku/ekom-pbl/assets/66005935/96631441-e86c-44b9-89ea-2608ce66cb10)








