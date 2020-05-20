# Using ldap storage in oxd

## Overview

The idea of using using `ldap` (or `Couchbase`) storage in oxd came so that oxd and Gluu Server can have common persistance. This is particularly useful when configuring high availability (HA) across multiple instances of the Gluu Server. And oxd can also be configured to use any remote or local `ldap` (which is not used by Gluu server) as storage.

## Using `ldap` used by Gluu server in oxd

In Gluu CE server the persistance configuration files are stored at location `/etc/gluu/conf/`. To configure 
