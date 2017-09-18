# oxd-python

## Overview
The following documentation demonstrates 
how to use the [oxd client software](http://oxd.gluu.org) Python library to 
send users from a Python application to an OpenID Connect Provider (OP), 
like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 

## Sample Project

[Download a sample project](https://github.com/GluuFederation/oxd-python/archive/master.zip) specific to this oxd library.

### System Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows 7 or higher / Windows Server 2008 or higher
- Python 2.7 or higher
- Flask
- oxdpython 3.1.0
- Flask-SSLify


## Prerequisites

### Required Software
To use the oxd Python library, you will need:

- A valid OpenID Connect Provider (OP), like Google or the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/).    
- An active installation of the [oxd server](../../install/index.md) running in the same server as the client application.
- An active installation of the [oxd web](../../install/index.md) if oxd-web connection is used.
- A Windows server or Windows installed machine / Linux server or Linux installed machine.

### Install oxdpython via pip

To install oxdpython via pip, run following commands in Linux terminal or 
Windows command window

``` {.code }
pip install oxdpython
```

To install Flask-SSLify via pip, run following commands in Linux terminal or 
Windows command window 

``` {.code }
pip install Flask-SSLify
```

### Configure the Client Application

- This oxd python library uses a configuration file (demosite.cfg) to specify 
information needed by OpenID Connect dynamic client registration, and to save 
information that is returned, like the oxd_id. So the config file needs to be 
writable by the Client application.


The minimal configuration required to get oxd-python working:

```
    [oxd]
    host = localhost
    port = 8099
    id = <oxd_id>
    
    [client]
    op_host = <https://ophost_url>
    client_name = 
    authorization_redirect_uri = https://client.example.com/CodeState
    post_logout_redirect_uri = https://client.example.com
    scope = openid,profile,email,uma_authorization,uma_protection
    client_id = <client_id>
    client_secret = <client_secret>
    grant_types = authorization_code,client_credentials,uma_ticket
    dynamic_registration = 'true' if the op_host supports dynamic registration otherwise 'false'
```
  
Upon successful registration of the client application oxd_id is set to the 
field 'id' in demosite.cfg.

!!!Note: 
    The [sample.cfg](https://github.com/GluuFederation/oxd-python/blob/3.0.1/sample.cfg)
    file contains detailed documentation about the configuration values.

- Your client application must have a valid ssl cert, so the url includes: `https://`    

- Enable SSL by	setting the valid certificate and key in your application startup file:

```{.code}
from flask_sslify import SSLify

app = Flask(__name__)
sslify = SSLify(app)

app.run('127.0.0.1', debug=True, port=8080, ssl_context=('<path>/demosite.crt', '<path>/demosite.key'))
```
    
- The client host name should be a valid `hostname`(FQDN), not localhost or an IP Address. 
You can configure the host name by adding the following entry in your host file.

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`

- setup_client url: https://client.example.com:8080/setupClient
- Login URL: https://client.example.com:8080


## oxd Server Methods

The oxd server provides the following six methods for authenticating users with 
an OpenID Connect Provider (OP):

- [Setup Client](../../protocol/#setup-client)  
- [Get Client Token](../../protocol/#get-client-token)
- [Register Site](../../protocol/#register-site) 
- [Update Site Registration](../../protocol/#update-site-registration)    
- [Get Authorization URL](../../protocol/#get-authorization-url)   
- [Get Tokens by Code](../../protocol/#get-tokens-id-access-by-code)
- [Get Access Token by Refresh Token](../../protocol/#get-access-token-by-refresh-token)    
- [Get User Info](../../protocol/#get-user-info)   
- [Get Logout URI](../../protocol/#log-out-uri) 
- [UMA RS Protect](../../protocol/#uma-rs-protect) 
- [UMA RS Check Access](../../protocol/#uma-rs-check-access) 
- [UMA RP Get RPT](../../protocol/#uma-rp-get-rpt) 
- [UMA RP Get Claims Gathering URL](../../protocol/#uma-rp-get-claims-gathering-url) 


## Sample Code

### Setup Client

In order to use an OpenID Connect Provider (OP) for login, 
you need to setup your client application at the OP. 
During setup oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful setup a unique 
identifier will be issued by the oxd server by assigning a specific oxd id. Along with oxd Id oxd server will also return client Id and client secret. This client Id and client secret can be used for `get_client_token` method. If your OpenID Connect Provider 
does not support dynamic registration (like Google), you will need to obtain a 
ClientID and Client Secret which can be passed to the `setup_client` method as a 
parameter. The Setup Client method is a one time task to configure a client in the 
oxd server and OP.

**Required parameters:**

- op_host: OpenId Connect Provider
- client_name: Client application name
- authorization_redirect_uri: URL which the OP is authorized to redirect the user after authorization
- post_logout_uri: URL to which user is redirected after successful logout
- clientId: Client Id from OpenID Connect Provider (Should be passed with Client Secret)
- clientSecret: Client Secret from OpenID Connect Provider (Should be passed with Client Id)
- connection_type: 'local' for oxd-local and 'web' for oxd-web
- connection_type_value: 'oxd port number' for oxd-local type and 'oxd web url' for oxd-web type
- claims_redirect_uri: 

```python
from oxdpython import Client

config = "/var/www/demosite/demosite.cfg"  # This should be writable by the server
client = Client(config)
```

**Request:**
```python
client_data = oxc.setup_client(op_host, client_name, authorization_redirect_uri, post_logout_uri, clientId, clientSecret, conn_type, conn_type_value,None)
oxd_id = client_data.oxd_id
client_id = client_data.client_id
client_secret = client_data.client_secret
```

**Response:**

```python
oxd_id = 6F9619FF-8B86-D011-B42D-00CF4FC964FF
client_id = @!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!A125.EE21.F0E2.8C08
client_secret = f8eb25a0-f69b-4e4f-8058-ba9cbe6bf7eb
```

### Get Client Token
The `get_client_token` method is used to get a token which is sent as input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Request:**

```python
response = oxc.get_client_token()
```

**Response:**

```python
response = {
        "access_token"=u'd9e64cbe-e096-4406-a030-8dd7542871d0', 
        "scope"=u'openid', 
        "expires_in"=299, 
        "refresh_token"=None
    }
```

### Register Site

In order to use an OpenID Connect Provider (OP) for login, 
you need to register your client application at the OP. 
During registration oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful registration a unique 
identifier will be issued by the oxd server. If your OpenID Connect Provider 
does not support dynamic registration (like Google), you will need to obtain a 
ClientID and Client Secret which can be passed to the `register_site` method as a 
parameter. The Register Site method is a one time task to configure a client in the 
oxd server and OP.

**Required parameters:**

- protection_access_token: Generated from get_client_token method.

**Request:**

```python
oxd_id = client.register_site(protection_access_token)
```

**Response:**

```python
oxd_id = 6F9619FF-8B86-D011-B42D-00CF4FC964FF
```

### Update Site Registration

The `update_site_registration` method can be used to update an existing client in the OP. 
Fields like Authorization redirect url, post logout url, scope, client secret and other fields, 
can be updated using this method.

**Required parameters:**

- protection_access_token: Generated from get_client_token method.
- client_name: Client application name
- authorization_redirect_uri:  URL which the OP is authorized to redirect the user after authorization.
- post_logout_uri: URL to which the RP is requesting that the 
End-User's User Agent be redirected after a logout has been performed.
- connection_type_value: 'oxd port number' for oxd-local type and 'oxd web url' for oxd-web type
- connection_type: 'local' for oxd-local and 'web' for oxd-web


**Request:**

```python
authorization_redirect_uri = request.form['redirect_uri']
client_name = request.form['client_name']
post_logout_uri = request.form['post_logout_uri']

status = oxc.update_site_registration(protection_access_token, client_name, redirect_uri, post_logout_uri, conn_type_value, conn_type)
```
**Response:**

```python
status = ok
```

### Get Authorization URL

The `get_authorization_url` method returns the OpenID Connect Provider 
authentication URL to which the client application must redirect the user to 
authorize the release of personal data. The response URL includes state value, 
which can be used to obtain tokens required for authentication. This state value used 
to maintain state between the request and the callback.

**Required parameters:**

- protection_access_token: Generated from get_client_token method.
- acr_values: (Optional)
- prompt: (Optional)
- scope: (Optional)

**Request:**

```python
auth_url = client.get_authorization_url(protection_access_token)
```

**Response:**

```python
auth_url = https://client.example.com/authorize?response_type=code&client_id=s6BhdRkqt3&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb&scope=openid%20profile&acr_values=duo&state=af0ifjsldkj&nonce=n-0S6_WzA2Mj
```

### Get Tokens by Code

Upon successful login, the login result will return code and state. `get_tokens_by_code` 
uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**

- protection_access_token: Generated from get_client_token method.
- code: The Code from OP authorization redirect url
- state: The State from OP authorization redirect url

**Request:**

```python
tokens = client.get_tokens_by_code(protection_access_token, code, state)

accessToken = tokens.access_token
refreshToken = tokens.refresh_token
```

**Response:**

```python
accessToken = SlAV32hkKG
refreshToken = aaAV32hkKG1
```
### Get Access Token by Refresh Token

The `get_access_token_by_refresh_token` method is used to get a fresh access token and refresh 
token by using the refresh token which is obtained from `get_tokens_by_code` method.

**Required parameters:**

- protection_access_token: Generated from get_client_token method.
- refreshToken: Obtained from the get_tokens_by_code method
- scope: (Optional)

**Request:**

```python
newtokens = oxc.get_access_token_by_refresh_token(protection_access_token, refresh_token)
accessToken = newtokens.access_token
refreshToken = newtokens.refresh_token
```

**Response:**

```python
accessToken = ALSAKLDDKN9787DYAH
refreshToken = gh75UHGHHS88sd
```

### Get User Info

Once the user has been authenticated by the OpenID Connect Provider, 
the `get_user_info` method returns Claims (Like First Name, Last Name, emailId, etc.) 
about the authenticated end user.

**Required parameters:**

- protection_access_token: Generated from get_client_token method.
- access_token: access_token from GetTokenByCode or GetAccessTokenbyRefreshToken

**Request:**

```python
user = client.get_user_info(protection_access_token, access_token)

userName = user.name[0]
userEmail = user.email[0]
```

**Response:**

```python
user = {given_name=["Jane"], family_name=["Doe"], email=["janedoe@example.com"], sub=["248289761001"], name=["Jane Doe"] }
```

### Logout

`get_logout_uri` method returns the OpenID Connect Provider logout url. 
Client application  uses this logout url to end the user session.

**Required parameters:**

- protection_access_token: Generated from get_client_token method.
- id_token_hint: (Optional)
- post_logout_redirect_uri: (Optional) URL to which user is redirected after successful logout
- state: (Optional)
- session_state: (Optional)

**Request:**

```python
logout_url = client.get_logout_uri(protection_access_token)
session.clear()
return redirect(logout_url)
```

**Response:**

```python
logout_url = https://<server>/end_session?id_token_hint=<id token>&state=<state>&post_logout_redirect_uri=<...>
```
### UMA RS Protect

`uma_rs_protect` method is used for protecting resource by the Resource Server. 
Resource server need to construct the command which will protect the resource.
The command will contain api path, http methods (POST,GET, PUT) and scopes. Scopes can be mapped 
with authorization policy (uma_rpt_policies). If no authorization policy mapped, 
`uma_rs_check_access` method will always return access as granted. To know more about uma_rpt_policies 
you can check this [document](https://github.com/GluuFederation/docs-ce-prod/blob/3.1.0/3.1.0/source/admin-guide/uma.md#uma-rpt-authorization-policies).

**Required parameters:**

- protection_access_token: Generated from get_client_token method.
- resources: One or more protected resources that a resource server manages as a set, abstractly. 
In authorization policy terminology, a resource set is the "object" being protected. 

**Request:**

```python
resources = [
        {"path": "/photo",
         "conditions": [
             {
                 "scopes":["https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1"],
                 "ticketScopes":["https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1"],
                 "httpMethods":["GET"]
             }
         ]
         }
    ]

result = oxc.uma_rs_protect(protection_access_token, resources)
```

**Response:**

```python
result = true
```

### UMA RS Check Access 

Function to be used in a UMA Resource Server to check access by the 

**Required parameters:**

- protection_access_token: Generated from get_client_token method.
- rpt: Requesting Party Token
- path: Path of the resource to be checked 
- http_method: http metho like POST, GET, PUT

**Request:**

```python
result = oxc.uma_rs_check_access(protection_access_token, rpt, path, http_method)
```

**Response:**

***Access Granted response:***

```javascript
{
    "status":"ok",
    "data":{
        "access":"granted"
    }
}
```

***Access Denied with ticket response:***

```javascript
{
    "status":"ok",
    "data":{
        "access":"denied"
        "www-authenticate_header":"UMA realm=\"example\",
                                   as_uri=\"https://as.example.com\",
                                   error=\"insufficient_scope\",
                                   ticket=\"016f84e8-f9b9-11e0-bd6f-0021cc6004de\"",
        "ticket":"016f84e8-f9b9-11e0-bd6f-0021cc6004de"
    }
}
```

***Access Denied without ticket response:***

```javascript
{
    "status":"ok",
    "data":{
        "access":"denied"
    }
}
```

***Resource is not protected***

```javascript
{
    "status":"error",
    "data":{
        "error":"invalid_request",
        "error_description":"Resource is not protected. Please protect your resource first with uma_rs_protect command."
    }
}
```

### UMA RP Get RPT 

The method uma_rp_get_rpt is called in order to obtain the rpt 

**Required parameters:**

- protection_access_token: Generated from get_client_token method.
- ticket: Client Access Ticket generated by uma_rs_check_access method
- rpt: (Optional)
- state: (Optional) state that is returned from uma_rp_get_claims_gathering_url method
- claim_token: (Optional)
- claim_token_format: (Optional)
- pct: (Optional)
- scope: (Optional)

**Request:**

```python
result = oxc.uma_rp_get_rpt(protection_access_token=protection_access_token, ticket)
```

**Response:**


***Success Response***

```javascript
 {
     "status":"ok",
     "data":{
         "access_token":"SSJHBSUSSJHVhjsgvhsgvshgsv",
         "token_type":"Bearer",
         "pct":"c2F2ZWRjb25zZW50",
         "upgraded":true
     }
}
```

***Needs Info Error Response***

```javascript
{
     "status":"error",
     "data":{
              "error":"need_info",
              "error_description":"The authorization server needs additional information in order to determine whether the client is authorized to have these permissions.",
              "details": {  
                  "error":"need_info",
                  "ticket":"ZXJyb3JfZGV0YWlscw==",
             "required_claims":[  
                   {  
                     "claim_token_format":[  
                         "http://openid.net/specs/openid-connect-core-1_0.html#IDToken"
                     ],
                     "claim_type":"urn:oid:0.9.2342.19200300.100.1.3",
                     "friendly_name":"email",
                     "issuer":["https://example.com/idp"],
                     "name":"email23423453ou453"
                   }
                 ],
             "redirect_user":"https://as.example.com/rqp_claims?id=2346576421"
         }
     }
}
```

***Invalid ticket error Response***

```javascript
 {
    "status":"error",
    "data":{
            "error":"invalid_ticket",
            "error_description":"Ticket is not valid (outdated or not present on Authorization Server)."
           }
 }
```

### UMA RP Get Claims Gathering URL 

**Required parameters:**

- protection_access_token: Generated from get_client_token method.
- claims_redirect_uri: 
- ticket: Client Access Ticket generated by uma_rs_check_access method

**Request:**

```python
result = oxc.uma_rp_get_claims_gathering_url(protection_access_token, claims_redirect_uri, ticket)
```

**Response:**

```javascript
{
    "status":"ok",
    "data":{
        "url":"https://as.com/restv1/uma/gather_claims
              ?client_id=@!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!AB77!1A2B
              &ticket=4678a107-e124-416c-af79-7807f3c31457
              &claims_redirect_uri=https://client.example.com/cb
              &state=af0ifjsldkj",
        "state":"af0ifjsldkj" 
    }
}
```

## Support
Please report technical issues and suspected bugs on our [support page](https://support.gluu.org/). 
You can use the same credentials you created to register for your oxd license to sign in on Gluu support.