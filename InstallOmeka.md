# Install Omeka  

Omeka - Open-source web publishing platforms for sharing digital collections and creating media-rich online exhibits.  

## The Task  

To download, install, and configure Omeka.  

- GOAL: To take what you learned from the bare bones OPAC/ILS and WordPress assignments, and apply them to the Omeka installation and setup.  

## Prerequisites  

Before installing Omeka, there are a few prerequisites: install ImageMagick, enable Apache `mod_rewrite` and then restart Apache  

- ImageMagick: a suite of utilities to work with photo files. It's used by Omeka to create thumbnail images of any photo uploaded to the digital library.  
```  
sudo apt install imagemagick  
```  

- Apache `mod_rewrite`: n Apache module used to rewrite URLs. Omeka uses this to create appropriate URLs for items and collections in its digital libraries.  
```  
sudo a2enmod rewrite  
```  

- You should be instructed to restart Apache in order to activate the new configuration:  
```  
sudo systemctl restart apache2  
```  

## Omeka Installation Process  

###Step One: Download and Extract  

- The Omeka software will download as a **.zip** file. When we unzip it using the `unzip` command the result will be a new directory named **omeka-3.1**.  
```  
sudo apt instal unzip  
```  
 
- The following commands will change to the appropriate directory, use the `wget` program to download Omeka and then extract the package using the `unzip` command. 
Don't forget the `sudo`!  
```  
cd /var/www/html  
sudo wget https://github.com/omeka/Omeka/releases/download/v3.1/omeka-3.1.zip  
sudo unzip omeka-3.1
```    

A new directory called **omeka-3.1** was created and the full path of the installation is **/var/www/html/omeka-3.1**  


### Step Two: Create the Database and a User in MySQL 

- Create the Omeka database and a database user using the same process we used to create a database and user for our bare bones OPAC and for WordPress. 
Use these commands to switch to the root Linux user and then login as the MySQL root user:  
```  
sudo su  
mysql -u root  
```  

- We are now in the MySQL command prompt `mysql>`. Use the following commands to create a new user for the Omeka database, 
create a password by replacing the Xs with a strong password, create a new database for Omeka, grant all privileges for the new user, 
examine the output and then exit the MySQL prompt. After each semicolon, hit enter.
```  
create user 'omeka-3.1'@'localhost' identified by 'XXXXXXXXX';  
create database omeka;  
grant all privileges on omeka.* to 'omeka-3.1'@'localhost';  
show databases;  
\q  
```  

### Step Three: Set Up db.ini  

- - Like the **login.php** file we created for the OPAC and the **wp-config.php** for WordPress, we need to edit **db.ini**. 
It contains the name of the database, the database username and the user's password. 
To do that, `cd` to the omeka-3.1 directory `nano` to edit the file with your login info (**DB_NAME**, **DB_USER** and **DB_PASSWORD**). 
For steps two and three, if you are logged in as root, you won't need the `sudo`.  
```  
cd /var/www/html/omeka-3.1  
sudo nano db.ini  
```  

### Step Four: Optional  

- Your site is currently located at http://11.111.111.11/omeka-3.1 (with the 1s representing your IP address). 
If you want to change it, you will need to rename your directory. 
Be sure to keep the nameall lowercase and all one word (no spaces, only alphabetic characters). The word "blog" below represents the new name.  
	```  
	cd /var/www/html  
	sudo mv omeka-3.1 blog  
	```  
I changed mine from omeka-3.1 to omeka


### Step Five: Change File Ownership  

- Omeka will need to write to files in the base directory. From your base directory, `/var/www/html/omeka` in my case, run the following command:  
```  
sudo chown -R www-data:www-data *  
```  

- Restart Apache2 and MySQL using these two commands:  
```  
sudo systemctl restart apache2  
sudo systemctl restart mysql  
```  
