# oxd-server Installation 

## System Requirements

oxd needs to be deployed on a server or VM with the following **minimum** requirements. 

|CPU Unit  |    RAM     |   Disk Space      | Processor Type |
|----------|------------|-------------------|----------------|
|       1  |    400MB     |   200MB            |  64 Bit        |

Note: **oxd requires Java version 1.8**


## Linux Packages

The oxd Linux packages provide an easy way to install the `oxd-server` and the `oxd-https-extension`. Follow the steps below to get started:

Step 1: Find the proper linux package below.     

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
apt-get install oxd-server=3.1.2-*
```

### Ubuntu 16.04 (xenial)

```
echo "deb https://repo.gluu.org/ubuntu/ xenial main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
apt-get update
apt-get install oxd-server=3.1.2-*
```

### Debian 8 (Jessie)

```
echo "deb https://repo.gluu.org/debian/ jessie main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/debian/gluu-apt.key | apt-key add -
apt-get update
apt-get install oxd-server=3.1.2-*
```

### CentOS 6

```
wget https://repo.gluu.org/centos/Gluu-centos6.repo -O /etc/yum.repos.d/Gluu.repo
wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
yum clean all
yum install oxd-server-3.1.2
```

### CentOS 7

```
wget https://repo.gluu.org/centos/Gluu-centos7.repo -O /etc/yum.repos.d/Gluu.repo
wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
yum clean all
yum install oxd-server-3.1.2
```

### RHEL 6

```
wget https://repo.gluu.org/rhel/Gluu-rhel6.repo -O /etc/yum.repos.d/Gluu.repo
wget https://repo.gluu.org/rhel/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
yum clean all
yum install oxd-server-3.1.2
```

### RHEL 7

```
wget https://repo.gluu.org/rhel/Gluu-rhel7.repo -O /etc/yum.repos.d/Gluu.repo
wget https://repo.gluu.org/rhel/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
yum clean all
yum install oxd-server-3.1.2
```


## Manual installation

The oxd-server is a self-contained program. You can just unzip the folder and run it. 

!!! Note 
    To **support RESTful calls to your `oxd-server`**, after you complete manual installation below proceed to the [`oxd-https-extension` installation docs](../oxd-https/start/index.md#manual-installation). 

### Windows

1. Make a folder called `oxd-server` (or whatever you like)
 
2. Unzip the [zip distribution](http://ox.gluu.org/maven/org/xdi/oxd-server/3.1.2.Final/oxd-server-3.1.2.Final-distribution.zip)
in the above folder you just created. 

3. Now configure oxd following the [configuration instructions](../configuration/index.md). 

4. Run `oxd-server/bin/oxd-start.bat`

Note that under Windows you may need to change default h2 database location path, to something like
```json
 "storage_configuration": {
    "dbFileLocation":"c:\\opt\\oxd-server\\bin\\oxd_db"
  }
```

### Unix

1. Make a folder called `oxd-server` (or whatever you like), and `cd` to this folder
 
2. `$ wget http://ox.gluu.org/maven/org/xdi/oxd-server/3.1.2.Final/oxd-server-3.1.2.Final-distribution.zip`

3. `$ unzip oxd-server-3.1.2.Final-distribution.zip`

4. Now configure oxd following the [configuration instructions](../configuration/index.md). 

5. `$ nohup bin/oxd-start.sh`

## Manual Build oxd-server Server

If you're a Java geek, you can build the oxd-server server using [Maven](http://maven.apache.org).

The code is available in [Github](https://github.com/GluuFederation/oxd). A zip file can be 
downloaded directly from [this link](https://ox.gluu.org/maven/org/xdi/oxd-server/3.1.2.Final/oxd-server-3.1.2.Final-distribution.zip). 

The following command can be run inside the oxd folder to run the build:

```
  $ mvn clean package
```
## oxd-server Un-Install Procedure

### Ubuntu 14.04 (trusty)/Ubuntu 16.04 (xenial)/Debian 8 (Jessie)


```
$ sudo apt-get remove oxd-server
```

### CentOS 6/CentOS 7/RHEL 6/RHEL 7

```
# yum remove oxd-server
```
