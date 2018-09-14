# Security Considerations

The `oxd-https-extension` is a RESTful server that accepts `HTTPS` calls based on `dropwizard` framework. Communication between `oxd-https-extension` and `oxd-server` is protected by `protection_access_token`.

## Limit access

`oxd-https-extension` is a web server which handles all requests. An attacker can use such an open server for his or her own needs or attack it (e.g. DDoS). Therefore it is recommended to protect it by putting `oxd-https-extension` in a private network. As an alternative it is possible to proxy requests via a web server (e.g. Apache HTTP Server or nginx) and limit access via it. 
