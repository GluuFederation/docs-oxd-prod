# Frequently Asked Questions (FAQ)

## General FAQs

### What is oxd?  
oxd is a mediator: it provides APIs that can be called by a web application more easily than directly calling the APIs of an OpenID Connect Provider (OP) or an UMA Authorization Server (AS).

### How is oxd licensed, and how much does it cost?       
oxd is commercially licensed software. To start the oxd server, a valid license is needed, which can be [obtained by registering on the oxd website](https://oxd.gluu.org). Each application (OAuth 2.0 client) that leverages the oxd service is billed USD $0.33 per day. Usage fees are accumulated daily and charged at the end of each month. If your organization needs annual billing, [schedule a call with us](https://gluu.org/booking).
 
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

It can happen if `code` lifetime in `oxauth` server is very small and `code` expires before token can be obtained. So in logs, you can see this:

```
2018-04-05 14:30:32,530 ERROR [org.xdi.oxd.server.op.GetTokensByCodeOperation] Failed to get tokens because response code is: null
2018-04-05 14:30:32,530 ERROR [org.xdi.oxd.server.Processor] No response from operation. Command: Command{command=GET_TOKENS_BY_CODE, params={"code":"cc36672e-f8b9-4958-9a7c-3d83c99c4289","state":"us7d1v37cn1fcsd1c0156adr16","oxd_id":"055cec18-bd2e-4b29-ae38-7428d1d7c7fb","protection_access_token":"51ebfa51-d290-410e-bd37-abb3a0d8ab0c"}}
```

To fix it, increase the `authorizationCodeLifetime` oxauth configuration value as explained [here](https://gluu.org/docs/ce/3.1.3/admin-guide/oxtrust-ui/#oxauth-configuration).

### `oxd-https-extension` does not work because of a PROTECTION error.

If you see output in the logs similar to what is shown below, it means that the `uma_protection` scope is disabled for dynamic registration on the `oxauth` side.
Find the `uma_protection` Connect scope property `Allow for dynamic registration`, and make sure it is checked (set to true). Find more information about scopes [here](https://gluu.org/docs/ce/3.1.3/admin-guide/openid-connect/#scopes)
 
```
2018-04-04 20:03:24,855 ERROR [org.xdi.oxd.server.service.UmaTokenService] oxd requested scope PROTECTION but AS returned access_token without that scope, token scopes :openid
2018-04-04 20:03:24,855 ERROR [org.xdi.oxd.server.service.UmaTokenService] Please check AS(oxauth) configuration and make sure UMA scope (uma_protection) is enabled.
2018-04-04 20:03:24,855 TRACE [org.xdi.oxd.server.service.IntrospectionService] Exception during access token introspection.
java.lang.RuntimeException: oxd requested scope PROTECTION but AS returned access_token without that scope, token scopes :openid
	at org.xdi.oxd.server.service.UmaTokenService.obtainTokenWithClientCredentials(UmaTokenService.java:196)
	at org.xdi.oxd.server.service.UmaTokenService.obtainToken(UmaTokenService.java:169)
```

### I've registered site via `register_site` (or `setup_client`) command and have `oxd_id`. How can I veiw data associated with this `oxd_id`?

There is script `/opt/oxd-server/bin/oxd-show.sh` which you can use to view the data. If thit without parameters it show hint
```
yuriy@yuriyz:~/oxd-server-3.1.4-SNAPSHOT-distribution/bin$ sh oxd-show.sh
BASEDIR=.
CONF=./../conf/oxd-conf.json
Missing required option: oxd_id
usage: utility-name
 -oxd_id,--oxd_id <arg>   oxd_id is unique identifier within oxd database
                          (returned by register_site and setup_client
                          commands)

```

Typical call looks as
```
yuriy@yuriyz:~/oxd-server-3.1.4-SNAPSHOT-distribution/bin$ sh oxd-show.sh -oxd_id d8cc6dea-4d66-4995-b6e1-da3a33722f2e
BASEDIR=.
CONF=./../conf/oxd-conf.json

yuriy@yuriyz:~/oxd-server-3.1.4-SNAPSHOT-distribution/bin$JSON for oxd_id d8cc6dea-4d66-4995-b6e1-da3a33722f2e
{"scope":["openid","uma_protection","profile"],"contacts":[],"pat":null,"rpt":null,"oxd_id":"d8cc6dea-4d66-4995-b6e1-da3a33722f2e","op_host":"https://ce-dev4.gluu.org","op_discovery_path":null,"id_token":null,"access_token":null,"authorization_redirect_uri":"https://client.example.com/cb","logout_redirect_uri":"https://client.example.com/cb","application_type":"web","redirect_uris":["https://client.example.com/cb"],"claims_redirect_uri":[],"response_types":["code"],"front_channel_logout_uri":[""],"client_id":"@!38D4.410C.1D43.8932!0001!37F2.B744!0008!B390.5F6D.2051.A8C0","client_secret":"4a72e386-97ed-49a0-a338-cd448e5020b3","client_registration_access_token":"920fdb64-9bd7-4b5f-8a8c-8689e29860b8","client_registration_client_uri":"https://ce-dev4.gluu.org/oxauth/restv1/register?client_id=@!38D4.410C.1D43.8932!0001!37F2.B744!0008!B390.5F6D.2051.A8C0","client_id_issued_at":1528879584000,"client_secret_expires_at":1528965984000,"client_name":null,"sector_identifier_uri":null,"client_jwks_uri":null,"token_endpoint_auth_signing_alg":null,"token_endpoint_auth_method":null,"is_setup_client":null,"setup_oxd_id":null,"setup_client_id":null,"ui_locales":["en"],"claims_locales":["en"],"acr_values":[""],"grant_types":["authorization_code","urn:ietf:params:oauth:grant-type:uma-ticket","client_credentials"],"user_id":null,"user_secret":null,"pat_expires_in":0,"pat_created_at":null,"pat_refresh_token":null,"uma_protected_resources":[],"rpt_token_type":null,"rpt_pct":null,"rpt_upgraded":null,"rpt_expires_at":null,"rpt_created_at":null,"oxd_rp_programming_language":"java"}
```