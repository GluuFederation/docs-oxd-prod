# oxd-python-flask

Use oxd's Python Flask library to send users from a Flask application to your Gluu Server OpenID Connect Provider (OP) for dynamic enrollment, single sign-on (SSO), strong authentication, and access management policy enforcement. 

!!! Note:
    You can also refer to the [oxd-python library docs](../../languages/python/index.md) for more details on python classes.

## Installation Guides

- [Github oxd-python](https://github.com/GluuFederation/oxd-python)
- [Gluu Server](https://gluu.org/docs/ce/3.1.3/installation-guide/install/)
- [oxd-server](../../../install/index.md)


## Software Requirements

System Requirements:

Ubuntu 14.04 with some basic utilities listed below:

```bash
apt-get install apache2 libapache2-mod-wsgi python-dev git python-pip
a2enmod wsgi
a2enmod ssl
```

Gluu development binaries:

```bash
echo "deb https://repo.gluu.org/ubuntu/ trusty main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
apt-get update
apt-get install oxd-server
```

To use the oxd-python library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google    
- An active installation of the [oxd-server](../../../install/index.md) running on the same server as the client application
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


- Install oxd-python in the Client server:

```bash
git clone https://github.com/GluuFederation/oxd-python.git
cd oxd-python
python setup.py install
```

- Switch to the demosite folder

```bash
cd examples/flask_app
```

- Edit the `demosite.cfg` file. Add the URI of the OpenID Provider (OP) in the `op_host` field, e.g. `https://idp.example.com`. If `op_host` was configured during installation, this step can be skipped.
  
- Edit `/etc/hosts` and point `client.example.com` at the IP Address of the server where the demo app is installed, e.g. `100.100.100.100 client.example.com`

- Run the demo server

```bash
pip install flask
pip install pyOpenSSL
python demosite.py
```

Now the demosite can be accessed at [https://client.example.com:8080](https://client.example.com:8080)
