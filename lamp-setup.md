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
- install MySQL using this command:  
	`sudo apt install mysql-server`  
	- check to see if it's running and enabled  
	`systemctl status mysql`  
	- login to test it, become the root Linux user (**be careful**)  
	`sudo su`  
	- login to MySQL  
	root@syslib-2023:/home/jwillhoit1# `mysql -u root`  
	- create regular MySQL user called **opacuser** with a password   
	`create user 'opacuser'@'localhost' identified by 'XXXXXXXXX';` 
	- create a practice database and grant privileges to user  
	`create database opacdb;`  
	`grant all privileges on opacdb.* to 'opacuser'@'localhost';`  
	`show databases;`  
	- exit out of root MySQL user and then out of root Linux user  
	`\q`  
	`exit`  
	- login as regular MySQL user  
	`mysql -u opacuser -p` and then enter password (will not display) 
	- show databases: `show databases;` and then select opacdb: `use opacdb;`  
 	- to clear the screen in MySQL use: ctrl L  
	- create and define a new table called **books** type each line, press enter, close with ;
	`create table books`   
	`(`  
	`id int unsigned not null auto_increment,`  
	`author varchar(150) not null,`  
	`title varchar(150) not null,`  
	`copyright date not null,`  
	`primary key (id)`  
	`);`  
	- confirm table was created: `show tables;` and then describe the table: `describe books;`  
	- add records to the table - use the insert command  
	`insert into books (author, title, copyright) values`  
	`('Jennifer Egan', 'The Candy House', '2022-04-05'),`  
	`('Imbolo Mbue', 'How Beautiful We Were', '2021-03-09'),`  
	`('Lydia Millet', 'A Children\'s Bible', '2020-05-12'),`  
	`('Julia Phillips', 'Disappearing Earth', '2019-05-14');`  
	- view the records created using:  
	`select * from books;`  
	- test commands - some are single lines, some are multi-lines but all end with semicolon  
	```  
	select author from books;
	select copyright from books;
	select author, title from books;
	select author from books where author like '%millet%';
	select title from books where author like '%mbue%';
	select author, title from books where title not like '%e';
	select * from books;
	alter table books add publisher varchar(75) after title;
	describe books;
	update books set publisher='Simon \& Schuster' where id='1';
	update books set publisher='Penguin Random House' where id='2';
	update books set publisher='W. W. Norton \& Company' where id='3';
	update books set publisher='Knopf' where id='4';
	select * from books;
	delete from books where author='Julia Phillips';
	insert into books
	(author, title, publisher, copyright) values
	('Emma Donoghue', 'Room', 'Little, Brown \& Company', '2010-08-06'),
	('Zadie Smith', 'White Teeth', 'Hamish Hamilton', '2000-01-27');
	select * from books;
	select author, publisher from books where copyright < '2011-01-01';
	select author from books order by copyright;
	\q
	```


