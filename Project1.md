# LAMP STACK IMPLEMANTATION IN AWS


A LAMP stack is a bundle of four different software technologies that developers use to build websites and web applications. LAMP is an acronym for the operating system, Linux; the web server, Apache; the database server, MySQL; and the programming language, PHP. Before I attempted this project, I had already created an AWS account and launched an instance running Ubuntu Server OS. I will now describe the steps I took to succesfully implement LAMP.

**STEP 1:** I set up an SSH and connected to my running instance on AWS via terminal
<img width="1280" alt="Screenshot 2023-06-01 at 17 47 04" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/d6b16a0a-b019-4dbf-9519-7b68f58627ac">

**STEP 2:** I installed Apache using Ubuntu's package installer 'apt'

*to update a list of packages in package manager, I ran the command below*

sudo apt update

*to run apache2 package installation, I ran the command below*

sudo apt install apache2

*to verify that apache2 is running as a service in my OS, I ran the command below*

sudo systemctl status apache2

<img width="1280" alt="Screenshot 2023-06-01 at 18 34 19" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/bac01c05-fd63-40b4-85f2-e8f3223e6d68">

**STEP 3:** I added a rule in my EC2 configuration to open inbound configuration through port 80 and set the source to 0.0.0.0/0 so my Web Server can receive traffic and can be accessed both locally and from the Internet.

*to confirm I can access my Web Server locally, I ran the command below*

curl http://127.0.0.1:80

<img width="736" alt="Screenshot 2023-06-01 at 18 55 00" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/e57fcfe7-425e-4ea1-9b87-3eefada55d38">

*to confirm that my Apache server can respond to requests from the Internet, on my browser, I accessed the url below*

http://16.16.254.129
  
<img width="1270" alt="Screenshot 2023-06-01 at 19 08 39" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/f01d55e3-a42d-44ce-931c-e09462d63f36">

My web server, Apache is now running.

**STEP 4:** I installed MySQL using 'apt'

*to acquire and install this software, I ran the command below*

sudo apt install mysql-server

*after installation was completed, I logged in to the MySQL console with the command below

sudo mysql

*I set a password for the root user with the command below, using mysql_native_password as default authentication method. I'm defining this user's password as newpassword* 

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'newpassword';

*I exited the MySQL shell with the command below*

exit

*I ran a security script to remove insecure default settings and lock down access to my database system using* 

sudo mysql_secure_installation

*I validated password plugin, removed anonymous users, disabled remote root logins e.t.c then I logged back into the MySQL console using*

sudo mysql -p

