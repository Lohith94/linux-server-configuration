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
* Choose the 'OS Only' and 'Ubuntu 16.04 LTS' options
* Choose a payment plan
* Give the instance a unique name and click 'Create'
* Wait for the instance to start up
