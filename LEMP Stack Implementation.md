# LEMP STACK IMPLEMENTATION IN AWS
A LEMP stack is a bundle of four different software technologies that developers use to build websites and web applications. LEMP is an acronym for the operating system, Linux; the web server, Nginx; the database server, MySQL; and the programming language, PHP. Before attempting this project, I had already created an AWS account and launched an instance running Ubuntu Server OS. I will now describe the steps I took to successfully implement LEMP.

**STEP 1:** I set up an SSH and connected to my running instance on AWS via terminal

<img width="569" alt="Screenshot 2023-06-03 at 09 53 27" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/d51f51f5-5c1e-42db-9b9e-02b877a2640f">

**STEP 2:** I installed Nginx using Ubuntu's package installer 'apt'

*to update a list of packages in package manager, I ran the command below*

    sudo apt update
    
*to run Nginx package installation, I ran the command below*

    sudo apt install nginx

*to verify that Nginx is running as a service in my OS, I ran the command below*

    sudo systemctl status nginx
  
<img width="561" alt="Screenshot 2023-06-02 at 22 45 05" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/ea588f96-fc3e-4412-9863-8eb3694c2c44">

**STEP 3:** I added a rule in my EC2 configuration to open inbound configuration through port 80 and set the source to 0.0.0.0/0 so my Web Server can receive traffic and can be accessed both locally and from the Internet.

<img width="1177" alt="Screenshot 2023-06-03 at 09 25 39" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/d0ee3452-60ac-4e13-a56e-003d7b87ed55">

*to confirm I can access my Web Server locally, I ran the command below*

     curl http://127.0.0.1

<img width="554" alt="Screenshot 2023-06-02 at 22 46 48" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/290104ce-ae08-4f44-b0c9-fbd1aeee1591">

*to confirm that my Nginx server can respond to requests from the Internet, on my browser, I accessed the url below*

     http://16.170.201.96
     
<img width="1097" alt="Screenshot 2023-06-02 at 22 48 37" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/0538425a-7796-4b86-a83d-dcd90dad85b6">

My web server, Nginx, is now running.

**STEP 4:** I installed MySQL using 'apt'

*to acquire and install this software, I ran the command below*

    sudo apt install mysql-server

*after installation was completed, I logged in to the MySQL console with the command below*

    sudo mysql

*I set a password for the root user using mysql_native_password as default authentication method. I'm defining this user's password as newpassword. To do this, I ran the command below*

    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'newpassword';

*I exited the MySQL shell with the command below*

    exit

*I ran a security script to remove insecure default settings and lock down access to my database system using:*

    sudo mysql_secure_installation

*I validated password plugin, removed anonymous users, disabled remote root logins e.t.c then I logged back into the MySQL console using:*

    sudo mysql -p

<img width="552" alt="Screenshot 2023-06-02 at 22 51 57" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/b8cbe32a-b73a-4172-b50a-dcc64227a6f8">

*I exited the MySQL console using:*

    exit

My MySQL server is now installed and secured.

**STEP 5:** I installed the PHP package consisting php-fpm and php-mysql using 'apt'

*to install the two packages at once, I ran the command*

    sudo apt install php-fpm php-mysql
    
PHP has been installed.

**STEP 6:** I configured Nginx to use PHP processor

When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. On Ubuntu 22.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for the your_domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites. I will be using projectLEMP as an example domain name.

*I created the root web directory for projectLEMP by running:*

    sudo mkdir /var/www/projectLEMP

*I assigned ownwership of the directory to my current system with the command:*

    sudo chown -R $USER:$USER /var/www/projectLEMP
    
*I created and opened a new configuration file in Nginx's sites-available directory using vi with the command:*

    sudo nano /etc/nginx/sites-available/projectLEMP
  
*In the blank file that was created, I pasted in the following bare-bones configuration:*

    #/etc/nginx/sites-available/projectLEMP

    server {
        listen 80;
        server_name projectLEMP www.projectLEMP;
        root /var/www/projectLEMP;

        index index.html index.htm index.php;

        location / {
        try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        }

        location ~ /\.ht {
        deny all;
        }

    }

Here’s what each of these directives and location blocks do:

listen — Defines what port Nginx will listen on. In this case, it will listen on port 80, the default port for HTTP.

root — Defines the document root where the files served by this website are stored.

index — Defines in which order Nginx will prioritize index files for this website. It is a common practice to list index.html files with a higher precedence than index.php files to allow for quickly setting up a maintenance landing page in PHP applications. You can adjust these settings to better suit your application needs.

server_name — Defines which domain names and/or IP addresses this server block should respond for. Point this directive to your server’s domain name or public IP address.

location / — The first location block includes a try_files directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find the appropriate resource, it will return a 404 error.

location ~ \.php$ — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.4-fpm.sock file, which declares what socket is associated with php-fpm.

location ~ /\.ht — The last location block deals with .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess files happen to find their way into the document root ,they will not be served to visitors.

I saved and closed the file by typing **CTRL+X** and then **y** and **ENTER** to confirm.

*I activated my configuration by linking to the config file from Nginx’s sites-enabled directory:*

    sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
    
*I tested my configuration for syntax errors by typing:*

    sudo nginx -t
    
<img width="551" alt="Screenshot 2023-06-02 at 22 55 48" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/804efe7a-ba06-4106-98e6-b765c9193d15">

*I then disabled the default Nginx host that is currently configured to listen on port 80:* 

    sudo unlink /etc/nginx/sites-enabled/default
    
*I reloaded Nginx to apply changes*

    sudo systemctl reload nginx
    
*My new website is now active, but the web root /var/www/projectLEMP is still empty. I created an index.html file in that location so that we can test that the new server block works as expected:*

    sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s         http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
    
*On my browser, I accessed the URL:*

    http://16.170.201.96
    
<img width="1063" alt="Screenshot 2023-06-02 at 22 56 39" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/1e1c1caf-e64d-4491-a3fd-17bef55cddcc">

**STEP 7:** I created a PHP script to test that Nginx is in fact able to handle .php files within my newly configured website.

*I opened a new file called info.php within my document root in my text editor with the command:*

    sudo nano /var/www/projectLEMP/info.php
    
*I pasted into the file that was created, the code:* 

    <?php
    phpinfo();
    
This page can now be accessed in a web browser by visiting the domain name or public IP address I set up in my Nginx configuration file, followed by /info.php:

    http://16.170.201.96/info.php
    
<img width="1231" alt="Screenshot 2023-06-02 at 22 58 05" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/fcb76eb9-6150-4956-9cd8-d57fb520c18b">

*I removed the file as it contains sensitive information about my PHP environment and my Ubuntu server using:*

    sudo rm /var/www/projectLEMP/info.php
    
This file can be regenerated if necessary.

**STEP 6:** Retrieving data from MySQL database with PHP

I created a test database (DB) with simple “To do list” and configured access to it, so the Nginx website would be able to query data from the DB and display it. I created a database named example_database and a user named example_user

*I started by connecting to the MySQL console using the root account:*

    sudo mysql -p
    
*To create a new database, I ran:*

    CREATE DATABASE `example_database`;
    
*I  created a new user named example_user, using mysql_native_password as default authentication method. I'm defining this user’s password as 'newpassword':*

    CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'newpassword';
    
*I gave this user permission over the example_database database by running:*

    GRANT ALL ON example_database.* TO 'example_user'@'%';
    
*I exited the MySQL shell with:*

    exit
  
*I tested if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:*

    mysql -u example_user -p  
    
*To confirm I have access to the example_database database, I ran:*

    SHOW DATABASES;
    
<img width="691" alt="Screenshot 2023-06-03 at 13 14 16" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/3dde4a1d-fd59-4699-baf0-39ae146a69ce">

*I created a test table named todo_list by running:*

    CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id)); 
    
*I inserted a few rows of content in the test table. I repeated the command below a few times, using different VALUES each time:*

    INSERT INTO example_database.todo_list (content) VALUES ("My first important item");   
  
*To confirm that the data was successfully saved to the table, I ran:*

    SELECT * FROM example_database.todo_list;  
    
<img width="559" alt="Screenshot 2023-06-02 at 23 47 41" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/c9970f22-70be-43d1-92e0-94d711459348">

*I exited the MySQL console using:*

    exit
    
*Next, I created a PHP script that will connect to MySQL and query for content. I created a new PHP file in my custom web root directory using vi:*

    nano /var/www/projectLEMP/todo_list.php 
    
The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. If there is a problem with the database connection, it will throw an exception.  

*Into my todo_list.php script, I pasted:*

     <?php
     $user = "example_user";
     $password = "newpassword";
     $database = "example_database";
     $table = "todo_list";

     try {
       $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
       echo "<h2>TODO</h2><ol>";
       foreach($db->query("SELECT content FROM $table") as $row) {
         echo "<li>" . $row['content'] . "</li>";
       }
       echo "</ol>";
     } catch (PDOException $e) {
          print "Error!: " . $e->getMessage() . "<br/>";
          die();
     }
     
I saved and closed the file by typing **CTRL+X** and then **y** and **ENTER** to confirm.

*I accessed this page on my web browser by visiting the domain name or public IP address configured for my website, followed by /todo_list.php:*

<img width="1142" alt="Screenshot 2023-06-02 at 23 49 53" src="https://github.com/ifyyegwim/Breaking-into-DevOps/assets/134213051/bec3a0ec-e075-4905-898e-07803c17f350">


**LEMP STACK HAS BEEN SUCCESSFULLY IMPLEMENTED IN AWS** 🥳

