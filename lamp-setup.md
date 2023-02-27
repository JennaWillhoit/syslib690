# LAMP Setup

**Purpose:** The purpose of the LAMP process is to learn how to install
and configure Apache, PHP, and MySQL on a Linux server.

**Task:**

To install Apache2, I ran the following commands:  
`sudo apt instal apache2`  


1. I installed Apache2
	- make sure apache2 is enabled and running  
	`systemctl list-unit-files apache2.service`  
	`systemctl status apache2`
	- install w3m  
	`cd /var/log`  
	`cd apache2`  
	`sudo apt install w3m`  
	`w3m localhost`  
	- created a web page  
	`cd /var/www/html/`  
	`sudo mv index.html index.html.original`  
	`sudo nano index.html`  
	add basic html to create a page 

2. I installed PHP and configured that to work with Apache2  
	- installed the modules and restarted the Apache2  
	`sudo apt install php libapache2-mod-php`  
	`sudo systemctl restart apache2`  
	- check status to see if any errors  
	`systemctl status apache2`  
	- check install by creating a small PHP file  
	`cd /var/www/html/`  
	`sudo nano info.php`  
	added the following text:  
	<?php  
	phpinfo();  
	?>  
	- visit external IP address and add /info.php to check if it's working  
	- create a backup of the original dir.conf file before changing it  
	`cd /etc/apache2/mods-enabled/`  
	`sudo cp dir.conf dir.conf.bak`  
	`sudo nano dir.conf`  
	add index.php infront of index.html and delete later instance of index.php  
	- check to make sure configuration is still correct  
	`apachectl configtest`  
	- if OK, reload and restart  
	`sudo systemctl reload apache2`  
	`sudo systemctl restart apache2`  
	`systemctl status apache2`  
	- go back to document root directory and create a basic PHP page  
	`cd /var/www/html/`  
	`sudo nano index.php`  
	add some html and php  

3. I installed MySQL and configured that to work with Apache2


