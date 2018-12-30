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
* Copy the text and put it in a file called lightrail_key.rsa in the local ~/.ssh/ directory
* Run ```chmod 600 ~/.ssh/lightrail_key.rsa```
* Log in with the following command: ```ssh -i ~/.ssh/lightrail_key.rsa ubuntu@XX.XX.XX.XX```, where XX.XX.XX.XX is the public IP address of the instance (note that Lightsail will not allow someone to log in as ```root```; ```ubuntu``` is the default user for Lightsail instances)
