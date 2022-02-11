## WEB SOLUTION WITH WORDPRESS
You are progressing in practicing to implement web solutions using different technologies. As a DevOps engineer you will most probably encounter PHP-based solutions since, even in 2021, it is the dominant web programming language used by more websites than any other programming language.

In this project you will be tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress. WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).

Project 6 consists of two parts:

1. Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give you practical experience of working with disks, partitions and volumes in Linux.

2. Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify your skills of deploying Web and DB tiers of Web solution.

As a DevOps engineer, your deep understanding of core components of web solutions and ability to troubleshoot them will play essential role in your further progress and development.

Three-tier Architecture
Generally, web, or mobile solutions are implemented based on what is called the **Three-tier Architecture**

**Three-tier Architecture** is a client-server software architecture pattern that comprise of 3 separate layers.

![image](https://user-images.githubusercontent.com/45608947/138530405-ca66ecd8-8817-461f-86e6-d1e2193bd379.png)


1. **Presentation Layer (PL):** This is the user interface such as the client server or browser on your laptop.
2. **Business Layer (BL):** This is the backend program that implements business logic. Application or Webserver
3. **Data Access or Management Layer (DAL):** This is the layer for computer data storage and data access. Database Server or File System Server such as FTP server, or NFS Server

In this project, you will have the hands-on experience that showcases Three-tier Architecture while also ensuring that the disks used to store files on the Linux servers are adequately partitioned and managed through programs such as gdisk and LVM respectively.

You will be working working with several storage and disk management concepts, to have a better understanding, watch following video:
Disk management in Linux

**Note:** We are gradually introducing new AWS elements into our solutions, but do not be worried if you do not fully understand AWS Cloud Services yet, there are Cloud focused projects ahead where we will get into deep details of various Cloud concepts and technologies – not only AWS, but other Cloud Service Providers as well.

**Your 3-Tier Setup**
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server

**Use RedHat OS for this project**

By now you should know how to spin up an EC2 instanse on AWS, but if you forgot – refer to Project1 Step 0.
In previous projects we used ‘Ubuntu’, but it is better to be well-versed with various Linux distributions, thus, for this projects we will use very popular distribution called ‘RedHat’ (it also has a fully compatible derivative – CentOS)

**Note:** for Ubuntu server, when connecting to it via SSH/Putty or any other tool, we used ubuntu user, but for RedHat you will need to use ec2-user user. Connection string will look like ec2-user@<Public-IP>

Let us get started!


## LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER”.
  Step 1 — Prepare a Web Server
1. Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.
Learn How to Add EBS Volume to an EC2 instance [here](https://www.youtube.com/watch?v=HPXnXkBzIHw)
  
  ![image](https://user-images.githubusercontent.com/45608947/138531516-f4fdcccd-c32c-45f1-a4d1-b8d52dfcd3a4.png)  

2. Attach all three volumes one by one to your Web Server EC2 instance
  
![image](https://user-images.githubusercontent.com/45608947/138531587-e48c7854-acd3-4c79-8e77-da3c511443c3.png)

  
3. Open up the Linux terminal to begin configuration

4. Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.
  
  ![image](https://user-images.githubusercontent.com/45608947/138531866-6cbd6155-e7a7-424e-876b-2a31905ef61e.png)
  
  **OUTPUT**
  ![Screenshot (149)](https://user-images.githubusercontent.com/45608947/153641056-2004c5b5-60ce-4930-8ba1-b7c6e6ca595e.png)

   
5. Use df -h command to see all mounts and free space on your server

6. Use gdisk utility to create a single partition on each of the 3 disks

```
sudo gdisk /dev/xvdf
```
```
   GPT fdisk (gdisk) version 1.0.3

Partition table scan:
  MBR: not present
  BSD: not present
  APM: not present
  GPT: not present

Creating new GPT entries.

Command (? for help branch segun-edits: p
Disk /dev/xvdf: 20971520 sectors, 10.0 GiB
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): D936A35E-CE80-41A1-B87E-54D2044D160B
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 20971486
Partitions will be aligned on 2048-sector boundaries
Total free space is 2014 sectors (1007.0 KiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048        20971486   10.0 GiB    8E00  Linux LVM

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): yes
OK; writing new GUID partition table (GPT) to /dev/xvdf.
The operation has completed successfully.
Now,  your changes has been configured succesfuly, exit out of the gdisk console and do the same for the remaining disks.
```

7. Use lsblk utility to view the newly configured partition on each of the 3 disks.

  ![image](https://user-images.githubusercontent.com/45608947/138532142-60f930aa-42c7-4ad6-aa38-2ade9a2343b3.png)
  
    **OUTPUT**
  ![Screenshot (150)](https://user-images.githubusercontent.com/45608947/153647700-654cbcdc-c6cb-49f4-92c1-a96d1548a661.png)

  
8. Install lvm2 package using
  ```
  sudo yum install lvm2
  ```
 check for available partitions.
  ```
  sudo lvmdiskscan 
  ```
  
Note: Previously, in Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.
  
  
  
  **OUTPUT**
  ![Screenshot (151)](https://user-images.githubusercontent.com/45608947/153648488-615878fd-ec80-44f0-a841-b27904a75b8f.png)


9. Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM
```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```
10. Verify that your Physical volume has been created successfully by running sudo pvs
  
![image](https://user-images.githubusercontent.com/45608947/138532419-4ca21057-237f-4258-a992-986e7f0c47a7.png)
  
  **OUTPUT**
  ![Screenshot (152)](https://user-images.githubusercontent.com/45608947/153648839-c1625678-30a7-4528-8be0-a8187eebed47.png)


11. Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg
```
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
```
  
12. Verify that your VG has been created successfully by running sudo vgs
  
  ![image](https://user-images.githubusercontent.com/45608947/138532477-145ce96b-7b40-402d-8a25-76efd6e73d7e.png)
  
13. Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.
```  
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```
  
14.Verify that your Logical Volume has been created successfully by running sudo lvs
  
  ![image](https://user-images.githubusercontent.com/45608947/138532612-c2393971-aa1e-46d9-97c7-874bb84f4984.png)

15. Verify the entire setup

```
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk  
```
  
![image](https://user-images.githubusercontent.com/45608947/138532748-aed1aa9d-8d84-4e99-8e39-e131949a9bea.png)

16. Use mkfs.ext4 to format the logical volumes with ext4 filesystem

```
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```
  
17. Create /var/www/html directory to store website files
  
```
sudo mkdir -p /var/www/html
```

18. Create /home/recovery/logs to store backup of log data
  
```
sudo mkdir -p /home/recovery/logs
```

19. Mount /var/www/html on apps-lv logical volume

```
sudo mount /dev/webdata-vg/apps-lv /var/www/html/
```
20. Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)

```
sudo rsync -av /var/log/. /home/recovery/logs/
```
  
21. Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very
important)
```
sudo mount /dev/webdata-vg/logs-lv /var/log
```
  
22. Restore log files back into /var/log directory
  
```
sudo rsync -av /home/recovery/logs/. /var/log
```
  
23. Update **/etc/fstab** file so that the mount configuration will persist after restart of the server.
  
**Click on the next button** To update the /etc/fstab file
  

  
## UPDATE THE `/ETC/FSTAB` FILE
  
The UUID of the device will be used to update the /etc/fstab file;

```
sudo blkid
```

![image](https://user-images.githubusercontent.com/45608947/138533458-4ab2e9d6-d133-493a-a20c-3adb2abfb19e.png)

```
sudo vi /etc/fstab
```
  
Update **/etc/fstab** in this format using your own UUID and rememeber to remove the leading and ending quotes.
  
![image](https://user-images.githubusercontent.com/45608947/138533535-eef51236-b6c1-46a6-8ae7-f1776241ddbc.png)

1. Test the configuration and reload the daemon
  
```
 sudo mount -a
 sudo systemctl daemon-reload
```
  
2. Verify your setup by running **df -h**, output must look like this:
  
  ![image](https://user-images.githubusercontent.com/45608947/138533641-31c5e16e-2447-439a-a5f3-68f5ca00e613.png)


**Step 2 — Prepare the Database Server**
  
Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of **/var/www/html/.**

**Step 3 — Install WordPress on your Web Server EC2**
  
1. Update the repository
  
```
sudo yum -y update
```

2. Install wget, Apache and it’s dependencies
  
```
sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
```

3. Start Apache
  
```
sudo systemctl enable httpd
sudo systemctl start httpd
```
  
4. To install PHP and it’s depemdencies
```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```
  
5. Restart Apache
  
```
sudo systemctl restart httpd
```

6. Download wordpress and copy wordpress to var/www/html
  
```
  mkdir wordpress
  cd   wordpress
  sudo wget http://wordpress.org/latest.tar.gz
  sudo tar xzvf latest.tar.gz
  sudo rm -rf latest.tar.gz
  cp wordpress/wp-config-sample.php wordpress/wp-config.php
  cp -R wordpress /var/www/html/
```
  
7. Configure SELinux Policies

```
  sudo chown -R apache:apache /var/www/html/wordpress
  sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
  sudo setsebool -P httpd_can_network_connect=1
```
  
  
**Step 4 — Install MySQL on your DB Server EC2**
```
sudo yum update
sudo yum install mysql-server
```
  
Verify that the service is up and running by using **sudo systemctl status mysqld,** if it is not running, restart the service and enable it so it will be running even after reboot:
  
```
sudo systemctl restart mysqld
sudo systemctl enable mysqld
```
  
**Step 5 — Configure DB to work with WordPress**
  
```
sudo mysql
CREATE DATABASE wordpress;
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
```
  
**Step 6 — Configure WordPress to connect to remote database.**
  
**Hint:** Do not forget to open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY from your Web Server’s IP address, so in the Inbound Rule configuration specify source as /32
  
  ![image](https://user-images.githubusercontent.com/45608947/138534984-4d5ebb8e-1d31-494e-8262-e641faf06ab4.png)

 
1. Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client
  
```
sudo yum install mysql
sudo mysql -u admin -p -h <DB-Server-Private-IP-address>
```
  
2. Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases.
  
3. Change permissions and configuration so Apache could use WordPress:

4. Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your workstation’s IP)

5. Try to access from your browser the link to your WordPress http://<Web-Server-Public-IP-Address>/wordpress/  
  
  ![image](https://user-images.githubusercontent.com/45608947/138535141-ea34505e-f3d3-4910-af98-ec6dbe1bff4c.png)

 Fill out your DB credentials:
  
![image](https://user-images.githubusercontent.com/45608947/138535184-ad5d32e0-6d39-4296-bd06-9613b3ecfda8.png)


If you see this message – it means your WordPress has successfully connected to your remote MySQL database

![image](https://user-images.githubusercontent.com/45608947/138535217-d9f88903-b830-4a83-9580-4551feb03b91.png)
  
Important: Do not forget to STOP your EC2 instances after completion of the project to avoid extra costs.

CONGRATULATIONS!
You have learned how to configure Linux storage susbystem and have also deployed a full-scale Web Solution using WordPress CMS and MySQL RDBMS.
  





