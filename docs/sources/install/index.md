# oxd-server Installation 

## System Requirements

oxd needs to be deployed on a server or VM with the following **minimum** requirements. 

|CPU Unit  |    RAM     |   Disk Space      | Processor Type |
|----------|------------|-------------------|----------------|
|       1  |    400MB     |   200MB            |  64 Bit        |

!!! Note
    **oxd requires Java version 1.8**

oxd 4.0 works with CE 3.1.6 and above.

## Linux Packages

Before the installation, make sure that Java 8 is installed on your OS. Please check it with
```
# java -version
java version "1.8.0_181"
```
If the above command confirms that Java 8 is installed, you can go on with the oxd installation.

The oxd Linux packages provide an easy way to install the `oxd-server`. Follow the steps below to get started:

Step 1: Find the proper Linux package below.     

Step 2: After the installation, [configure](../configuration/index.md) your oxd server.

Step 3: Run oxd server:             
 
`/etc/init.d/oxd-server start`

!!! Note
    If you need to stop your `oxd-server`at any point, you can run the following command: `/etc/init.d/oxd-server stop` 

### Ubuntu 16.04 (xenial)

```
echo "deb https://repo.gluu.org/ubuntu/ xenial main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
apt-get update
apt-get install oxd-server
```

### Debian 9 (Jessie)

```
echo "deb https://repo.gluu.org/debian/ jessie main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/debian/gluu-apt.key | apt-key add -
apt-get update
apt-get install oxd-server
```

### CentOS 7

```
wget https://repo.gluu.org/centos/Gluu-centos7.repo -O /etc/yum.repos.d/Gluu.repo
wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
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

## Service Operations

**Ubuntu 16.04 (xenial)**

|Operation | Command|
|------ |------ |
|Start oxd server | `/etc/init.d/oxd-server start` |
|Stop oxd server | `/etc/init.d/oxd-server stop` |
|oxd server status | `/etc/init.d/oxd-server status`|
|Restart oxd server | `/etc/init.d/oxd-server restart` |

**Ubuntu 18.04 (bionic)/Debian 9 (stretch)/CentOS 7/RHEL 7**

|Operation | Command|
|------ |------ |
|Start oxd server | `systemctl start oxd-server` |
|Stop oxd server | `systemctl stop oxd-server` |
|oxd server status | `systemctl status oxd-server`|
|Restart oxd server | `systemctl restart oxd-server` |

## Manual installation

The oxd-server is a self-contained program.  

To run oxd-server:

1. download the jar file: http://ox.gluu.org/maven/org/xdi/oxd-server/4.0-SNAPSHOT/oxd-server-4.0-SNAPSHOT.jar

1. download the jar file: http://ox.gluu.org/maven/org/bouncycastle/bcprov-jdk15on/1.54/bcprov-jdk15on-1.54.jar

1. download the yml file: https://raw.githubusercontent.com/GluuFederation/oxd/version_4.0/oxd-server/src/main/resources/oxd-server.yml

1. download the keystore file: https://github.com/GluuFederation/oxd/raw/version_4.0/oxd-server/src/main/resources/oxd-server.keystore

1. put it in one directory, correct `oxd-server.yml` if needed, move inside it and run the following (java 1.8 is required):

Windows:
``` 
java -Djava.net.preferIPv4Stack=true -cp bcprov-jdk15on-1.54.jar;oxd-server-4.0-SNAPSHOT.jar org.xdi.oxd.server.OxdServerApplication server oxd-server.yml
```

Unix:
``` 
java -Djava.net.preferIPv4Stack=true -cp bcprov-jdk15on-1.54.jar:oxd-server-4.0-SNAPSHOT.jar org.xdi.oxd.server.OxdServerApplication server oxd-server.yml
```

## Manual Build oxd-server Server

If you're a Java geek, you can build the oxd-server server using [Maven](http://maven.apache.org).

The code is available in [Github](https://github.com/GluuFederation/oxd).

The following command can be run inside the oxd folder to run the build:

```
  $ mvn clean package
```

## oxd-server Uninstall Procedure

### Ubuntu 16.04 (xenial)

```
/etc/init.d/oxd-server stop
sudo apt-get remove oxd-server
apt-get purge oxd-server
```

### Ubuntu 18.04 (bionic)/Debian 9 (stretch)

```
systemctl stop oxd-server
sudo apt-get remove oxd-server
apt-get purge oxd-server
```

### CentOS 7/RHEL 7

```
systemctl stop oxd-server
yum remove oxd-server
rm -rf /opt/oxd-server.save
```

## Utility scripts

### View, delete entries inside the oxd-server database with lsox.sh or lsox.bat scripts

There are four types of parameters which can be used by lsox.sh/lsox.bat files:

 - `-l` - list all oxd_ids inside the oxd database
 - `-oxd_id <oxd_id>` - view JSON representation of the entity by oxd_id
 - `-d` - removes entity by oxd_id.
 - `-a` - authorization `access_token` (e.g. `lsox.sh -a gf4566-dlt456-emtr56-ddmg5kd`). It is optional if oxd is not running. If it is running then it is REQUIRED. This is true if h2 database is used for persistence which is default. The reason is that if oxd is running it locks h2 database file. The lock is exclusive, so script can't access the file while oxd is running. To handle it script needs authorization via `-a` parameter. Authorization means `access_token` obtained via `/get-client-token` API call (same as Authorization header value used for all API calls).

The script is located in `/opt/oxd-server/bin/lsox.sh`. If you hit the script without any parameters, it shows a hint:
```
yuriy@yuriyz:~/oxd-server-distribution/bin$ sh lsox.sh
BASEDIR=.
CONF=./../conf/oxd-server.yml
Missing required option: oxd_id
usage: utility-name
 -oxd_id,--oxd_id <arg>   oxd_id is unique identifier within oxd database
                          (returned by register_site and setup_client
                          commands)
 -l,--list                list all oxd_ids inside oxd database
 -d,--delete              deletes entry from oxd database                         

```

A typical call looks like this:
```
yuriy@yuriyz:~/oxd-server-4.0-SNAPSHOT-distribution/bin$ sh lsox.sh -oxd_id d8cc6dea-4d66-4995-b6e1-da3a33722f2e -a gf4566-dlt456-emtr56-ddmg5kd
BASEDIR=.
CONF=./../conf/oxd-server.yml

yuriy@yuriyz:~/oxd-server-4.0-SNAPSHOT-distribution/bin$JSON for oxd_id d8cc6dea-4d66-4995-b6e1-da3a33722f2e
{"scope":["openid","uma_protection","profile"],"contacts":[],"pat":null,"rpt":null,"oxd_id":"d8cc6dea-4d66-4995-b6e1-da3a33722f2e","op_host":"https://ce-dev4.gluu.org","op_discovery_path":null,"id_token":null,"access_token":null,"logout_redirect_uri":"https://client.example.com/cb","application_type":"web","redirect_uris":["https://client.example.com/cb"],"claims_redirect_uri":[],"response_types":["code"],"front_channel_logout_uri":[""],"client_id":"@!38D4.410C.1D43.8932!0001!37F2.B744!0008!B390.5F6D.2051.A8C0","client_secret":"4a72e386-97ed-49a0-a338-cd448e5020b3","client_registration_access_token":"920fdb64-9bd7-4b5f-8a8c-8689e29860b8","client_registration_client_uri":"https://ce-dev4.gluu.org/oxauth/restv1/register?client_id=@!38D4.410C.1D43.8932!0001!37F2.B744!0008!B390.5F6D.2051.A8C0","client_id_issued_at":1528879584000,"client_secret_expires_at":1528965984000,"client_name":null,"sector_identifier_uri":null,"client_jwks_uri":null,"token_endpoint_auth_signing_alg":null,"token_endpoint_auth_method":null,"is_setup_client":null,"setup_oxd_id":null,"setup_client_id":null,"ui_locales":["en"],"claims_locales":["en"],"acr_values":[""],"grant_types":["authorization_code","urn:ietf:params:oauth:grant-type:uma-ticket","client_credentials"],"user_id":null,"user_secret":null,"pat_expires_in":0,"pat_created_at":null,"pat_refresh_token":null,"uma_protected_resources":[],"rpt_token_type":null,"rpt_pct":null,"rpt_upgraded":null,"rpt_expires_at":null,"rpt_created_at":null,"oxd_rp_programming_language":"java"}
```
