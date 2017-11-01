# oxd-server Installation 

## System Requirements

oxd needs to be deployed on a server or VM with the following **minimum** requirements. 

|CPU Unit  |    RAM     |   Disk Space      | Processor Type |
|----------|------------|-------------------|----------------|
|       1  |    400MB     |   200MB            |  64 Bit        |

Note: **oxd requires Java version 1.8 or higher.**


## Linux Packages

When you install oxd via one of the linux packages, you get both the `oxd-server` and the `oxd-https-extension`. The instructions below will provide steps to start the `oxd-server`. If you want to enable the `oxd-https-extension` to enable RESTful calls to your `oxd-server` over the web, follow the instructions for [starting the `oxd-https-extension`](./oxd-https/start/index.md). 

Step 1: Find the proper linux package below.    

Step 2: After installation, [configure](../configuration/index.md) your oxd-server and add your license keys.     

Step 3: Run the following command to start your `oxd-server`:         

`/etc/init.d/oxd-server start`

Step 4: If you need to stop the `oxd-server`, run the following command:      

`/etc/init.d/oxd-server stop`


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

oxd is self-contained--you can just unzip the folder and run it. 

### Windows

1. Make a folder called `oxd-server` (or whatever you like)
 
2. Unzip the [zip distribution](http://ox.gluu.org/maven/org/xdi/oxd-server/3.1.1.Final/oxd-server-3.1.1.Final-distribution.zip)
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
 
2. `$ wget http://ox.gluu.org/maven/org/xdi/oxd-server/3.1.1.Final/oxd-server-3.1.1.Final-distribution.zip`

3. `$ unzip oxd-server-3.1.1.Final-distribution.zip`

4. Now configure oxd following the [configuration instructions](../configuration/index.md). 

5. `$ nohup bin/oxd-start.sh`

## Manual Build oxd-server Server

If you're a Java geek, you can build the oxd-server server using [Maven](http://maven.apache.org).

The code is available in [Github](https://github.com/GluuFederation/oxd). A zip file can be 
downloaded directly from [this link](https://ox.gluu.org/maven/org/xdi/oxd-server/3.1.1.Final/oxd-server-3.1.1.Final-distribution.zip). 

The following command can be run inside the oxd folder to run the build:

```
  $ mvn clean package
```
