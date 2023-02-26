# LAMP Setup

**Purpose:** The purpose of the LAMP process is to learn how to install
and configure Apache, PHP, and MySQL on a Linux server.

**Task:**

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

3. I installed MySQL and configured that to work with Apache2

To install Apache2, I ran the following commands:

```
sudo apt instal apache2
```
