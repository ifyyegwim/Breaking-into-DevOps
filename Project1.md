# LAMP STACK IMPLEMANTATION IN AWS


A LAMP stack is a bundle of four different software technologies that developers use to build websites and web applications. LAMP is an acronym for the operating system, Linux; the web server, Apache; the database server, MySQL; and the programming language, PHP. Before I attempted this project, I had already created an AWS account and launched an instance running Ubuntu Server OS. I will now describe the steps I took to succesfully implement LAMP.

**STEP 1:** I set up an SSH and connected to my running instance on AWS via terminal
<img width="1280" alt="Screenshot 2023-06-01 at 17 47 04" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/d6b16a0a-b019-4dbf-9519-7b68f58627ac">

**STEP 2:** I installed Apache using Ubuntu's package installer 'apt'

*to update a list of packages in package manager, I ran the command below*

sudo apt update

*to run apache2 package installation, I ran the command below*

sudo apt install apache2

*to verify that apache2 is running as a service in my OS, I ran the command below

sudo systemctl status apache2
