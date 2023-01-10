
## Project 6: WEB SOLUTION WITH WORDPRESS

### Step 1: Prepare a Web Server

1. Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB



![Screenshot from 2023-01-10 19-24-37](https://user-images.githubusercontent.com/66005935/211632269-3035648a-1b14-4943-abc7-3800ae7bd5a5.png)



2. Attach all three volumes one by one to your Web Server EC2 instance



3.  Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.


![Screenshot from 2023-01-10 19-30-27](https://user-images.githubusercontent.com/66005935/211633660-bdeaa86b-8c2b-4537-8027-78a5d27e849d.png)

4. Use df -h command to see all mounts and free space on your server


5. Use gdisk utility to create a single partition on each of the 3 disks


~~~
sudo gdisk /dev/xvdf
~~~

> GPT fdisk (gdisk) version 1.0.3

>Partition table scan:
  > MBR: not present
  > BSD: not present
  > APM: not present
  > GPT: not present

> Creating new GPT entries.

> Command (? for help branch segun-edits: p
> Disk /dev/xvdf: 20971520 sectors, 10.0 GiB
> Sector size (logical/physical): 512/512 bytes
> Disk identifier (GUID): D936A35E-CE80-41A1-B87E-54D2044D160B
>Partition table holds up to 128 entries
> Main partition table begins at sector 2 and ends at sector 33
> First usable sector is 34, last usable sector is 20971486
> Partitions will be aligned on 2048-sector boundaries
> Total free space is 2014 sectors (1007.0 KiB)

> Number  Start (sector)    End (sector)  Size       Code  Name
  > 1            2048        20971486   10.0 GiB    8E00  Linux LVM

> Command (? for help): w

> Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
> PARTITIONS!!

> Do you want to proceed? (Y/N): yes
> OK; writing new GUID partition table (GPT) to /dev/xvdf.
> The operation has completed successfully.
> Now,  your changes has been configured succesfuly, exit out of the gdisk console and do the same for the remaining disks.










