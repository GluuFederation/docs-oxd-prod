oxd Operational Commands


## Start 

Run the following command to start your oxd server:

`/etc/init.d/oxd-server start`

## Stop 

Run the following command to stop your oxd server: 

`/etc/init.d/oxd-server stop`

## Uninstall

To uninstall on Ubuntu 14.04 (trusty)/Ubuntu 16.04 (xenial)/Debian 8 (Jessie), run the following command:

$ sudo apt-get remove oxd-server

To uninstall on CentOS 6/CentOS 7/RHEL 6/RHEL 7, run the following command: 

# yum remove oxd-server
Note

You must use `apt-get purge oxd-server` or `apt-get remove --purge oxd-server` to uninstall and remove all the folders and services of oxd server.
