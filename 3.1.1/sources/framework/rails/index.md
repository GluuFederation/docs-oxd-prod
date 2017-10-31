# oxd Ruby On Rails

The following documentation demonstrates how to use Gluu's commercial OAuth 2.0 client software, [oxd](http://oxd.gluu.org), to send users from a Ruby on Rails application to an OpenID Connect Provider (OP) for login. You can send users to any standard OpenID Connect Provider (OP) for login, including the [free open source Gluu Server](http://gluu.org/gluu-server) or Google. 

!!! Note
    You can also refer to the [oxd-ruby library docs](../../libraries/ruby/) for more details on ruby classes.


## Installation Guides

- [Github oxd-ruby](https://github.com/GluuFederation/oxd-ruby)
- [Gluu Server](https://gluu.org/docs/ce/3.1.1/installation-guide/install/)
- [oxd-server](https://gluu.org/docs/oxd/3.1.1/install/)


## Prerequisites

Ubuntu 14.04 with some basic utilities listed below:

```bash
$ sudo apt-get install apache2
$ a2enmod ssl
```


**Install RVM and Ruby on Ubuntu**

Install mpapis public key (might need gpg2):

```bash
$ sudo gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
```

Install RVM Stable with Ruby:

```bash
$ sudo \curl -sSL https://get.rvm.io | bash -s stable --ruby
$ cd /var/www/html
```

Reload shell configuration and test:
```bash
$ source ~/.rvm/scripts/rvm
```

To get a list of all the Ruby versions installed on your system:
```bash
$ rvm list
```

Using RVM you can install a specific Ruby version:
```bash
$ rvm install ruby-2.3.0
```

To change Ruby version run this command:
```bash
$ rvm use ruby-2.3.0
```

Rails is distributed as a Ruby gem and adding it to the local system is extremely simple:
```bash
$ gem install rails 
```

!!! Note
	For more help you can see RVM commands here : https://rvm.io/rvm/install

**Phusion Passenger Setup**

Phusion Passenger (commonly shortened to Passenger or referred to as mod_passenger) is an application server and is often used to power Ruby sites. Its code is distributed in the form of a gem, which is then compiled on the target machine and installed into Apache as a module.

First, the gem needs to be installed on the system:
```bash
$ gem install passenger
```

The environment is now ready for the compilation. Enter the following to start the process (the process may take a few minutes):
```bash
$ passenger-install-apache2-module
```

!!! Note 
	This script will not install the module. It compiles the module’s binary and places it in the gem’s path. The path will be printed on the screen and it needs to be copy-pasted into Apache’s config file. 

The output will be similar to this one:
```
LoadModule passenger_module /home/username/.rvm/gems/ruby-2.2.1/gems/passenger-5.0.28/buildout/apache2/mod_passenger.so
```

Add these lines to Apache's config file:
```
<IfModule mod_passenger.c>
 PassengerRoot /home/username/.rvm/gems/ruby-2.2.1/gems/passenger-5.0.28
 PassengerDefaultRuby /home/username/.rvm/gems/ruby-2.2.1/wrappers/ruby
</IfModule>
```


## Configuring oxd-server

- Edit the file `/opt/oxd-server/conf/oxd-conf.json` 

    Change the OP HOST name to your OpenID Provider domain at the line `"op_host": "https://<idp-hostname>"`

- Edit the file `/opt/oxd-server/conf/oxd-default-site-config.json`

    Change the `response_types` line to `"response_types": ["code"]`

- Start oxd-server, as described [here](../install/index.md):
<!--
- To start oxd-server, run the following command:

```bash
/etc/init.d/oxd-server start
```
-->


## Demosite Deployment

OpenID Connect only works with HTTPS connections. Enter the following to prepare the SSL certificates:

```bash
$ mkdir /etc/certs
$ cd /etc/certs
$ openssl genrsa -des3 -out demosite.key 2048
$ openssl rsa -in demosite.key -out demosite.key.insecure
$ mv demosite.key.insecure demosite.key
$ openssl req -new -key demosite.key -out demosite.csr
$ openssl x509 -req -days 365 -in demosite.csr -signkey demosite.key -out demosite.crt
```

Create a virtual host of oxd-ruby, oxd-ruby.conf under /etc/apache2/sites-available/ file and add these lines:

```bash
$ sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/oxd-ruby.conf
$ sudo vi /etc/apache2/sites-available/oxd-ruby.conf
```

Replace the content in the oxd-ruby.conf file with the following:

```
<IfModule mod_ssl.c>
	<VirtualHost *:443>
		ServerAdmin webmaster@localhost
		ServerName oxd-rails.com
		DocumentRoot /var/www/html/oxdrails

		LogLevel info ssl:warn
		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

		#   SSL Engine Switch:
		#   Enable/Disable SSL for this virtual host.
		SSLEngine on
		SSLCertificateFile	/etc/certs/demosite.crt
		SSLCertificateKeyFile /etc/certs/demosite.key

		<Directory /var/www/html/oxdrails>
			AllowOverride All
            		Options Indexes FollowSymLinks
			Order allow,deny
			Allow from all
		</Directory>
	</VirtualHost>
</IfModule>
```

Enable `oxd-rails.com` virtual host by running:
```bash
$ sudo a2ensite oxd-ruby.conf 
```

In the console, add the domain name to the virtual host file:
```bash
$ sudo nano /etc/hosts
```

Add these lines to the virtual host file:
```
127.0.0.1 www.oxd-rails.com
127.0.0.1 oxd-rails.com
```

Reload the Apache server:
```bash
$ sudo service apache2 restart
```

**Running the Demo Application**

Navigate to Rails app root:
```bash
cd /var/www/html/oxdrails
```

Run:
```bash
bundle install
```

- With the oxd-server running, navigate to the URL's below to run the sample client application. To register a client in the oxd-server use the Setup Client URL. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, navigate to the Login URL for authentication.
    - Setup Client URL: https://client.example.com:portno
    - Login URL: https://client.example.com:portno
    - UMA URL: https://client.example.com:portno/uma

