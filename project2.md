## WEB STACK IMPLEMENTATION (LEMP STACK)


Project 2 covers similar concepts as Project 1 and helps to cement your skills of deploying Web solutions using LA(E)MP stacks.
You have done a great job with successful completion of Project 1.

In this project you will implement a similar stack, but with an alternative Web Server – [NGINX](https://nginx.org/en/), which is also very popular and widely used by many websites in the Internet.


## STEP 1 – INSTALLING THE NGINX WEB SERVER

Step 1 – Installing the Nginx Web Server
In order to display web pages to our site visitors, we are going to employ Nginx, a high-performance web server. We’ll use the <span style="color:red">apt</span> package manager to install this package.

Since this is our first time using <span style ="color:red">apt</span> for this session, start off by updating your server’s package index. Following that, you can use <span style ="color:red">apt install</span> to get Nginx installed:

```
sudo apt update
sudo apt install nginx
```
###### output:
![Screenshot (82)](https://user-images.githubusercontent.com/45608947/135770909-33ee9eed-85d6-4020-805b-6607c26ed6bd.png)


When prompted, enter Y to confirm that you want to install Nginx. Once the installation is finished, the Nginx web server will be active and running on your Ubuntu 20.04 server.

To verify that nginx was successfully installed and is running as a service in Ubuntu, run:
``` 
sudo systemctl status nginx
```
If it is green and running, then you did everything correctly – you have just launched your first Web Server in the Clouds!

Before we can receive any traffic by our Web Server, we need to open [TCP port 80](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers) which is default port that web brousers use to access web pages in the Internet.

As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80:

Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).

First, let us try to check how we can access it locally in our Ubuntu shell, run:

```
curl http://localhost:80
or
curl http://127.0.0.1:80
```
These 2 commands above actually do pretty much the same – they use ‘curl’ command to request our Nginx on port 80 (actually you can even try to not specify any port – it will work anyway). The difference is that: in the first case we try to access our server via DNS name and in the second one – by IP address (in this case IP address 127.0.0.1 corresponds to DNS name ‘localhost’ and the process of converting a DNS name to IP address is called "resolution"). We will touch DNS in further lectures and projects.

As an output you can see some strangely formatted test, do not worry, we just made sure that our Nginx web service responds to ‘curl’ command with some payload.

Now it is time for us to test how our Nginx server can respond to requests from the Internet.
Open a web browser of your choice and try to access following url

``` 
http://<Public-IP-Address>:80
```
Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to use following command

```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```
The URL in browser shall also work if you do not specify port number since all web browsers use port 80 by default.

If you see following page, then your web server is now correctly installed and accessible through your firewall.






## STEP 2 — INSTALLING MYSQL
Step 2 — Installing MySQL
Now that you have a web server up and running, you need to install a Database [Management System (DBMS)](https://en.wikipedia.org/wiki/Database#Database_management_system)to be able to store and manage data for your site in a [relational database](). [MySQL]() is a popular relational database management system used within PHP environments, so we will use it in our project.

Again, use apt to acquire and install this software:

```
sudo apt install mysql-server
```
When prompted, confirm installation by typing Y, and then ENTER.

When the installation is finished, it’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Start the interactive script by running:

```
sudo mysql_secure_installation
```




