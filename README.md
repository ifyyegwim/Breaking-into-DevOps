# LAMP STACK IMPLEMENTATION IN AWS
A LAMP stack is a bundle of four different software technologies that developers use to build websites and web applications. LAMP is an acronym for the operating system, Linux; the web server, Apache; the database server, MySQL; and the programming language, PHP. Before I attempted this project, I had already created an AWS account and launched an instance running Ubuntu Server OS. I will now describe the steps I took to successfully implement LAMP.

**STEP 1 - SET UP AN SSH AND CONNECT TO UBUNTU SERVER ON AWS VIA TERMINAL**

<img width="1280" alt="Screenshot 2023-06-01 at 17 47 04" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/d6b16a0a-b019-4dbf-9519-7b68f58627ac">

**STEP 2 - INSTALL APACHE WEB SERVER**

*First, update a list of packages in package manager:*

    sudo apt update

*Next, run apache2 package installation:*

    sudo apt install apache2

*To verify that apache2 is running as a service in my OS, run:*

    sudo systemctl status apache2

<img width="1280" alt="Screenshot 2023-06-01 at 18 34 19" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/bac01c05-fd63-40b4-85f2-e8f3223e6d68">

*We need to add a rule in our EC2 configuration to open inbound configuration through port 80 and set the source to 0.0.0.0/0 so our Web Server can receive traffic and can be accessed both locally and from the Internet.*

<img width="1177" alt="Screenshot 2023-06-03 at 09 25 39" src="https://github.com/NewthingAde/WEB-STACK-IMPLEMENTATION-LEMP-STACK-/assets/134213051/af7dbed1-de51-4f48-ae5e-b9249b9e700e">

*To confirm we can access my Web Server locally, run:*
                 
    $ curl http://localhost:80
                   or
    $ curl http://127.0.0.1:80           

<img width="736" alt="Screenshot 2023-06-01 at 18 55 00" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/e57fcfe7-425e-4ea1-9b87-3eefada55d38">

*To confirm that our Apache server can respond to requests from the Internet, on our browser, we try to accessed the url below*

    http://<Public-IP-Address>:80)
  
[<img width="1270" alt="Screenshot 2023-06-01 at 19 08 39" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/f01d55e3-a42d-44ce-931c-e09462d63f36">](https://user-images.githubusercontent.com/80678596/163705182-cd4d5d38-6943-41dc-bfb1-2184847c3a54.png)

Our web server, Apache, is now running.

**STEP 3 - INSTALL MySQL DATABASE SERVER**

*To acquire and install this software, run*

    sudo apt install mysql-server

*After installation is completed, log in to the MySQL console with the command below*

    sudo mysql

*Set a password for the root user using mysql_native_password as default authentication method. We're defining this user's password as newpassword. To do this, run:* 

    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'newpassword';

*Exit the MySQL shell with the command below*

    exit

*Run a security script to remove insecure default settings and lock down access to your database system using:* 

    sudo mysql_secure_installation

*Validate password plugin, remove anonymous users, disabled remote root logins e.t.c then I logged back into the MySQL console using:*

    sudo mysql -p

<img width="570" alt="Screenshot 2023-06-01 at 20 02 25" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/9fafb9ef-20f2-4869-83f4-013fd3ab771c">

My MySQL server is now installed and secured.

**STEP 4 - INSTALL PHP**

In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. You’ll also need libapache2-mod-php to enable Apache to handle PHP files.

*To install these 3 packages at once, run*

    sudo apt install php libapache2-mod-php php-mysql

*To confirm my php version after installation, run*

    php -v

<img width="566" alt="Screenshot 2023-06-01 at 20 17 21" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/37eecc2d-3a5d-497f-9740-a02b8aa117fb">

PHP has been installed.

**STEP 5 - CREATE A VIRTUAL HOST USING APACHE**

*Create a directory for projectlamp with the command:*

    sudo mkdir /var/www/projectlamp

*Assign ownwership of the directory to your current system with the command:*

    sudo chown -R $USER:$USER /var/www/projectlamp

*Create and open a new configuration file in Apache's sites-available directory using vi with the command:*

    sudo vi /etc/apache2/sites-available/projectlamp.conf

*On the new blank file created, past in the following bare-bones configuration by hitting on **i** on the keyboard to enter the insert mode, and paste the text:*

    <VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>

*Save and close the file by clicking **esc**, typing **:**, followed by **wq** and **ENTER**. Then used the **ls** command to show the new file in the sites-available directory*

    sudo ls /etc/apache2/sites-available

<img width="469" alt="Screenshot 2023-06-01 at 20 53 48" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/6e5421d7-40bb-48d2-948e-8bc0348b6af4">

*Use a2ensite command to enable the new virtual host:*

    sudo a2ensite projectlamp

*Disable Apache’s default website using a2dissite command:*

    sudo a2dissite 000-default

*Reloade Apache so these changes can take effect:*

    sudo systemctl reload apache2

*Your website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that you can test that the virtual host works as expected:*

    sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s         http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html

*On your browser, try to access your website URL using:*

    http://<Public-IP-Address>:80

<img width="1097" alt="Screenshot 2023-06-01 at 21 11 57" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/b20610b3-cd7c-4bc2-8c5f-5bc6aeed1f1c">

Apache virtual host is working as expected.

**STEP 6 - ENABLE PHP**

With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page.

*To change this behaviour, edited the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive using the command:*

    sudo vim /etc/apache2/mods-enabled/dir.conf

    <IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
    </IfModule>

*After saving and closing the file, reloaded Apache so the changes take effect:*

    sudo systemctl reload apache2

*Now that you have a custom location to host my website’s files and folders, create a PHP test script to confirm that Apache is able to handle and process requests for PHP files. Create a new file named index.php inside your custom web root folder:*

    vim /var/www/projectlamp/index.php

*In the blank file that was created, add the following text, which is valid PHP code, inside the file:* 
               
    <?php
     phpinfo();
     
*Save and close the file and refresh the page:*

<img width="1100" alt="Screenshot 2023-06-01 at 21 44 24" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/4cbfc9bc-1650-4e25-92e1-c7243f4703a7">

PHP Installation is working as expected.

*Remove the file as it contains sensitive information about your PHP environment and your Ubuntu server using the command:*

    sudo rm /var/www/projectlamp/index.php

The page can be recreated if needed.

**LAMP STACK HAS BEEN SUCCESSFULLY IMPLEMENTED IN AWS** 🥳
