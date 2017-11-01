# oxd-python

## Overview

Use oxd's Python library to send users from a Python application to your Gluu Server OpenID Connect Provider (OP) for dynamic enrollment, single sign-on (SSO), strong authentication, and access management policy enforcement. 


## Sample Project

Download a [Sample Project](https://github.com/GluuFederation/oxd-python/archive/3.1.1.zip) specific to this oxd-python library.

### System Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows Server 2008 or higher
- Python 2.7
- Flask
- oxdpython
- Flask-SSLify


## Prerequisites

### Required Software

To use the oxd-python library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google.    
- An active installation of the [oxd-server](https://gluu.org/docs/oxd/3.1.1/install/
) running on the same server as the client application.
- An active installation of the [oxd-https-extension](https://gluu.org/docs/oxd/3.1.1/install/
) if oxd-https-extension connection is used. In this case, client applications can be on different servers but will be able to access oxd-https-extension.
- A Windows server or Windows installed machine / Linux server or Linux installed machine.


### Install oxdpython via pip

To install oxdpython via pip, run the following commands in the Linux terminal or Windows command window:

``` {.code }
pip install oxdpython
```

To install Flask-SSLify via pip, run the following commands in the Linux terminal or Windows command window:

``` {.code }
pip install Flask-SSLify
```


### Configure the Client Application

- Client application must have a valid SSL certificate, so the URL includes: `https://`    

    
- The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP address. You can configure the hostname by adding the following entry in the host file:

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`

- Open the downloaded [Sample Project](https://github.com/GluuFederation/oxd-python/archive/3.1.1.zip) and navigate to the `demosite` directory inside the project.

- Enable SSL by	setting the valid certificate and key in your application startup file (demosite.py):

```{.code}
from flask_sslify import SSLify

app = Flask(__name__)
sslify = SSLify(app)

app.run('127.0.0.1', debug=True, port=8080, ssl_context=('<path>/demosite.crt', '<path>/demosite.key'))
```
- Run the following command to install oxdpython library:

``` {.code }
pip install oxdpython
```
- Run the following command to run the sample client application:
   
``` {.code }
python demosite.py
```

- With the oxd-server running, navigate to the URL's below to run the sample client application. To register a client in the oxd-server use the Setup Client URL. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, navigate to the Login URL for authentication.

    - Setup Client URL: https://client.example.com:8080/setupClient
    - Login URL: https://client.example.com:8080
    - UMA URL: https://client.example.com:8080/uma

- The input values used during Setup Client are stored in the configuration file (demosite.cfg). Therefore, the configuration file needs to be writable by the client application.


!!! Note: 
    The [sample.cfg](https://github.com/GluuFederation/oxd-python/blob/3.1.1/sample.cfg)
    file contains detailed documentation about the configuration values.



## Endpoints

The oxd-server and oxd-https-extension provide the following methods for authenticating users with an OpenID Connect Provider (OP):

- Available OpenID Connect Endpoints
    - [Setup Client](https://gluu.org/docs/oxd/3.1.1/api/#setup-client)  
    - [Get Client Token](https://gluu.org/docs/oxd/3.1.1/api/#get-client-token)
    - [Register Site](https://gluu.org/docs/oxd/3.1.1/api/#register-site) 
    - [Update Site Registration](https://gluu.org/docs/oxd/3.1.1/api/#update-site-registration)
    - [Get Authorization URL](https://gluu.org/docs/oxd/3.1.1/api/#get-authorization-url)   
    - [Get Tokens by Code](https://gluu.org/docs/oxd/3.1.1/api/#get-tokens-id-access-by-code)
    - [Get Access Token by Refresh Token](https://gluu.org/docs/oxd/3.1.1/api/#get-access-token-by-refresh-token)    
    - [Get User Info](https://gluu.org/docs/oxd/3.1.1/api/#get-user-info)   
    - [Get Logout URI](https://gluu.org/docs/oxd/3.1.1/api/#get-logout-uri) 


The oxd-server provides the following methods for performing access management with a UMA Authorization Server (AS):

- Available UMA (User Managed Access) Endpoints  
    - [RS Protect](https://gluu.org/docs/oxd/3.1.1/api/#uma-rs-protect-resources) 
    - [RS Check Access](https://gluu.org/docs/oxd/3.1.1/api/#uma-rs-check-access) 
    - [RP Get RPT](https://gluu.org/docs/oxd/3.1.1/api/#uma-rp-get-rpt) 
    - [RP Get Claims Gathering URL](https://gluu.org/docs/oxd/3.1.1/api/#uma-rp-get-claims-gathering-url) 



## Sample Code

### OpenID Connect

#### Setup Client

In order to use an OpenID Connect Provider (OP) for login, 
you need to setup your client application at the OpenID Connect Provider (OP). 
During setup, oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful setup, the oxd-server will assign a unique oxd ID, return a Client ID and Client Secret. This Client ID and Client Secret can be used for `get_client_token` method. If your OpenID Connect Provider (OP) does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be passed to the `setup_client` method as a parameter. The Setup Client method is a one time task to configure a client in the oxd-server and OpenID Connect Provider (OP).

**Parameters:**

- authorization_redirect_uri: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- op_host: URL of the OpenID Connect Provider (OP)
- post_logout_uri: (Optional) URL to which the user is redirected to after successful logout
- application_type: (Optional) Application type, the default values are native or web. The default, if omitted, is web.
- response_types: (Optional) Determines the authorization processing flow to be used
- grant_types: (Optional) Grant types that the client is declaring that it will restrict itself to using
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- acr_values: (Optional) Custom authentication script from the Gluu server.  Required for extended authentication.
- client_name: (Optional) Client application name
- client_jwks_uri: (Optional) URL for the client's JSON Web Key Set (JWKS) document
- client_token_endpoint_auth_method: (Optional) Requested client authentication method for the Token Endpoint
- client_request_uris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OpenID Connect Provider (OP)
- client_frontchannel_logout_uris: (Optional) Client application Logout URL
- client_sector_identifier_uri: (Optional) URL using the HTTPS scheme to be used in calculating pseudonymous identifiers by the OpenID Connect Provider (OP)
- contacts: (Optional) Array of e-mail addresses of people responsible for this client
- ui_locales: (Optional) End user's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- claims_locales: (Optional) End user's preferred languages and scripts for claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- client_id: (Optional) Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: (Optional) Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- claims_redirect_uri: (Optional) The URI to which the client wishes the authorization server to direct the requesting party’s user agent after completing its interaction
- conn_type: 'local' for oxd-server and 'web' for oxd-https-extension
- conn_type_value: 'oxd port number' for oxd-server type and ' oxd-https-extension URL' for  oxd-https-extension type


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


#### Get Client Token

The `get_client_token` method is used to get a token which is sent as an input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Parameters:**

- client_id: Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- op_host: URL of the OpenID Connect Provider (OP)
- op_discovery_path: (Optional) Path to discovery document. For example if it's https://client.example.com/.well-known/openid-configuration then path is blank. But if it is https://client.example.com/oxauth/.well-known/openid-configuration then path is oxauth
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)

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


#### Register Site

In order to use an OpenID Connect Provider (OP) for login, 
you need to register your client application at the OpenID Connect Provider (OP). 
During registration oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful registration, a unique 
identifier will be issued by the oxd-server. If your OpenID Connect Provider (OP) does not support dynamic registration (like Google), you will need to obtain a 
ClientID and Client Secret which can be passed to the `register_site` method as a 
parameter. The Register Site method is a one time task to configure a client in the 
oxd-server and OpenID Connect Provider (OP).

!!! Note: 
    The `register_site` endpoint is not required if client is registered using `setup_client`

**Parameters:**

- authorization_redirect_uri: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- op_host: URL of the OpenID Connect Provider (OP)
- post_logout_uri: (Optional) URL to which the user is redirected to after successful logout
- application_type: (Optional) Application type, the default values are native or web. The default, if omitted, is web.
- response_types: (Optional) Determines the authorization processing flow to be used
- grant_types: (Optional) Grant types the client is restricting itself to using
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- acr_values: (Optional) Required for extended authentication. Custom authentication script from Gluu server.
- client_name: (Optional) Client application name
- client_jwks_uri: (Optional) URL for the client's JSON Web Key Set (JWKS) document
- client_token_endpoint_auth_method: (Optional) Requested client authentication method for the Token Endpoint
- client_request_uris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OP
- client_frontchannel_logout_uris: (Optional) Client application Logout URL
- client_sector_identifier_uri: (Optional) URL using the HTTPS scheme to be used in calculating pseudonymous identifiers by the OpenID Connect Provider (OP)
- contacts: (Optional) Array of e-mail addresses of people responsible for this client
- ui_locales: (Optional) End user's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- claims_locales: (Optional) End user's preferred languages and scripts for claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- client_id: (Optional) Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: (Optional) Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- claims_redirect_uri: (Optional) The URI to which the client wishes the authorization server to direct the requesting party’s user agent after completing its interaction
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- conn_type: 'local' for oxd-server and 'web' for oxd-https-extension
- conn_type_value: 'oxd port number' for oxd-server type and ' oxd-https-extension URL' for  oxd-https-extension type


**Request:**

```python
oxd_id = client.register_site(protection_access_token=str(protection_access_token))
```

**Response:**

```python
oxd_id = 6F9619FF-8B86-D011-B42D-00CF4FC964FF
```


#### Update Site Registration

The `update_site_registration` method can be used to update an existing client in the OpenID Connect Provider (OP). 
Fields like Authorization Redirect URL, Post Logout URL, Scope, Client Secret and other fields can be updated using this method.

**Parameters:**

- oxd_id: oxd ID from client registration
- authorization_redirect_uri: (Optional) URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- post_logout_uri: (Optional) URL to which the RP is requesting the end user's user agent be redirected to after a logout has been performed
- response_types: (Optional) Determines the authorization processing flow to be used
- grant_types: (Optional) Grant types the client is restricting itself to using
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- acr_values: (Optional) Custom authentication script from the Gluu server. Required for extended authentication.
- client_name: (Optional) Client application name
- client_secret_expires_at: (Optional) Used to extend client lifetime (milliseconds since 1970)
- client_jwks_uri: (Optional) URL for the client's JSON Web Key Set (JWKS) document
- client_token_endpoint_auth_method: (Optional) Requested client authentication method for the Token Endpoint
- client_request_uris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OpenID Connect Provider (OP)
- client_frontchannel_logout_uris: (Optional) Client application Logout URL
- client_sector_identifier_uri: (Optional) URL using the HTTPS scheme to be used in calculating pseudonymous identifiers by the OpenID Connect Provider (OP)
- contacts: (Optional) Array of e-mail addresses of people responsible for this client
- ui_locales: (Optional) End user's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- claims_locales: (Optional) End user's preferred languages and scripts for claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- connection_type_value: 'oxd port number' for oxd-server type and 'oxd-https-extension URL' for oxd-https-extension  type
- connection_type: 'local' for oxd-server and 'web' for oxd-https-extension

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


#### Get Authorization URL

The `get_authorization_url` method returns the OpenID Connect Provider (OP) 
Authentication URL to which the client application must redirect the user to 
authorize the release of personal data. The Response URL includes state value, 
which can be used to obtain tokens required for authentication. This state value is used 
to maintain state between the request and the callback.

**Parameters:**

- oxd_id: oxd ID from client registration
- scope: (Optional) A scope is an indication by the client that it wants to access some resource
- acr_values: (Optional) Custom authentication script from the Gluu server.  Required for extended authentication. 
- prompt: (Optional) Values that specifies whether the Authorization Server prompts the end user for reauthentication and consent
- custom_params: (Optional) Custom parameters
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
auth_url = oxc.get_authorization_url(custom_params=custom_params, protection_access_token=str(protection_access_token))
```

**Response:**

```python
auth_url = https://client.example.com/authorize?response_type=code&client_id=s6BhdRkqt3&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb&scope=openid%20profile&acr_values=duo&state=af0ifjsldkj&nonce=n-0S6_WzA2Mj&param2=value2&param1=value1
```


#### Get Tokens by Code

Upon successful login, the login result will return code and state. `get_tokens_by_code` method 
uses code and state to retrieve token which can be used to access user claims.

**Parameters:**

- oxd_id: oxd ID from client registration
- code: The code from OpenID Connect Provider (OP) Authorization Redirect URL
- state: The state from OpenID Connect Provider (OP) Authorization Redirect URL
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


#### Get Access Token by Refresh Token

The `get_access_token_by_refresh_token` method is used to get a new access token and a new refresh token by using the refresh token which is obtained from `get_tokens_by_code` method.

**Parameters:**

- oxd_id: oxd ID from client registration
- refreshToken: Obtained from the get_tokens_by_code method
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
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


#### Get User Info

Once the user has been authenticated by the OpenID Connect Provider (OP), 
the `get_user_info` method returns the claims (First Name, Last Name, E-Mail ID, etc.) about the authenticated end user.

**Parameters:**

- oxd_id: oxd ID from client registration
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


#### Logout

`get_logout_uri` method returns the OpenID Connect Provider (OP) Logout URL. 
Client application uses this Logout URL to end the user session.

**Parameters:**

- oxd_id: oxd ID from client registration
- id_token_hint: (Optional) ID Token previously issued by the Authorization Server being passed as a hint about the end user's current or past authenticated session with the client
- post_logout_redirect_uri: (Optional) URL to which user is redirected to after successful logout
- state: (Optional) Value used to maintain state between the request and the callback
- session_state: (Optional) JSON string that represents the end user’s login state at the OpenID Connect Provider (OP)
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


### UMA (User Managed Access)

#### RS Protect

`uma_rs_protect` method is used for protecting resources by the Resource Server. The Resource Server is needed to construct the command which will protect the resource.
The command will contain an API path, HTTP methods (POST, GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy is mapped, uma_rs_check_access method will always return access as granted. For more information about uma_rpt_policies you can reference this [document](https://gluu.org/docs/oxd/3.1.1/api/#uma-2-client-apis).

**Parameters:**

- oxd_id: oxd ID from client registration
- resources: One or more protected resources that a resource server manages, abstractly, as a set. In authorization policy terminology, a resource set is the "object" being protected. 
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


#### RS Check Access 

`uma_rs_check_access` method is used in the UMA Resource Server to check the access to the resource.

**Parameters:**

- oxd_id: oxd ID from client registration
- rpt: Requesting Party Token
- path: Path of the resource to be checked 
- http_method: HTTP methods (POST, GET and PUT)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
result = oxc.uma_rs_check_access(rpt=str(rpt), path=path, http_method=http_method, protection_access_token=str(protection_access_token))
```

**Response:**

***Access Granted Response:***

```javascript
{
    "status":"ok",
    "data":{
        "access":"granted"
    }
}
```

***Access Denied with Ticket Response:***

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

***Access Denied without Ticket Response:***

```javascript
{
    "status":"ok",
    "data":{
        "access":"denied"
    }
}
```

***Resource is not Protected Response:***

```javascript
{
    "status":"error",
    "data":{
        "error":"invalid_request",
        "error_description":"Resource is not protected. Please protect your resource first with uma_rs_protect command."
    }
}
```


#### RP Get RPT 

The method uma_rp_get_rpt is called in order to obtain the RPT (Requesting Party Token). 

**Parameters:**

- oxd_id: oxd ID from client registration
- ticket: Client Access Ticket generated by uma_rs_check_access method
- claim_token: (Optional) A package of claims provided by the client to the authorization server through claims pushing
- claim_token_format: (Optional) A string containing directly pushed claim information in the indicated format. Must be base64url encoded unless otherwise specified.  
- pct: (Optional) Persisted Claims Token
- rpt: (Optional) Requesting Party Token
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- state: (Optional) State that is returned from uma_rp_get_claims_gathering_url method
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
result = oxc.uma_rp_get_rpt(ticket=str(ticket), scope='Test_scope', protection_access_token=str(protection_access_token))
```

**Response:**

***Success Response:***

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

***Needs Info Error Response:***

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

***Invalid Ticket Error Response:***

```javascript
 {
    "status":"error",
    "data":{
            "error":"invalid_ticket",
            "error_description":"Ticket is not valid (outdated or not present on Authorization Server)."
           }
 }
```


#### RP Get Claims Gathering URL 

**Parameters:**

- oxd_id: oxd ID from client registration
- ticket: Client Access Ticket generated by uma_rs_check_access method
- claims_redirect_uri: The URI to which the client wishes the authorization server to direct the requesting party’s user agent after completing its interaction
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
Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.
