# oxd-java-spring

Use oxd's Java Spring library to send users from a Spring application to your Gluu Server OpenID Connect Provider (OP) for dynamic enrollment, single sign-on (SSO), strong authentication and access management policy enforcement.  

!!! Note
    You can also refer to the [oxd-java library docs](../languages/java/) for more details on Java classes.


## Installation Guides

- [Github oxd-node](https://github.com/GluuFederation/oxd-node)
- [Gluu Server](https://gluu.org/docs/ce/3.1.3/installation-guide/install/)
- [oxd-server](../../../install/index.md)


## Software Requirements

System Requirements:

- Ubuntu / Debian / CentOS / RHEL / Windows Server 2008 or higher
- Java 7 or higher
- Apache 2.4.4 or higher

To use the oxd-java library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/) or Google    
- An active installation of the [oxd-server](../../../install/index.md) running in the same server as the client application
- If you want to make RESTful (https) calls from your app to your `oxd-server`, you will also need an active installation of the [oxd-https-extension](../../../oxd-https/start/index.md)  
- A Windows server or Windows installed machine / Linux server or Linux installed machine

## Configuring the oxd-server

- Edit the file `/opt/oxd-server/conf/oxd-conf.json` 

    - Update the following fields `"server_name"`, `"license_id"`, `"public_key"` and `"public_password"`

- Edit the file `/opt/oxd-server/conf/oxd-default-site-config.json`

    - Change the OP HOST name to your OpenID Provider domain at the line `"op_host": "https://<idp-hostname>"`

    - Change the `response_types` line to `"response_types": ["code"]`

- To start oxd-server, run the following command or [click here](../../../install/index.md) for more detailed instructions:

```bash
/etc/init.d/oxd-server start
```

## Demosite Deployment

**Install oxd-spring**

Your client application must have a valid SSL certificate, so the URL includes: `https://`    

The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP address. You can configure the hostname by adding the following entry in the host file:

**Linux**

Host file location `/etc/host` :

`127.0.0.1  client.example.com`  

**Windows**

Host file location `C:\Windows\System32\drivers\etc\host` :

`127.0.0.1  client.example.com`

Clone oxd-spring from Github repo and run maven command to install it:
```
cd oxd-spring 
mvn clean package -Dmaven.test.skip=true
```

Now you can run the executable jar:
```
java -jar target/oxd-spring-0.0.1-SNAPSHOT.jar
```

Point browser to `https://client.example.com:8443/` and log into ce-dev3.gluu.org using test credentials: test_user/test_user_password 

!!!Note 
    oxd-server must run on _localhost_ and be bound to port: _8099_, otherwise you'll need to configure `oxd-spring/src/main/resources/application.properties` file.


**Customize oxd-spring**

To use your own server as an OpenID Provider you need to modify `oxd.server.op-host` property from `oxd-spring/src/main/resources/application.properties`, e.g:

```
oxd.server.op-host=https://gluu.localhost.info
```

Make sure the server already installed on your machine, or you can follow 
[this](https://gluu.org/docs/ce/latest/installation-guide/install/) guide to install it.

