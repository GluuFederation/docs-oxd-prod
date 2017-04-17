[TOC]

# oxd Server Installation/Configuration

The easiest way to install oxd is to use one of the Linux packages.

## Ubuntu 14.04(trusty)

```
# echo "deb https://repo.gluu.org/ubuntu/ trusty main" > /etc/apt/sources.list.d/gluu-repo.list
# curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
# apt-get update
# apt-get install gluu-oxd-server
# service gluu-oxd-server start
```

## Ubuntu 16.04(xenial)

```
echo "deb https://repo.gluu.org/ubuntu/ xenial main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/ubuntu/gluu-apt.key | apt-key add -
apt-get update
apt-get install gluu-oxd-server
service gluu-oxd-server start
```

## Debian 8 (Jessie)

```
echo "deb https://repo.gluu.org/debian/ jessie main" > /etc/apt/sources.list.d/gluu-repo.list
curl https://repo.gluu.org/debian/gluu-apt.key | apt-key add -
apt-get update
apt-get install gluu-oxd-server
service gluu-oxd-server start
```

## CentOS 6

```
# wget https://repo.gluu.org/centos/Gluu-centos6.repo -O /etc/yum.repos.d/Gluu.repo
# wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# yum clean all
# yum install gluu-oxd-server
# service gluu-oxd-server start
```

## CentOS 7

```
# wget https://repo.gluu.org/centos/Gluu-centos7.repo -O /etc/yum.repos.d/Gluu.repo
# wget https://repo.gluu.org/centos/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# yum clean all
# yum install gluu-oxd-server
# service gluu-oxd-server start
```

## RHEL 6

```
# wget https://repo.gluu.org/rhel/Gluu-rhel6.repo -O /etc/yum.repos.d/Gluu.repo
# wget https://repo.gluu.org/rhel/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
# yum clean all
# yum install gluu-oxd-server
# service gluu-oxd-server start
```

## RHEL 7

```
wget https://repo.gluu.org/rhel/Gluu-rhel7.repo -O /etc/yum.repos.d/Gluu.repo
wget https://repo.gluu.org/rhel/RPM-GPG-KEY-GLUU -O /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-GLUU
yum clean all
yum install gluu-oxd-server
service gluu-oxd-server start
```

## Manual installation

If you don't want to use one of the Unix packages, oxd is pretty easy to install. It requires
Java version 1.7 or higher. But otherwise it's self-contained, and you can just unzip the folder 
and run it.

It is not necessary to install oxd in Windows, it can be downloaded and run. The `oxd Server` is 
available for download from [maven repository](http://ox.gluu.org/maven/org/xdi/oxd-server).

### Windows

1. Make a folder called `oxd-server` (or whatever you like)
 
2. Unzip the [zip distribution](http://ox.gluu.org/maven/org/xdi/oxd-server/2.4.4/oxd-server-2.4.4-distribution.zip)
in the above folder you just created.

3. Run `oxd-server/bin/oxd-start.bat`

### Unix

1. Make a folder called `oxd-server` (or whatever you like), and `cd` to this folder
 
2. `$ wget http://ox.gluu.org/maven/org/xdi/oxd-server/2.4.4/oxd-server-2.4.4-distribution.zip`

3. `$ unzip oxd-server-2.4.4-distribution.zip`

4. `$ nohup bin/oxd-start.sh &`

## Manual Build oxd Server

If you're a Java geek, oxd server can be built using [Maven](http://maven.apache.org).

The code is available in [Github](https://github.com/GluuFederation/oxd). A zip file can be 
downloaded directly from [this link](https://github.com/GluuFederation/oxd/archive/master.zip). 

The following command can be run inside the oxd folder to run the build:

```
  $ mvn clean package
```

