# Install the Koha ILS

## Google Cloud Setup  

### New VM Instance  

A new virtual machine instance needs to be created because the one we have been using
does not meet the memory (RAM) needs required by the Koha ILS. Follow the instructions 
in the section titled **gcloud VM Instance** in section 2.1 (Using gcloud Virtual Machines) 
of the handbook.  

Note: the only change here is to set the **Machine Type** to **2 vCPU, 4 GB memory**. Be 
sure to delete this instance at the end of the semester!!  

### Google Cloud Firewall  

In Koha, we will need to access the staff interface on a special HTTP port. The default port for HTTP 
is 80 and when we selected **Allow HTTP traffic** when creating our VM instance, we allowed web traffic 
through port 80. But, we will use port 8080 to access the Koha staff interface so we need to add a 
firewall rule to allow web traffic through port 8080.  

In the Google Cloud console:  

- Click on the hamburger icon (navigation menu) in the top left corner next to "Google Cloud"  
- Mouse down to **VPN Network** and click on **Firewall**  
- At the top of the page, choose **Create a filewall rule** (do NOT choose Create a firewall policy)  
    - Add name: koha-ils  
    - Add description: Allow traffic to staff interface  
- Next to **Targets**, click on **ALL instances in the network**  
- In the **Source IPv4 ranges** field, add **0.0.0.0/0**  
- Under **Protocols and ports**, click on **Specified protocols and ports  
    - Click the box nect to **TCP**  
    - Add **8080** in the **Ports** box  
- Click on **Create** at the bottom of the page  

- Click on the hamburger icon (navigation menu) in the top left  
- Mouse down to **Compute Engine** and click on **VM instances**   
- Click on the down arrow next to SSH for your new VM instance (syslib-koha-ils) and select **View gcloud command**  
- Copy the gcloud command line and paste into your terminal (same process as when we open our original VM instance in the command line)  

## Install Koha Repo  

### Server Setup  

Update our local repositories: `sudo apt update`  

Then upgrade our servers: `sudo apt upgrade`  

We use the next two commands to help save disk space. The first, `sudo apt autoremove` removes packages that are no longer needed 
and `sudo apt clean` clears out the local repository of retrieved package files. Combine them on one line:  

```  
sudo apt autoremove -y && sudo apt clean  
```  

Next, install **gnupg2**, which is used to create digital signatures, encrypt data and aid in secure communication: `sudo apt install gnupg2`  

And then you'll need to reboot your machine. This will disconnect you from the server - wait a minute and reconnect. `sudo reboot now`  

### Add Koha Repository  

We can add repositories to sync with and to use to download software, and this includes the Koha ILS. To add the special Koha repository to our system, 
we use the following commands. Note: most of the following commands require admin accessso either login as **root** user or be sure to use the 
`sudo` command.  First line logs you into **root**, second command gets you out of your home directory, third adds the Koha repository to the server 
and fourth adds the digital signature that verifies the Koha repo.  

```  
sudo su  
cd   
echo 'deb http://debian.koha-community.org/koha stable main' | sudo tee /etc/apt/sources.list.d/koha.list  
wget -q -O- https://debian.koha-community.org/koha/gpg.asc | sudo apt-key add -  
```  

## Install Koha  

Note: If you are still in **root** you do NOT need to use `sudo` in front of these commands.  

The new repository now needs to be updated and synced to the Koha remote repository - so use, `apt update` again  

Next use the `apt show koha-common` command to retrieve that data from the new repository we added  

And now we can install Koha: `apt install koha-common`  

### Configure Koha  

We now need to configure Koha to get it up and running. First, we need to edit some configuration files:  
```  
nano /etc/koha/koha-sites.conf  
```  

With the above file, **koha-sites.conf** file open, change the line that contains `INTRAPORT="80"` to `="8080"`  

Next, install and setup **mysql-server**: `apt install mysql-server`  

Instead of creating a database and a user like we did for WordPress and Omeka, here we are going to do that without logging in. To set 
the MySQL root password:  
```  
mysqladmin -u root password bibliolib1  
```  

Now we need to enable URL rewriting and CGI functionality to Apache2. Following the first two steps, we need to restart Apache2:  
```  
a2enmod rewrite  
a2enmod cgi  
systemctl restart apache2  
```  

We need to create a database for Koha: `koha-create --create-db bibliolib`  

Now, tell Apache2 it needs to listen on port 8080. Open `ports.conf` with `nano` and then add `Listen:8080`  
```  
nano /etc/apache2/ports.conf  
```  
Under "Listen 80" add "Listen 8080" -> ^X  Y  

Restart Apache2 again: `systemctl restart apache2`  

The next few commands will disable the default Apache2 setup, enable traffic compression using **deflate**, enable the **bibliolib** site, 
and then reload Apache2's configurations and restart again:  
```  
a2dissite 000-default  
a2enmod deflate  
a2ensite bibliolib  
systemctl reload apache2  
systemctl restart apache2  
```  

### Koha Web Installer  

All the back work is complete and we will finish the Koha installation through a web installer. First, we need to get the Koha username 
and password in the following file by opening it in `nano`.  
```  
nano /etc/koha/sites/bibliolib/koha-conf.xml  
```  

Look for `<config>` stanza Line number 252 which will be about halfway down the page) and the line beginning with `<user>` (five lines below config). 
The password is the next line `<pass>`. Copy the username and password and then exit nano (^X).  
Example:  
<user>koha_bibliolib</user>  
<pass>FpGCB5[iH,H|#`^J</pass>  

Go back to your Google Cloud screen and click on the external IP address for your NEW VM instance. Make sure it is HTTP:// NOT HTTPS:// and then add 
:8080 to the end of the URL.  
Example:  
http://34.69.79.51:8080  

You should then see the Koha 22.11 web installer screen. Copy and paste your username and password into the login screen. Follow instructions as you 
click through each step.  

## Public OPAC  

Once you have completed the installation and the initial setup of Koha, you will have access to the staff interface. To be able to view 
the public facing OPAC, you'll need to make a change in the settings. From the home screen:  

- Click on **More** in the top drop down box (next to the little shopping cart)  
- Click on **Administration**  
- Click on **Global System Preferences** (in the white box near the top of the page)  
- Click on **OPAC** in the gray left hand side bar  
- Scroll down to the **OPACBaseURL** line (9th line down)  
- Enter the IP address of your server: **http://34.69.79.51**  
- Click on **Save all OPAC Preferences** (top left corner, orange button)  

