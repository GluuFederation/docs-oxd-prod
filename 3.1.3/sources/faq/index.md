# Frequently Asked Questions (FAQ)

## General FAQs

### What is oxd?  
oxd is a mediator: it provides APIs that can be called by a web application more easily than directly calling the APIs of an OpenID Connect Provider (OP) or an UMA Authorization Server (AS).

### How is oxd licensed, and how much does it cost?       
oxd is commercially licensed software. To start the oxd server, a valid license is needed, which can be [obtained by registering on the oxd website](https://oxd.gluu.org). Each application (OAuth 2.0 client) that leverages the oxd service is billed USD $10 per month. The first 10 oxd clients are always free, and there is a five (5) day grace period for each new client–meaning: only clients active for 5 or more days are recorded for billing purposes.  Usage fees are accumulated daily and charged at the end of each month. If your organization needs annual billing, [schedule a call with us](https://gluu.org/booking).
 
### What types of applications can use oxd?       
Server-side web applications. 

### Where should oxd be deployed?      
By default, oxd-server must be deployed on the same host as the target web application(s). With the `oxd-https-extension` enabled, applications can call oxd over the web, enabling a central oxd service for many applications. 

### Can I use oxd for two-factor authentication (2FA)?**    
No. 2FA is implemented at the OP, not the client.  

### What are the support options?    
Gluu offers community support and VIP support. Anyone can register and enlist community support on the [Gluu support portal](https://support.gluu.org). For guaranteed responses and priority support, learn more about [VIP support](https://gluu.org/pricing). 

## Technical FAQs
### The `get_tokens_by_code` command fails with a `No response from operation` error

It can happen if the `code` lifetime in the `oxauth` server is very short and the `code` expires before the token can be obtained. So in logs, you can see this:

```
2018-04-05 14:30:32,530 ERROR [org.xdi.oxd.server.op.GetTokensByCodeOperation] Failed to get tokens because response code is: null
2018-04-05 14:30:32,530 ERROR [org.xdi.oxd.server.Processor] No response from operation. Command: Command{command=GET_TOKENS_BY_CODE, params={"code":"cc36672e-f8b9-4958-9a7c-3d83c99c4289","state":"us7d1v37cn1fcsd1c0156adr16","oxd_id":"055cec18-bd2e-4b29-ae38-7428d1d7c7fb","protection_access_token":"51ebfa51-d290-410e-bd37-abb3a0d8ab0c"}}
```

To fix it, increase the `authorizationCodeLifetime` oxauth configuration value as explained [here](https://gluu.org/docs/ce/3.1.3/admin-guide/oxtrust-ui/#oxauth-configuration).

### `oxd-https-extension` does not work because of a PROTECTION error.

If you see an output in the logs similar to what is shown below, it means that the `uma_protection` scope is disabled for dynamic registration on the `oxauth` side.
Find the `uma_protection` Connect scope property `Allow for dynamic registration`, and make sure it is checked (set to true). Find more information about scopes [here](https://gluu.org/docs/ce/3.1.3/admin-guide/openid-connect/#scopes)
 
```
2018-04-04 20:03:24,855 ERROR [org.xdi.oxd.server.service.UmaTokenService] oxd requested scope PROTECTION but AS returned access_token without that scope, token scopes :openid
2018-04-04 20:03:24,855 ERROR [org.xdi.oxd.server.service.UmaTokenService] Please check AS(oxauth) configuration and make sure UMA scope (uma_protection) is enabled.
2018-04-04 20:03:24,855 TRACE [org.xdi.oxd.server.service.IntrospectionService] Exception during access token introspection.
java.lang.RuntimeException: oxd requested scope PROTECTION but AS returned access_token without that scope, token scopes :openid
	at org.xdi.oxd.server.service.UmaTokenService.obtainTokenWithClientCredentials(UmaTokenService.java:196)
	at org.xdi.oxd.server.service.UmaTokenService.obtainToken(UmaTokenService.java:169)
```

### How can I view data inside an oxd database manually without an oxd-server? 

By default, an oxd-server persists data inside a H2 embedded database. On a disk it should be visible as a `oxd_db.mv.db` file.
You can use any convenient database viewer to view/edit data inside the database. We recommend using a browser-based viewer, H2:

 - Download http://www.h2database.com/html/download.html
 - Run it (in the "Platform-Independent zip" case it is as simple as hitting `h2.sh` or `h2.bat`)
 
 You will see connection details in the browser. Please specify the details as in `oxd-conf.json` file. 
 If everything is filled in correctly, in "Test Connection" you should see a "Test successful" message like in the screenshot below:
 
 ![H2](../img/faq_h2_connection_details.png)
 
 After hitting the "Connect" button, you will be able to view/modify the data manually. Please be careful not to corrupt the data inside; otherwise the oxd-server will not be able to operate in its stable mode. 
 
### How can I avoid client expiration?

The `register_site` and `setup_client` commands generate clients dynamically, thus setting the lifetimes of these clients. The lifetime of the client is set on the OP side.

It is possible to extend a client's lifetime by calling the `update_site` command and set `client_secret_expires_at` to a chosen date. This field accepts the number of milliseconds since 1970. You can use [https://currentmillis.com/](https://currentmillis.com/) to convert this time to milliseconds. For example, `Fri Jun 15 2018 12:28:28` is `1529065708906`.

Note that `setup_client` creates 2 clients in oxd-server versions up to 3.2.0, so if you need to extend the lifetimes of both clients, call `update_site` with `oxd_id` and `setup_client_oxd_id` which are returned as the response from the `setup_client` command. 

### How can I use oxd with AS that does not support UMA ?
Please set `uma2_auto_register_claims_gathering_endpoint_as_redirect_uri_of_client` in `oxd-config.json` to fails otherwise you may get `no_uma_discovery_response` if UMA is not supported on AS side.  
