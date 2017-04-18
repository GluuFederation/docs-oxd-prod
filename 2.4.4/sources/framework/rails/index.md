# oxd Ruby On Rails Demo site

The following documentation shows how to configure Ruby on Rails apps to use oxd for authentication. 

## Deployment

### Prerequisites

Ubuntu 14.04 with some basic utilities listed below

```bash
$ sudo apt-get install apache2
$ a2enmod ssl
```

### Installing and configuring the oxd-server
You can download the oxd-server and follow the installation instructions from 
[here](https://www.gluu.org/docs-oxd)

## Demosite deployment

OpenID Connect works only with HTTPS connections. So let us get the ssl certs ready.
```bash
$ mkdir /etc/certs
$ cd /etc/certs
$ openssl genrsa -des3 -out demosite.key 2048
$ openssl rsa -in demosite.key -out demosite.key.insecure
$ mv demosite.key.insecure demosite.key
$ openssl req -new -key demosite.key -out demosite.csr
$ openssl x509 -req -days 365 -in demosite.csr -signkey demosite.key -out demosite.crt
```

###Install RVM and Ruby on ubuntu

Install mpapis public key first (might need gpg2) 

```bash
$ sudo gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
```

Install RVM stable with ruby:

```bash
$ sudo \curl -sSL https://get.rvm.io | bash -s stable --ruby
$ cd /var/www/html
```

Reload shell configuration & test
```bash
$ source ~/.rvm/scripts/rvm
```

Run this command in terminal to get list of installed ruby versions on your system
```bash
$ rvm list
```

Using rvm you can install specific ruby version. E.g. for ruby-2.2.0 :
```bash
$ rvm install ruby-2.2.0
```

To change ruby version simply run this command. E.g. to switch to ruby-2.2.0 :
```bash
$ rvm use ruby-2.2.0
```

Rails is distributed as a Ruby gem and adding it to the local system is extremely simple:
```bash
$ gem install rails 
```

For more help you can see rvm commands here :
https://rvm.io/rvm/install

###Phusion Passenger Setup 

Phusion Passenger (commonly shortened to Passenger or referred to as mod_passenger) is an application server and it is often used to power Ruby sites. Its code is distributed in form of a Ruby gem, which is then compiled on the target machine and installed into Apache as a module.

First, the gem needs to be installed on the system:
```bash
$ gem install passenger
```

The environment is now ready for the compilation. The process takes a few minutes and it’s started by the following command:
```bash
$ passenger-install-apache2-module
```

Note that this script will not install the module really. It will compile module’s binary and place it under gem’s path. The path will be printed on screen and it needs to be copy-pasted into Apache’s config file 

The output will be similar to this one:
```
LoadModule passenger_module /home/username/.rvm/gems/ruby-2.2.1/gems/passenger-5.0.28/buildout/apache2/mod_passenger.so
```

Then in Apache's config file add these lines :
```
<IfModule mod_passenger.c>
 PassengerRoot /home/username/.rvm/gems/ruby-2.2.1/gems/passenger-5.0.28
 PassengerDefaultRuby /home/username/.rvm/gems/ruby-2.2.1/wrappers/ruby
</IfModule>
```

In console :
```bash
$ sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/oxd-ruby.conf
$ sudo vi /etc/apache2/sites-available/oxd-ruby.conf
```
Replace the content of oxd-ruby.conf file with following:
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

Then enable `oxd-rails.com` virtual host by running:
```bash
$ sudo a2ensite oxd-ruby.conf 
```

After that add domain name in virtual host file.
In console:
```bash
$ sudo nano /etc/hosts
```

Add these lines in virtual host file:
```
127.0.0.1 www.oxd-rails.com
127.0.0.1 oxd-rails.com
```

Reload the apache server
```bash
$ sudo service apache2 restart
```
### Setting up and running demo app

Navigate to Rails app root:
```bash
cd /var/www/html/oxdrails
```

Run :
```bash
bundle install
```

Now your rails app should work from http://oxd-rails.com

##Enjoy!
