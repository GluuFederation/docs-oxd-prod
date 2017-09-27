# oxd-python

## Overview
The following documentation demonstrates 
how to use the [oxd client software](http://oxd.gluu.org) Python library to 
send users from a Python application to an OpenID Connect Provider (OP), 
like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 


## Sample Project

[Download a sample project](https://github.com/GluuFederation/oxd-python/archive/3.1.1.zip) specific to this oxd python library.

### System Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows 7 or higher / Windows Server 2008 or higher
- Python 2.7
- Flask
- oxdpython 3.1.1
- Flask-SSLify


## Prerequisites

### Required Software
To use the oxd Python library, you will need:

- A valid OpenID Connect Provider (OP), like Google or the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/).    
- An active installation of the [oxd server](../../install/index.md) running in the same server as the client application.
- An active installation of the [oxd-https-extension](../../install/index.md) if oxd-https-extension connection is used.
- A Windows server or Windows installed machine / Linux server or Linux installed machine.


### Install oxdpython via pip

To install oxdpython via pip, run following commands in Linux terminal or 
Windows command window

``` {.code }
pip install oxdpython==3.1.1
```

To install Flask-SSLify via pip, run following commands in Linux terminal or 
Windows command window 

``` {.code }
pip install Flask-SSLify
```


### Configure the Client Application

- Client application must have a valid ssl cert, so the url includes: `https://`    

    
- The client host name should be a valid `hostname`(FQDN), not localhost or an IP Address. 
You can configure the host name by adding the following entry in the host file.

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`

- Open the downloaded [sample project](https://github.com/GluuFederation/oxd-python/archive/3.1.1.zip) and navigate to `demosite` directory inside the project

- Enable SSL by	setting the valid certificate and key in your application startup file (demosite.py):

```{.code}
from flask_sslify import SSLify

app = Flask(__name__)
sslify = SSLify(app)

app.run('127.0.0.1', debug=True, port=8080, ssl_context=('<path>/demosite.crt', '<path>/demosite.key'))
```
- Run the following command to install oxdpython library

``` {.code }
pip install oxdpython==3.1.1
```
- Run the following command to run the sample client application
   
``` {.code }
python demosite.py
```

- That's it!. Now navigate to following url to run Sample client application. Make sure oxd server is running. Setup client url can be used for registering Client in oxd server. Upon successful registration of the client application, oxd Id will be displayed in the UI. Then navigate to Login URL for authentication.
    - Setup client url: https://client.example.com:8080/setupClient
    - Login URL: https://client.example.com:8080
    - UMA URL: https://client.example.com:8080/uma

- The input values used during Setup Client are stored in a configuration file (demosite.cfg). So the config file needs to be writable by the Client application.


!!!Note: 
    The [sample.cfg](https://github.com/GluuFederation/oxd-python/blob/3.1.1/sample.cfg)
    file contains detailed documentation about the configuration values.



## oxd Server Endpoints

The oxd server provides the following methods for authenticating users with 
an OpenID Connect Provider (OP):

- Available OpenId Connect Endpoints
    - [Setup Client](../../protocol/#setup-client)  
    - [Get Client Token](../../protocol/#get-client-token)
    - [Register Site](../../protocol/#register-site) 
    - [Update Site Registration](../../protocol/#update-site-registration)    
    - [Get Authorization URL](../../protocol/#get-authorization-url)   
    - [Get Tokens by Code](../../protocol/#get-tokens-id-access-by-code)
    - [Get Access Token by Refresh Token](../../protocol/#get-access-token-by-refresh-token)    
    - [Get User Info](../../protocol/#get-user-info)   
    - [Get Logout URI](../../protocol/#log-out-uri) 
- Available UMA (User Managed Access) Endpoints  
    - [UMA RS Protect](../../protocol/#uma-rs-protect) 
    - [UMA RS Check Access](../../protocol/#uma-rs-check-access) 
    - [UMA RP Get RPT](../../protocol/#uma-rp-get-rpt) 
    - [UMA RP Get Claims Gathering URL](../../protocol/#uma-rp-get-claims-gathering-url) 


## oxd https extension Endpoints

The oxd https extension provides the following methods for authenticating users with an OpenID Connect Provider (OP):
- Available OpenId Connect Endpoints
    - [Setup Client](../../oxd-https/api/#setup-client)  
    - [Get Client Token](../../oxd-https/api/#get-client-token)
    - [Register Site](../../oxd-https/api/#register-site) 
    - [Update Site Registration](../../oxd-https/api/#update-site-registration)    
    - [Get Authorization URL](../../oxd-https/api/#get-authorization-url)   
    - [Get Tokens by Code](../../oxd-https/api/#get-tokens-id-access-by-code)
    - [Get Access Token by Refresh Token](../../oxd-https/api/#get-access-token-by-refresh-token)    
    - [Get User Info](../../oxd-https/api/#get-user-info)   
    - [Get Logout URI](../../oxd-https/api/#log-out-uri) 
- Available UMA (User Managed Access) Endpoints 
    - [UMA RS Protect](../../oxd-https/api/#uma-rs-protect) 
    - [UMA RS Check Access](../../oxd-https/api/#uma-rs-check-access) 
    - [UMA RP Get RPT](../../oxd-https/api/#uma-rp-get-rpt) 
    - [UMA RP Get Claims Gathering URL](../../oxd-https/api/#uma-rp-get-claims-gathering-url) 


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

- op_host: OpenId Connect Provider url
- authorization_redirect_uri: URL which the OP is authorized to redirect the user after authorization
- connection_type: 'local' for oxd-server and 'web' for oxd-https-extension
- connection_type_value: 'oxd port number' for oxd-server type and ' oxd-https-extension url' for  oxd-https-extension type
- client_name: Client application name
- post_logout_uri: URL to which user is redirected after successful logout
- clientId: Client Id from OpenID Connect Provider (Should be passed with Client Secret)
- clientSecret: Client Secret from OpenID Connect Provider (Should be passed with Client Id)
- claims_redirect_uri: 

```python
from oxdpython import Client

config = "/var/www/demosite/demosite.cfg"  # This should be writable by the server
oxc = Client(config)
```

**Request:**
```python
client_data = oxc.setup_client(op_host=str(op_host), authorization_redirect_uri=str(authorization_redirect_uri), conn_type=str(conn_type), conn_type_value=str(conn_type_value), client_name=str(client_name), post_logout_uri=str(post_logout_uri), client_id=str(client_id), client_secret=str(client_secret))
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

!!!Note: 
`register_site` endpoint is not required if client is registered using `setup_client`

**Required parameters:**

- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
oxd_id = client.register_site(protection_access_token=str(protection_access_token))
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

- client_name: (Optional) Client application name
- contacts: (Optional) User's email Id
- authorization_redirect_uri:  (Optional) URL which the OP is authorized to redirect the user after authorization.
- post_logout_uri: (Optional) URL to which the RP is requesting that the 
End-User's User Agent be redirected after a logout has been performed.
- connection_type_value: (Optional) 'oxd port number' for oxd-server type and 'oxd-https-extension url' for oxd-https-extension  type
- connection_type: (Optional) 'local' for oxd-server and 'web' for oxd-https-extension
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
authorization_redirect_uri = request.form['redirect_uri']
client_name = request.form['client_name']
post_logout_uri = request.form['post_logout_uri']
conn_type_value = request.form['conn_type_name']
conn_type = request.form['conn_type_radio']

status = oxc.update_site_registration(client_name=str(client_name), authorization_redirect_uri=str(redirect_uri), post_logout_uri=str(post_logout_uri), connection_type_value=str(conn_type_value), connection_type=str(conn_type), protection_access_token=str(protection_access_token))
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

- scope: (Optional) A scope is an indication by the client that it wants to access some resource.
- acr_values: (Optional) Required for extened authenication. Custom Authentication script from Gluu server. 
- prompt: (Optional)
- custom_params: (Optional) custom parameters
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
auth_url = oxc.get_authorization_url(custom_params=custom_params, protection_access_token=str(protection_access_token))
```

**Response:**

```python
auth_url = https://client.example.com/authorize?response_type=code&client_id=s6BhdRkqt3&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb&scope=openid%20profile&acr_values=duo&state=af0ifjsldkj&nonce=n-0S6_WzA2Mj&param2=value2&param1=value1
```

### Get Tokens by Code

Upon successful login, the login result will return code and state. `get_tokens_by_code` 
uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**

- code: The Code from OP authorization redirect url
- state: The State from OP authorization redirect url
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
tokens = oxc.get_tokens_by_code(code=code, state=state, protection_access_token=str(protection_access_token))

accessToken = tokens.access_token
refreshToken = tokens.refresh_token
```

**Response:**

```python
accessToken = SlAV32hkKG
refreshToken = aaAV32hkKG1
```
### Get Access Token by Refresh Token

The `get_access_token_by_refresh_token` method is used to get a new access token and a new refresh token by using the refresh token which is obtained from `get_tokens_by_code` method.

**Required parameters:**

- refreshToken: Obtained from the get_tokens_by_code method
- scope: (Optional) A scope is an indication by the client that it wants to access some resource.
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
newtokens = oxc.get_access_token_by_refresh_token(refresh_token=tokens.refresh_token, protection_access_token=str(protection_access_token))
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

- access_token: access_token from GetTokenByCode or GetAccessTokenbyRefreshToken
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
user = oxc.get_user_info(access_token=access_token, protection_access_token=str(protection_access_token))

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

- id_token_hint: (Optional)
- post_logout_redirect_uri: (Optional) URL to which user is redirected after successful logout
- state: (Optional)
- session_state: (Optional)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
logout_url = oxc.get_logout_uri(protection_access_token=str(protection_access_token))
session.clear()
return redirect(logout_url)
```

**Response:**

```python
logout_url = https://<server>/end_session?id_token_hint=<id token>&state=<state>&post_logout_redirect_uri=<...>
```
### UMA RS Protect

`uma_rs_protect` method is used for protecting resource by the Resource Server. Resource server need to construct the command which will protect the resource.
The command will contain api path, http methods (POST,GET, PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy mapped, uma_rs_check_access method will always return access as granted. To know more about uma_rpt_policies you can check this [document]().

**Required parameters:**

- resources: One or more protected resources that a resource server manages as a set, abstractly. In authorization policy terminology, a resource set is the "object" being protected. 
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

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

result = oxc.uma_rs_protect(resources=resources, protection_access_token=str(protection_access_token))
```

**Response:**

```python
result = true
```

### UMA RS Check Access 

`uma_rs_check_access` method used in a UMA Resource Server to check the access to the resource.

**Required parameters:**

- rpt: Requesting Party Token
- path: Path of the resource to be checked 
- http_method: http metho like POST, GET, PUT
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
result = oxc.uma_rs_check_access(rpt=str(rpt), path=path, http_method=http_method, protection_access_token=str(protection_access_token))
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

The method uma_rp_get_rpt is called in order to obtain the rpt (Requesting Party Token). 

**Required parameters:**

- ticket: Client Access Ticket generated by uma_rs_check_access method
- claim_token: (Optional)
- claim_token_format: (Optional)
- pct: (Optional) Persisted Claims Token
- rpt: (Optional) Requesting Party Token. 
- scope: (Optional)
- state: (Optional) state that is returned from uma_rp_get_claims_gathering_url method
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
result = oxc.uma_rp_get_rpt(ticket=str(ticket), scope='Test_scope', protection_access_token=str(protection_access_token))
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

- ticket: Client Access Ticket generated by uma_rs_check_access method
- claims_redirect_uri: 
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
result = oxc.uma_rp_get_claims_gathering_url(ticket=str(ticket), claims_redirect_uri=str(claims_redirect_uri), protection_access_token=str(protection_access_token))
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
Please report technical issues and suspected bugs on our [support page](https://support.gluu.org/). You can use the same credentials you created to register for your oxd license to sign in on Gluu support.