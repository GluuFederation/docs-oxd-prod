# Using ldap storage in oxd

## Overview

The idea of using using `ldap` (or `Couchbase`) storage in oxd came so that oxd and Gluu Server can have common persistance. This is particularly useful when configuring high availability (HA) across multiple instances of the Gluu Server. And oxd can also be configured to use any remote or local `ldap` (which is not used by Gluu server) as storage.

## Using Gluu server's `ldap` in oxd

To use Gluu server's `ldap` as storage in oxd-server we need to follow below step:

1. [Login](https://www.gluu.org/docs/gluu-server/installation-guide/install-ubuntu/#start-the-server-and-log-in) to Gluu CE Server.

1. In /opt/oxd-server/conf/oxd-server.yml file of installed oxd-server set following parameters. Here, `gluu_server_configuration` storage tells oxd to use Gluu server persistance. 

  ```
  storage: gluu_server_configuration
  storage_configuration:
    baseDn: o=gluu
    type: /etc/gluu/conf/gluu.properties
    connection: /etc/gluu/conf/gluu-ldap.properties
    salt: /etc/gluu/conf/salt
  ```
  
In Gluu CE server the [persistance](https://www.gluu.org/docs/gluu-server/reference/persistence) configuration files are stored at location `/etc/gluu/conf`. Following oxd following 3 configuration files are used for ldap connection: `/etc/gluu/confgluu.properties`, `/etc/gluu/conf/gluu-ldap.properties` and `/etc/gluu/conf/salt`.
  
1. Restart oxd server. Once oxd is successfully restarted then

  ```
  systemctl restart oxd-server
  ```

### Fields details of 

**gluu.properties fields**
oxd read `persistence.type` field from this file to find whether the storage is `ldap` or `couchbase`.

```
persistence.type=<ldap or couchbase>
```

**gluu-ldap.properties fields**

```
bindDN: <ldap_binddn>
bindPassword: <encoded_ ldap_pw>
servers: <ldap_hostname>:<ldaps_port>

useSSL: true
ssl.trustStoreFile: <ldapTrustStoreFn>
ssl.trustStorePin: <encoded_ldapTrustStorePass>
ssl.trustStoreFormat: pkcs12

maxconnections: 10

# Max wait 20 seconds
connection.max-wait-time-millis=20000

# Force to recreate polled connections after 30 minutes
connection.max-age-time-millis=1800000

# Invoke connection health check after checkout it from pool
connection-pool.health-check.on-checkout.enabled=false

# Interval to check connections in pool. Value is 3 minutes. Not used when onnection-pool.health-check.on-checkout.enabled=true
connection-pool.health-check.interval-millis=180000

# How long to wait during connection health check. Max wait 20 seconds
connection-pool.health-check.max-response-time-millis=20000

binaryAttributes=objectGUID
certificateAttributes=userCertificate
```

**salt fields**

This file contain salt string to decode the encoded values from `gluu-ldap.properties` file.

```
encodeSalt=<salt-string>
```

