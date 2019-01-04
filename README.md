# linux-server-configuration
## Project Overview
To take a baseline installation of a Linux server and prepare it to host your web applications. To secure your server from a number of attack vectors, install and configure a database server, and deploy one of your existing web applications onto it.
## Details specific to the server set up
* The IP address is 
* The SSH port used is 2200.
* The URL to the hosted webpage is:
## Software Installed:
* Apache2
* mod_wsgi
* PostgreSQL
* git
* pip
* virtualenv
* httplib2
* Python Requests
* oauth2client
* SQLAlchemy
* Flask
* libpq-dev
* Psycopg2
* Feedparser
## Configuration steps
### Create an instance with Amazon Lightsail
* Sign in to [Amazon Lightsail](https://aws.amazon.com/lightsail/) using an Amazon Web Services account
* Follow the 'Create an instance' link
* Choose the 'OS Only' and 'Ubuntu 18.04 LTS' options
* Choose a payment plan
* Give the instance a unique name and click 'Create'
* Wait for the instance to start up
### Connect to the instance on a local machine
Note: While Amazon Lightsail provides a broswer-based connection method, this will no longer work once the SSH port is changed (see below). The following steps outline how to connect to the instance via the Terminal program on Mac OS machines (this can also be done on a Windows machine with a program such as PuTTY).
* Download the instance's private key by navigating to the Amazon Lightsail 'Account page'
* Click on 'Download default key'
* A file called LightsailDefaultPrivateKey.pem or LightsailDefaultPrivateKey-YOUR-AWS-REGION.pem will be downloaded; open this in a text editor
* Copy the text and put it in a file called lightrail_key.rsa in the local ```~/.ssh/ directory```
* Run ```chmod 600 ~/.ssh/lightrail_key.rsa```
* Log in with the following command: ```ssh -i ~/.ssh/lightrail_key.rsa ubuntu@XX.XX.XX.XX```, where XX.XX.XX.XX is the public IP address of the instance (note that Lightsail will not allow someone to log in as ```root```; ```ubuntu``` is the default user for Lightsail instances)
### Upgrade currently installed packages
* Notify the system of what package updates are available by running ```sudo apt-get update```
* Download available package updates by running ```sudo apt-get upgrade```
### Configure the firewall
* Start by changing the SSH port from ```22``` to ```2200``` 
* Run ```sudo vim /etc/ssh/ssfd_config```(open up the /etc/ssh/sshd_config file, change the port number on line 5 to ```2200```, then restart SSH by running ```sudo service ssh restart```; restarting SSH is a very important step!)
* Check to see if the ufw (the preinstalled ubuntu firewall) is active by running ```sudo ufw status```
* Run ```sudo ufw default deny incoming``` to set the ufw firewall to block everything coming in
* Run ```sudo ufw default allow outgoing``` to set the ufw firewall to allow everything outgoing
* Run ```sudo ufw allow ssh``` to set the ufw firewall to allow SSH
* Run ```sudo ufw allow 2200/tcp``` to allow all tcp connections for port ```2200``` so that SSH will work
* Run ```sudo ufw allow www``` to set the ufw firewall to allow a basic HTTP server
* Run ```sudo ufw allow 123/udp``` to set the ufw firewall to allow NTP
* Run ```sudo ufw deny 22``` to deny port ```22``` (deny this port since it is not being used for anything; it is the default port for SSH, but this virtual machine has now been configured so that SSH uses port ```2200```)
* Run ```sudo ufw enable``` to enable the ufw firewall
* Run ```sudo ufw status``` to check which ports are open and to see if the ufw is active; if done correctly, it should look like this:
```
To                         Action      From
--                         ------      ----
22                         DENY        Anywhere
2200/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
123/udp                    ALLOW       Anywhere
22 (v6)                    DENY        Anywhere (v6)
2200/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
123/udp (v6)               ALLOW       Anywhere (v6)
```
* Update the external (Amazon Lightsail) firewall on the browser by clicking on the 'Manage' option, then the 'Networking' tab, and then changing the firewall configuration to match the internal firewall settings above (only ports ```80```(TCP), ```123```(UDP), and ```2200```(TCP) should be allowed; make sure to deny the default port ```22```)
* Now, to login (on a Mac), open up the Terminal and run:
```ssh -i ~/.ssh/lightrail_key.rsa -p 2200 ubuntu@XX.XX.XX.XX```, where XX.XX.XX.XX is the public IP address of the instance
Note: As mentioned above, connecting to the instance through a browser now no longer works; this is because Lightsail's browser-based SSH access only works through port ```22```, which is now denied.
### Create a new user named ```grader```
* Run sudo adduser grader
* Enter in a new UNIX password (twice) when prompted
* Fill out information for the new ```grader``` user
* To switch to the ```grader``` user, run ```su - grader```, and enter the password
### Give ```grader``` user sudo permissions
* Run ```sudo visudo```
* Search for a line that looks like this:
```root ALL=(ALL:ALL) ALL```
* Add the following line below this one:
```grader ALL=(ALL:ALL) ALL```
* Save and close the visudo file
* To verify that grader has sudo permissions, ```su``` as ```grader``` (run ```su - grader```), enter the password, and run ```sudo -l```; after entering in the password (again), a line like the following should appear, meaning grader has sudo permissions:
```
Matching Defaults entries for grader on
    ip-XX-XX-XX-XX.ec2.internal:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User grader may run the following commands on
	ip-XX-XX-XX-XX.ec2.internal:
    (ALL : ALL) ALL
```
### Allow ```grader``` to log in to the virtual machine
* Run ```ssh-keygen``` on the local machine
* Choose a file name for the key pair (such as ```grader_key```)
* Enter in a passphrase twice (two files will be generated; the second one will end in .pub)
* Log in to the virtual machine
* Switch to ```grader```'s home directory, and create a new directory called ```.ssh``` (run ```mkdir .ssh```)
* Run ```touch .ssh/authorized_keys```
* On the local machine, run ```cat ~/.ssh/insert-name-of-file.pub```
* Copy the contents of the file, and paste them in the ```.ssh/authorized_keys``` file on the virtual machine
* Run ```chmod 700 .ssh``` on the virtual machine
* Run ```chmod 644 .ssh/authorized_keys``` on the virtual machine
* Make sure key-based authentication is forced (log in as grader, open the ```/etc/ssh/sshd_config``` file, and find the line that says, ```# Change to no to disable tunnelled clear text passwords```; if the next line says, ```PasswordAuthentication yes```, change the ```yes``` to ```no```; save and exit the file; run ```sudo service ssh restart```)
* Log in as the ```grade```r using the following command:
```ssh -i ~/.ssh/grader_key -p 2200 grader@XX.XX.XX.XX```
Note that a pop-up window will ask for ```grader```'s password.
### Configure the local timezone to UTC
* Run ```sudo dpkg-reconfigure tzdata```, and follow the instructions (UTC is under the 'None of the above' category)
* Test to make sure the timezone is configured correctly by running ```date```
### Install and configure Apache
* Run ```sudo apt-get install apache2``` to install Apache
* Check to make sure it worked by using the public IP of the Amazon Lightsail instance as as a URL in a browser; if Apache is working correctly, a page with the title 'Apache2 Ubuntu Default Page' should load
### Install mod_wsgi
* Install the ```mod_wsgi``` package (which is a tool that allows Apache to serve Flask applications) along with ```python-dev``` (a package with header files required when building Python extensions); use the following command:
```sudo apt-get install libapache2-mod-wsgi python-dev```
* Make sure mod_wsgi is enabled by running ```sudo a2enmod wsgi```
### Install PostgreSQL and make sure PostgreSQL is not allowing remote connections
* Install PostgreSQL by running ```sudo apt-get install postgresql```
* Open the ```/etc/postgresql/9.5/main/pg_hba.conf``` file
* Make sure it looks like this (comments have been removed here for easier reading):
```
local   all             postgres                                peer
local   all             all                                     peer
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5
```


