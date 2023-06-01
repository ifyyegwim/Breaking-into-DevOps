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

My web server, Apache, is now running.

**STEP 4:** I installed MySQL using 'apt'

*to acquire and install this software, I ran the command below*

sudo apt install mysql-server

*after installation was completed, I logged in to the MySQL console with the command below

sudo mysql

*I set a password for the root user using mysql_native_password as default authentication method. I'm defining this user's password as newpassword. To do this, I ran the command below* 

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'newpassword';

*I exited the MySQL shell with the command below*

exit

*I ran a security script to remove insecure default settings and lock down access to my database system using:* 

sudo mysql_secure_installation

*I validated password plugin, removed anonymous users, disabled remote root logins e.t.c then I logged back into the MySQL console using*

sudo mysql -p

<img width="570" alt="Screenshot 2023-06-01 at 20 02 25" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/9fafb9ef-20f2-4869-83f4-013fd3ab771c">

My MySQL server is now installed and secured.

**STEP 5:** I installed the PHP package consisting php-mysql and libapache2-mod-php using 'apt'

*to install these 3 packages at once, I ran the command below*

sudo apt install php libapache2-mod-php php-mysql

*to confirm my php version after installation, I ran the command below*

php -v

<img width="566" alt="Screenshot 2023-06-01 at 20 17 21" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/37eecc2d-3a5d-497f-9740-a02b8aa117fb">

PHP has been installed.

**STEP 6:** I created a virtual host for my website using Apache

*I created a directory for project lamp with the command:*

sudo mkdir /var/www/projectlamp

*I assigned ownwership of the directory with my current system with the command:*

sudo chown -R $USER:$USER /var/www/projectlamp

*I created and opened a new configuration file in Apache's sites-available directory using vi with the command:*

sudo vi /etc/apache2/sites-available/projectlamp.conf

*on the new blank file created. I pasted in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:*

<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

*I then saved and closed the file by clicking **esc**, typing **:**, followed by **wq** and **ENTER**. Then used the **ls** command to show the new file in the sites-available directory*

sudo ls /etc/apache2/sites-available

<img width="469" alt="Screenshot 2023-06-01 at 20 53 48" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/6e5421d7-40bb-48d2-948e-8bc0348b6af4">

*I used a2ensite command to enable the new virtual host:*

sudo a2ensite projectlamp

*I disabled Apache’s default website using a2dissite command:*

sudo a2dissite 000-default

*I reloaded Apache so these changes can take effect:*

sudo systemctl reload apache2

*website is now active, but the web root /var/www/projectlamp is still empty. I create an index.html file in that location so that I can test that the virtual host works as expected:*

sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html

*on my browser, I accessed my website URL using IP address:*

http://16.16.254.129

<img width="1097" alt="Screenshot 2023-06-01 at 21 11 57" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/b20610b3-cd7c-4bc2-8c5f-5bc6aeed1f1c">

*I saw the text from **echo** command I wrote to index.html file, meaning my Apache virtual host is working as expected*

**STEP 7:** I enabled PHP on my website

*With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page.*

*to change this behavior, I edited the /etc/apache2/mods-enabled/dir.conf file and changed the order in which the index.php file is listed within the DirectoryIndex directive using the command:*

sudo vim /etc/apache2/mods-enabled/dir.conf

<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>

*After saving and closing the file, I reloaded Apache so the changes take effect:*

sudo systemctl reload apache2

*Now that I have a custom location to host my website’s files and folders, I created a PHP test script to confirm that Apache is able to handle and process requests for PHP files. Create a new file named index.php inside your custom web root folder:*

vim /var/www/projectlamp/index.php

*In the blank file that was created, I added the following text, which is valid PHP code, inside the file:*

 <?php
 phpinfo();

*I saved and closed the file, refreshed the page and this page came up:*

