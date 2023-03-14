# Bare Bones Cataloging Module  

## Creating the HTML Page and a PHP Cataloging Page  

- Change directory to cd /var/www/html, create a new directory "cataloging" for this module, and then use nano to create "index.html"  
	```  
	cd /var/www/html  
	sudo mkdir cataloging  
	cd cataloging  
	sudo nano index.html  
	```  
the index.html file provides the user cataloging interface  

- In index.html, add the following content:  
	```  
	<!DOCTYPE html>  
	<html>  
	<head>  
    		<title>Enter Records</title>  
	</head>  
	<body>  
    		<h1>OPAC Library Administration</h1>  

		<p>This is the library administration page for entering records into the OPAC.</p>  
    		<p>Please do not use this page unless you are an authorized cataloger.</p>  

		<form action="insert.php" method="post">  
        		<label for="author">Author:</label>  
        		<input type="text" name="author" id="author" required><br><br>  

		        <label for="title">Book Title:</label>  
        		<input type="text" name="title" id="title" required><br><br>  

        		<label for="publisher">Publisher:</label>  
        		<input type="text" name="publisher" id="publisher" required><br><br>  

        		<label for="copyright">Copyright:</label>  
        		<input type="date" id="copyright" name="copyright">  

        		<input type="submit" value="Submit">  
    		</form>  
	</body>  
	</html>  

## PHP Insert Script  
Needed to communicate and add the data from our form into our MySQL database and books table  

- Use nano to create "insert.php" `sudo nano insert.php` and then copy and paste the PHP script:  
	```  
	<?php  

	// Load MySQL credentials  
	require_once '../login.php';  

	// Establish connection  
	$conn = mysqli_connect($db_hostname, $db_username, $db_password) or  
	  die("Unable to connect");  

	// Open database  
	mysqli_select_db($conn, $db_database) or  
	  die("Could not open database '$db_database'");  

	// Prepare and bind SQL statement  
	$stmt = $conn->prepare("INSERT INTO books (author, title, publisher, copyright) VALUES (?, ?, ?, ?)");  
	$stmt->bind_param("ssss", $author, $title, $publisher, $copyright);  

	// Set parameters and execute statement  
	$author = $_POST["author"];  
	$title = $_POST["title"];  
	$publisher = $_POST["publisher"];  
	$copyright = $_POST["copyright"];  

	if ($stmt->execute() === TRUE) {  
	    echo "New record created successfully";  
	} else {  
	    echo "Error: " . $stmt->error;  
	}  

	// Close statement and connection  
	$stmt->close();  
	$conn->close();  

	echo "<p>Return to the cataloging page: <a href='http://11.111.111.111/cataloging/'>http://11.111.111.111/cataloging/</a></p>";  
	?>  
	```  
change the IP address to yours (34.29.56.69)  

## Security  
We need to limit access to the module so we will rely on a simple authorization mechanism provided by the Apache2 server called htpasswd.  

- First, we create an authentication file in our **/etc/apache2** directory. Create a password (test) and username (libcat).    
	```  
	cd /etc/apache2  
	sudo htpasswd -c /etc/apache2/.htpasswd libcat  
	test  
	test  
	```  

- Use `nano` to open the apache2.conf file to tell the Apache2 web server that we will use the `htpasswd` to control access to our cataloging module  
`sudo nano /etc/apache2/apache2.conf`  

- Look for the following code block / stanza. In the third line, change **None** to **All** and save  
	```  
	<Directory /var/www/>  
  	  Options Indexes FollowSymLinks  
  	  AllowOverride None  
  	  Require all granted  
	</Directory>  
	```  

- Next, change to the cataloging directory and use `nano` to create a file called **.htaccess** and add the following content to **.htaccess** then save    
	```  
	cd /var/www/html/cataloging  
	sudo nano .htaccess  

	AuthType Basic  
	AuthName "Authorization Required"  
	AuthUserFile /etc/apache2/.htpasswd  
	Require valid-user  
	```  

- Check that the configuration file is okay: `apachectl configtest`  

- If you get a Syntax OK message, then restart Apache2 and check its status:  
	```  
	sudo systemctl restart apache2  
	systemctl status apache2  
	```  

- Now visit your cataloging module. You should be required to enter the username and password that you created with `htpasswd`.

 
 
