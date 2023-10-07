# LEMP STACK IMPLEMENTATION (Linux ,Nginx ,MySQL and PHP)

## Prerequisites

>To prepare for this project, you will need an AWS account and a virtual server running Ubuntu Server OS.

>Here's a summary of the steps:

>Prerequisites: You need an AWS account and an Ubuntu Server virtual server.

>AWS Account: You can sign up for a free AWS account.

>EC2 Instance: AWS provides a free virtual server called EC2 (Elastic Compute Cloud) for your project.

>Setup: You can either watch instructional videos or follow written instructions to set up your AWS account and provision an Ubuntu Server on EC2.

In summary, the first step is to ensure you have the necessary AWS account and server to proceed with the project.


+ Cloud Service Provider - AWA, AZURE, GOOGLE CLOUD e.t.c
+ Launch a Linux instance (preferably UBUNTU)
+ Prior knowledge on how to link SSH into a virtual host




## **STEP 1 - Installing Nginx Web Server**

### _**Installing Nginx Web Server**_

In order to display web pages to our site visitors, we are going to employ Nginx, a high-performance web server. We'll use the apt package manager to install this package.
Since this is our first time using apt for this session, start off by updating your server's package index. Following that, you can use apt install to get Nginx installed:

>Update the list of packages in the _package manager_

`sudo apt update`

>Now, lets run the `Nginx` package installation

`sudo apt install nginx`

![Alt text](image/Nginx.png)

>When prompted, enter Y to confirm that you want to install Nginx. Once the installation is finished, the Nginx web server will be active and running on your Ubuntu 22.04 server.

>Let's run a check to confirm what we just installed, this can be checked by using

`sudo systemctl status nginx`

![Alt text](image/Nginx.png)

>Now it is time for us to test how our _Nginx_ server can respond to requests from the Internet. Open a web browser and try to access following url

`http://<Public-IP-Address>:80`

![Alt text](image/Welcome_to_nginx.png)


## **STEP 2 - Installing MYSQL**

>Now that we have a web server up and running, we need to install a _Database Management System (DBMS)_ to be able to store and manage data for my site in a relational database. **MySQL** is a popular relational database management system used within PHP environments.

> To acquire and install this software, we would run

`sudo apt install mysql`

![Alt text](image/MySql_installation.png)

To confirm what we just installed ,lets run

`sudo mysql`

![Alt text](mysql_version.png)

Now, run the pre-installed MySQL security script to enhance security by eliminating insecure defaults and securing database access. Before executing the script, ensure you set a password for the root user, using 'mysql_native_password' as the default authentication method; for instance, we're defining this user's password as _**'PassWord'.1_**

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

To exit _mysql_ mode,

`mysql> exit`

To start the interactive script, run

`$ sudo mysql_secure_installation`

+ After configuring the VALIDATE PASSWORD PLUGIN, you'll be prompted to create a password for the MySQL root user. This user has full administrative privileges within the database.

+ Although the default authentication method may not require a password, it's advisable to set a strong one for added security.

+ If you enabled password validation, you'll see a strength assessment for your chosen root password. Confirm it by entering 'Y' if you're satisfied.

+ This step ensures a robust root password for enhanced system security.

To test if we are able to login to mysql console, run

`sudo mysql -p mysql_username -u`


___

## **STEP 3 - Installing PHP**

+ Your server setup includes Apache for content delivery and MySQL for data storage and management.

+ PHP plays a crucial role by processing code to generate dynamic content for users.

+ To enable PHP to interact with MySQL databases, ensure you have both the 'php-mysql' module and 'libapache2-mod-php' installed.

+ Core PHP packages will be automatically installed as dependencies for seamless functionality.

> To install the 3 - packages, run these

`$ sudo apt install php libapache2-mod-php php-mysql`

`php -v`

> PHP 7.4.3 (cli) (built: Oct  6 2020 15:47:56) ( NTS )
> Copyright (c) The PHP Group
> Zend Engine v3.4.0, Copyright (c) Zend Technologies

![Alt text](mysql_install.png)

## **Step 4 - Configuring Nginx to Use PHP Processor**

+ When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server.

+ On Ubuntu 22.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html

+ Instead, we'll create a new directory alongside the default one to house the content for "project lamp."

+ To do this, you can use the `mkdir` command as shown below.

> Create the directory for project lamp using `mkdir` command as follows

`$ sudo mkdir /var/www/projectlemp`

> Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:

`$ sudo chown -R $USER:$USER /var/www/projectlemp`

> Then, create and open a new configuration file in Apache's sites-available directory using your preferred command-line editor. Here, we'll be using vi or vim (They are the same by the way)

`$ sudo vi /etc/apache2/sites-available/projectlamp.conf`

This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

#/etc/nginx/sites-available/projectLEMP

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

> To save and close the file simple follow the below steps:

1. Hit the esc button on the keyboard
2. Type:
3. Type wq. wfor write and qfor quit
4. Hit ENTER to save the file

> Activate your configuration by linking to the config file from _Nginx's_ sites-enabled directory:

`$ sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

> This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors by typing:

`$ sudo nginx -t`

![Alt text](image/nginx_ok.png)

> We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

`sudo unlink /etc/nginx/sites-enabled/default`

>When you are ready, reload Nginx to apply the changes:

`$ sudo systemctl reload nginx`

>Our new website is now active, but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that your new server block works as expected:

`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

Let now go to our browser and try to open your website URL using IP address:

`http://<Public-IP-Address>:80`

Here is what we got:

![Alt text](Host_name.png)

---

## **Step 5 - Testing PHP with Nginx**

>Our LEMP stack should now be completely set up.

+ LAMP Stack Installation: We've successfully completed the installation of your LAMP stack, and it's now fully operational

+ Testing _Nginx-PHP_ Interaction: To ensure that Nginx can effectively handle PHP files and pass them to your PHP processor, we need to conduct a test.

+ Creating a Test PHP File: Let's initiate the test by crafting a PHP file named _info.php_ directly within your document root. You can conveniently achieve this by opening your preferred text editor.

Now, lets run:

`$ nano /var/www/projectLEMP/info.php`

Once the nano file opens, type this _PHP_ valid code to return information about our server:

`<?php phpinfo();`

>You can now access this page in your web browser by visiting the domain name or public IP address you've set up in your Nginx configuration file, followed by _/info.php_ :

`http:// IP /info.php`

To confirm all we've done is correct, the below will be displayed

![Alt text](image/info_php.png)

>After checking the relevant information about our _PHP_ server through that page, it's best to remove the file you created as it contains sensitive information about your PHP environment and your Ubuntu server. You can use _rm_ command to remove that file:

`$ sudo rm /var/www/your_domain/info.php`

## **STEP 6 - Retrieving data from MySQL database with PHP**

>These steps should help you set up the required database and user configuration for your Nginx website to interact with the MySQL database effectively.

+ Create a Test Database: Your first step is to set up a test database (DB) that will contain a simple "To-do list." This database will be used to store and retrieve data for the _Nginx_ website.

+ Authentication Consideration: The native _MySQL PHP_ library, _mysqlnd_, lacks support for the `caching_sha2_authentication` method, which is the default authentication method for MySQL 8. Therefore, we need to create a new user with the `mysql_native_password` authentication method. This user will be used to establish a connection between PHP and the MySQL database.

+ Custom Database and User Names: By default, we'll create a database named _example_database_ and a user named _example_user_ However, you can customize these names as needed to suit your project requirements.

+ Accessing _MySQL_ Console: To begin the process, connect to the _MySQL_ console using your root account. This will allow you to execute the necessary commands to create the database and user.

`$ sudo mysql`

>To create a new database, run the following command from your MySQL console:

`mysql> CREATE DATABASE `akin22`;`

>Now you can create a new user and grant him full privileges on the database you have just created.
The following command creates a new user named _example_user_ , using _mysql _native_password_ as default authentication method. We're defining this user's password as _PassWord.1_ , but you should replace this value with a secure password of your own choosing.

`mysql>  CREATE USER 'abdulazeez'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord.1'; `

>Now we need to give this user permission over the example_database database:

`mysql> GRANT ALL ON akin22.* TO 'abduazeez'@'%'; `

>This will give the example_user user full privileges over the _akin22_ database, while preventing this user from creating or modifying other databases on your server.

>Now,let's exit MYSQL console

`mysql> exit`

>You can test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:

`$ mysql -u abdulazeez -p`

>Notice the **-p** flag in this command, which will prompt you for the password used when creating my _abdulazeez_ user. After logging in to the MySOL console. confirm that vou have access to the **example_database** database.

`mysql> SHOW DATABASES;`

The following input will be given

![Alt text](<SQL database.png>)

>Next, we'll create a test table named todo_list. From the _MySQL_ console, run the following statement:

`CREATE TABLE akin22.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));`

>Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:

`mysql> INSERT INTO akin22.todo_list (content) VALUES ("My first important item");`

>To confirm that the data was successfully saved to your table, run:

`mysql>  SELECT * FROM akin22.todo_list;`

The below output will be displayed

![Alt text](<ALL TABLES.png>)

After confirming we have a valid data in our table, let's exit the _MYSQL_ console 

`mysql > exit`

>Now you can create a _PHP_ script that will connect to _MySQL_ and query for your content. Create a new _PHP_ file in your custom web root directory using your preferred editor. We'll use **nano** for that:

`$ nano /var/www/projectLEMP/todo_list.php`

>The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. If there is a problem with the database connection, it will throw an exception.

>Copy the below content into the _todo_list.php_ script:

![Alt text](todo_list.png)

>Save and close the file when you are done editing.
You can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by `/todo_list.php`.

![Alt text](image/ToDo.png)

**OUR PHP ENVIRONMENT IS READY**




















> <IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>

> After saving and closing the file, you will need to reload Apache so the changes take effect:

`$ sudo systemctl reload apache2`

> Finally, we will create a _PHP_ script to test that _PHP_ is correctly installed and configured on your server.
Now that you have a custom location to host your website's files and folders, we'll create a _PHP_ test script to confirm that Apache is able to handle and process requests for _PHP_ files.

`$ vim /var/www/projectlamp/index.php`

> This will open a blank fle Add the following text which is valid PHP code inside the file This will open a blank file. Add the following text, which is valid PHP code, inside the file:

`<?php phpinfo();`

![Alt text](images/info_php.png)













