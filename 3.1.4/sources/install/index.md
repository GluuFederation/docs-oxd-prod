# oxd-server Installation 

## System Requirements

oxd needs to be deployed on a server or VM with the following **minimum** requirements. 

|CPU Unit  |    RAM     |   Disk Space      | Processor Type |
|----------|------------|-------------------|----------------|
|       1  |    400MB     |   200MB            |  64 Bit        |

Note: **oxd requires Java version 1.8**


## Linux Packages

The oxd Linux packages provide an easy way to install the `oxd-server` and the `oxd-https-extension`. Follow the steps below to get started:

Step 1: Find the proper Linux package below.     

Step 2: After installation, [configure](../configuration/index.md) your oxd-server and add your license keys. If you need to obtain a license, [register on the oxd website](https://oxd.gluu.org).      

Step 3: Run the following command to start your `oxd-server`:             
 
`/etc/init.d/oxd-server start`

Step 4: To support RESTful (https) calls to your `oxd-server`, you can now move on to the [oxd-https-extension docs](../oxd-https/start/index.md).      

!!! Note
    If you need to stop your `oxd-server`at any point, you can run the following command: `/etc/init.d/oxd-server stop` 


### Ubuntu 14.04 (trusty)

```
echo "deb https://repo.gluu.org/ubuntu/ trusty main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
apt-get update
apt-get install oxd-server
```

### Ubuntu 16.04 (xenial)

```
echo "deb https://repo.gluu.org/ubuntu/ xenial main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
apt-get update
apt-get install oxd-server
```

### Debian 8 (Jessie)

```
echo "deb https://repo.gluu.org/debian/ jessie main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/debian/gluu-apt.key | apt-key add -
apt-get update
apt-get install oxd-server
```

### CentOS 6

```
wget https://repo.gluu.org/centos/Gluu-centos6.repo -O /etc/yum.repos.d/Gluu.repo
wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
yum clean all
yum install oxd-server
```

### CentOS 7

```
wget https://repo.gluu.org/centos/Gluu-centos7.repo -O /etc/yum.repos.d/Gluu.repo
wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
yum clean all
yum install oxd-server
```

### RHEL 6

```
wget https://repo.gluu.org/rhel/Gluu-rhel6.repo -O /etc/yum.repos.d/Gluu.repo
wget https://repo.gluu.org/rhel/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
yum clean all
yum install oxd-server
```

### RHEL 7

```
wget https://repo.gluu.org/rhel/Gluu-rhel7.repo -O /etc/yum.repos.d/Gluu.repo
wget https://repo.gluu.org/rhel/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
yum clean all
yum install oxd-server
```


## Manual installation

The oxd-server is a self-contained program. You can just unzip the folder and run it. 

!!! Note 
    To **support RESTful calls to your `oxd-server`**, after you complete manual installation below, proceed to the [`oxd-https-extension` installation docs](../oxd-https/start/index.md#manual-installation). 

### Windows

1. Make a folder called `oxd-server` (or whatever you like)
 
1. Unzip the [zip distribution](http://ox.gluu.org/maven/org/xdi/oxd-server/3.1.3.Final/oxd-server-3.1.3.Final-distribution.zip) in the above folder you just created. 

1. Now configure oxd following the [configuration instructions](../configuration/index.md). 

1. Run `oxd-server/bin/oxd-start.bat`

Note that under Windows, you may need to change the default h2 database location path, to something like
```json
 "storage_configuration": {
    "dbFileLocation":"c:\\opt\\oxd-server\\bin\\oxd_db"
  }
```

### Unix

1. Make a folder called `oxd-server` (or whatever you like), and `cd` to this folder
 
1. `$ wget http://ox.gluu.org/maven/org/xdi/oxd-server/3.1.3.Final/oxd-server-3.1.3.Final-distribution.zip`

1. `$ unzip oxd-server-3.1.3.Final-distribution.zip`

1. Now configure oxd following the [configuration instructions](../configuration/index.md). 

1. `$ chmod +x bin/oxd-start.sh`

1. `$ nohup bin/oxd-start.sh`

## Manual Build oxd-server Server

If you're a Java geek, you can build the oxd-server server using [Maven](http://maven.apache.org).

The code is available in [Github](https://github.com/GluuFederation/oxd). A zip file can be 
downloaded directly from [this link](https://ox.gluu.org/maven/org/xdi/oxd-server/3.1.3.Final/oxd-server-3.1.3.Final-distribution.zip). 

The following command can be run inside the oxd folder to run the build:

```
  $ mvn clean package
```
## oxd-server Uninstall Procedure

### Ubuntu 14.04 (trusty)/Ubuntu 16.04 (xenial)/Debian 8 (Jessie)


```
$ sudo apt-get remove oxd-server
```

### CentOS 6/CentOS 7/RHEL 6/RHEL 7

```
# yum remove oxd-server
```

## Utility scripts

### View, delete entries inside oxd-server database with lsox.sh or lsox.bat scripts

There are three type of parameters which can be used by lsox.sh/lsox.bat files:
 - `-l` - list all oxd_ids inside oxd database
 - `-oxd_id <oxd_id>` - view JSON representation of the entity by oxd_id
 - `-d` - removes entity by oxd_id.

Script is located in `/opt/oxd-server/bin/lsox.sh`. If hit script without parameters it show hint
```
yuriy@yuriyz:~/oxd-server-distribution/bin$ sh lsox.sh
BASEDIR=.
CONF=./../conf/oxd-conf.json
Missing required option: oxd_id
usage: utility-name
 -oxd_id,--oxd_id <arg>   oxd_id is unique identifier within oxd database
                          (returned by register_site and setup_client
                          commands)
 -l,--list                list all oxd_ids inside oxd database
 -d,--delete              deletes entry from oxd database                         

```

Typical call looks as
```
yuriy@yuriyz:~/oxd-server-3.1.4-SNAPSHOT-distribution/bin$ sh oxd-show.sh -oxd_id d8cc6dea-4d66-4995-b6e1-da3a33722f2e
BASEDIR=.
CONF=./../conf/oxd-conf.json

yuriy@yuriyz:~/oxd-server-3.1.4-SNAPSHOT-distribution/bin$JSON for oxd_id d8cc6dea-4d66-4995-b6e1-da3a33722f2e
{"scope":["openid","uma_protection","profile"],"contacts":[],"pat":null,"rpt":null,"oxd_id":"d8cc6dea-4d66-4995-b6e1-da3a33722f2e","op_host":"https://ce-dev4.gluu.org","op_discovery_path":null,"id_token":null,"access_token":null,"authorization_redirect_uri":"https://client.example.com/cb","logout_redirect_uri":"https://client.example.com/cb","application_type":"web","redirect_uris":["https://client.example.com/cb"],"claims_redirect_uri":[],"response_types":["code"],"front_channel_logout_uri":[""],"client_id":"@!38D4.410C.1D43.8932!0001!37F2.B744!0008!B390.5F6D.2051.A8C0","client_secret":"4a72e386-97ed-49a0-a338-cd448e5020b3","client_registration_access_token":"920fdb64-9bd7-4b5f-8a8c-8689e29860b8","client_registration_client_uri":"https://ce-dev4.gluu.org/oxauth/restv1/register?client_id=@!38D4.410C.1D43.8932!0001!37F2.B744!0008!B390.5F6D.2051.A8C0","client_id_issued_at":1528879584000,"client_secret_expires_at":1528965984000,"client_name":null,"sector_identifier_uri":null,"client_jwks_uri":null,"token_endpoint_auth_signing_alg":null,"token_endpoint_auth_method":null,"is_setup_client":null,"setup_oxd_id":null,"setup_client_id":null,"ui_locales":["en"],"claims_locales":["en"],"acr_values":[""],"grant_types":["authorization_code","urn:ietf:params:oauth:grant-type:uma-ticket","client_credentials"],"user_id":null,"user_secret":null,"pat_expires_in":0,"pat_created_at":null,"pat_refresh_token":null,"uma_protected_resources":[],"rpt_token_type":null,"rpt_pct":null,"rpt_upgraded":null,"rpt_expires_at":null,"rpt_created_at":null,"oxd_rp_programming_language":"java"}
```