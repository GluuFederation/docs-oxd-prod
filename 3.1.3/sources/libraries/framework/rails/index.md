# oxd Ruby On Rails

Use oxd's Ruby on Rails library to send users from a Rails application to your Gluu Server OpenID Connect Provider (OP) for dynamic enrollment, single sign-on (SSO), strong authentication, and access management policy enforcement. 

!!! Note
    You can also refer to the [oxd-Ruby library docs](../../languages/ruby/index.md) for more details on Ruby classes.

## Installation Guides

- [Github oxd-ruby](https://github.com/GluuFederation/oxd-ruby)
- [Gluu Server](https://gluu.org/docs/ce/3.1.3/installation-guide/install/)
- [oxd-server](../../../install/index.md)


## Software Requirements

System Requirements:

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
$ rvm install ruby-2.4.0
```

To change Ruby version run this command:
```bash
$ rvm use ruby-2.4.0
```

Rails is distributed as a Ruby gem, and adding it to the local system is extremely simple:
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
LoadModule passenger_module /home/username/.rvm/gems/ruby-2.4.0/gems/passenger-5.2.1/buildout/apache2/mod_passenger.so
```

Add these lines to Apache's config file:
```
<IfModule mod_passenger.c>
 PassengerRoot /home/username/.rvm/gems/ruby-2.4.0/gems/passenger-5.2.1
 PassengerDefaultRuby /home/username/.rvm/gems/ruby-2.4.0/wrappers/ruby
</IfModule>
```

To use the oxd-ruby library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/) or Google
- An active installation of the [oxd-server](../../../install/index.md) 
- If you want to make RESTful (https) calls from your app to your `oxd-server`, you will also need an active installation of the [oxd-https-extension](../../../oxd-https/start/index.md)
- A Windows server or Windows installed machine / Linux server or Linux installed machine


## Configuring oxd-server

- Edit the file `/opt/oxd-server/conf/oxd-conf.json` 

    - Update the following fields `"server_name"`, `"license_id"`, `"public_key"`, `"public_password"` and `"license_password"`

- Edit the file `/opt/oxd-server/conf/oxd-default-site-config.json`

    - Change the OP HOST name to your OpenID Provider domain at the line `"op_host": "https://<idp-hostname>"`

    - Change the `response_types` line to `"response_types": ["code"]`

- To start oxd-server, run the following command or [click here](../../../install/index.md) for more detailed instructions:

```bash
/etc/init.d/oxd-server start
```

## Demosite Deployment

Get the source code for demosite:

```bash
cd /var/www/html
git clone https://github.com/GluuFederation/oxd-ruby-demo-app
```

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

Create a virtual host of oxd-ruby:
To create a virtual host for oxd-ruby-demo-app, create a oxd-ruby.conf file under /etc/apache2/sites-available/ with following commands:

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
		DocumentRoot /var/www/html/oxd-ruby-demo-app/public

		LogLevel info ssl:warn
		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

		#   SSL Engine Switch:
		#   Enable/Disable SSL for this virtual host.
		SSLEngine on
		SSLCertificateFile	/etc/certs/demosite.crt
		SSLCertificateKeyFile /etc/certs/demosite.key

		<Directory /var/www/html/oxd-ruby-demo-app/public>
			AllowOverride All
            Options Indexes FollowSymLinks
			Order allow,deny
			Allow from all
		</Directory>
	</VirtualHost>
</IfModule>
```

Enable the `oxd-rails.com` virtual host by running:
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

Navigate to the Rails app root:
```bash
cd /var/www/html/oxd-ruby-demo-app
```

Run:
```bash
bundle install
```

- Change the `oxd-ruby` configuration in `oxd-ruby-demo-app/config/initializers/oxd_config.rb` file.

- With the `oxd-server` running, navigate to the `https://oxd-rails.com` to run the sample client application. `oxd-ruby-demo-app` contains a nice UI with buttons and template code to walk you through the authentication flow with each `oxd-ruby` command. To register a client in the oxd-server use the `"Setup Client"` button. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, use the `"Login with OpenId"` button to navigate to the Login URL for authentication.
