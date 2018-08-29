# Security Considerations

The `oxd-https-extension` is a RESTful server that accepts `HTTPS` calls based on `dropwizard` framework. Communication between `oxd-https-extension` and `oxd-server` is protection by `protection_access_token`.

## Limit access

`oxd-https-extesion` is web server which handles all requests. Attacker can use such open server for own needs. Therefore it is recommended to protect it by putting `oxd-https-extension` in private network. As alternative it is possible to proxy requests via web server (e.g. Apache HTTP Server or nginx) and limit access via it. 