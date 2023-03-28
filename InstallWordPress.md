# Install WordPress  

Once logged in to the server, if you see you have updates run the `sudo apt update` command to see what is available and then run `sudo apt upgrade -y` to upgrade and accept all changes.  

## Customized Installation Process  

### Step One: Requirements  

- Make sure that our system meets the requirements for our installation. *For WordPress, we need PHP 7.4 and MySQL 5.7.* Use these commands to check:  
	```  
	php --version  
	mysql --version  
	```  
- Additional PHP modules need to be added for WordPress to operate at full functionality. Use this command to install:  
	```  
	sudo apt install php-curl php-xml php-imagick php-mbstring php-zip php-intl  
	```  
Type Y at the prompt to continue  

- Restart Apache2 and MySQL using these two commands:  
	```  
	sudo systemctl restart apache2  
	sudo systemctl restart mysql  
	```  

### Step Two: Download and Extract  

- The WordPress software will download as a **tar.gz** file - much like a zip file. When we extract it using the `tar` command the result will be a new directory that contains multiple files and subdirectories. The following commands will change to the appropriate directory, use the `wget` program to download WordPress and then extract the package using the `tar` program. Don't forget the `sudo`!  
	```  
	cd /var/www/html  
	sudo wget https://wordpress.org/latest.tar.gz  
	sudo tar -xzvf latest.tar.gz  
	```  
A new directory called **wordpress** was created and the full path of the installation is **/var/www/html/wordpress**  

### Step Three: Create the Database and a User  

- Create the WordPress database and a database user using the same process we used to create a database and user for our bare bones OPAC. Use these commands to switch to the root Linux user and then login as the MySQL root user:   
	```  
	sudo su  
	mysql -u root  
	```  
- We are now in the MySQL command prompt `mysql>`. Use the following commands to create a new user for the WordPress database, create a password by replacing the Xs with a strong password, create a new database for Word Press, grant all privileges for the new user, examine the output and then exit the MySQL prompt. After each semicolon, hit enter.
	```  
	create user 'wordpress'@'localhost' identified by 'XXXXXXXXX';  
	create database wordpress;  
	grant all privileges on wordpress.* to 'wordpress'@'localhost';  
	show databases;  
	\q  
	```  

### Step Four: Set Up wp-config.php  

- Like the **login.php** file we created for the OPAC, we now create **wp-config.php** that contains the name of the database, the database username and the user's password. To do that, `cd` to the wordpress directory, copy and rename the **wp-config-sample.php** file to **wp-config.php** and then use `nano` to edit the file with your login info (**DB_NAME**, **DB_USER** and **DB_PASSWORD**). For steps two and three, if you are logged in as root, you won't need the `sudo`.  
	```  
	cd /var/www/html/wordpress  
	sudo cp wp-config-sample.php wp-config.php  
	sudo nano wp-config.php  
	```  
### Step Five: Optional  

- Your site is currently located at http://11.111.111.11/wordpress (with the 1s representing your IP address). If you want to change it, you will need to rename your directory. Be sure to keep the nameall lowercase and all one word (no spaces, only alphabetic characters). The word "blog" below represents the new name.  
	```  
	cd /var/www/html  
	sudo mv wordpress blog  
	```  

### Step Six: Change File Ownership  

- WordPress will need to write to files in the base directory. From your base directory, `/var/www/html/jennaslibrary` in my case, run the following command:  
	```  
	sudo chown -R www-data:www-data *  
	```  

### Step Seven: Run the Install Script  

- For this part, go to the GoogleCloud console and click the IP address URL (http://34.29.56.69/ in my case). Then copy and paste this unto the URL: `jennaslibrary/wp-admin/install.php`  

### Finish Installation  

From the Welcome screen on your URL, set up a site username and password - *NOT* the same as the username and password you used to create your WordPress database in MySQL.  

Note: We have not set up email on the server. Enter an email when setting up the user account but it won't work. Also, make sure you are using http instead of https.  

