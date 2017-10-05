# oxd-server Server Installation 

## Linux Packages

### Ubuntu 14.04 (trusty)

```
# echo "deb https://repo.gluu.org/ubuntu/ trusty main" > /etc/apt/sources.list.d/gluu-repo.list
# curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
# apt-get update
# apt-get install gluu-oxd-server
```
Now configure oxd-server following the [configuration instructions](../configuration/index.md). 

Then run the following command to start the oxd-server server:

`# service gluu-oxd-server start`


### Ubuntu 16.04 (xenial)

```
echo "deb https://repo.gluu.org/ubuntu/ xenial main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
apt-get update
apt-get install gluu-oxd-server
```
Now configure oxd following the [configuration instructions](../configuration/index.md). 

Then run the following command to start the oxd server:

`service gluu-oxd-server start`

### Debian 8 (Jessie)

```
echo "deb https://repo.gluu.org/debian/ jessie main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/debian/gluu-apt.key | apt-key add -
apt-get update
apt-get install gluu-oxd-server
```
Now configure oxd following the [configuration instructions](../configuration/index.md). 

Then run the following command to start the oxd server:

`service gluu-oxd-server start`

### CentOS 6

```
# wget https://repo.gluu.org/centos/Gluu-centos6.repo -O /etc/yum.repos.d/Gluu.repo
# wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# yum clean all
# yum install gluu-oxd-server
```
Now configure oxd following the [configuration instructions](../configuration/index.md). 

Then run the following command to start the oxd server:

`# service gluu-oxd-server start`

### CentOS 7

```
# wget https://repo.gluu.org/centos/Gluu-centos7.repo -O /etc/yum.repos.d/Gluu.repo
# wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# yum clean all
# yum install gluu-oxd-server
```
Now configure oxd following the [configuration instructions](../configuration/index.md). 

Then run the following command to start the oxd server:

`# service gluu-oxd-server start`

### RHEL 6

```
# wget https://repo.gluu.org/rhel/Gluu-rhel6.repo -O /etc/yum.repos.d/Gluu.repo
# wget https://repo.gluu.org/rhel/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# yum clean all
# yum install gluu-oxd-server
```
Now configure oxd following the [configuration instructions](../configuration/index.md). 

Then run the following command to start the oxd server:

`# service gluu-oxd-server start`

### RHEL 7

```
wget https://repo.gluu.org/rhel/Gluu-rhel7.repo -O /etc/yum.repos.d/Gluu.repo
wget https://repo.gluu.org/rhel/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
yum clean all
yum install gluu-oxd-server
```
Now configure oxd following the [configuration instructions](../configuration/index.md). 

Then run the following command to start the oxd server:

`service gluu-oxd-server start`

## Manual installation

If you don't want to use one of the Linux packages, oxd is pretty easy to install. It requires
Java version 1.7 or higher. But otherwise it's self-contained, and you can just unzip the folder 
and run it.

It is not necessary to install oxd in Windows, it can be downloaded and run. The `oxd Server` is 
available for download from [maven repository](http://ox.gluu.org/maven/org/xdi/oxd-server).

### Windows

1. Make a folder called `oxd-server` (or whatever you like)
 
2. Unzip the [zip distribution](http://ox.gluu.org/maven/org/xdi/oxd-server/3.1.1.Final/oxd-server-3.1.1.Final-distribution.zip)
in the above folder you just created. 

3. Now configure oxd following the [configuration instructions](../configuration/index.md). 

4. Run `oxd-server/bin/oxd-start.bat`

### Unix

1. Make a folder called `oxd-server` (or whatever you like), and `cd` to this folder
 
2. `$ wget http://ox.gluu.org/maven/org/xdi/oxd-server/3.1.1.Final/oxd-server-3.1.1.Final-distribution.zip`

3. `$ unzip oxd-server-3.1.1.Final-distribution.zip`

4. Now configure oxd following the [configuration instructions](../configuration/index.md). 

5. `$ nohup bin/oxd-start.sh &`

## Manual Build oxd-server Server

If you're a Java geek, you can build the oxd-server server using [Maven](http://maven.apache.org).

The code is available in [Github](https://github.com/GluuFederation/oxd). A zip file can be 
downloaded directly from [this link](https://ox.gluu.org/maven/org/xdi/oxd-server/3.1.1.Final/oxd-server-3.1.1.Final-distribution.zip). 

The following command can be run inside the oxd folder to run the build:

```
  $ mvn clean package
```