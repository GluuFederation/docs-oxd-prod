# oxd-python

## Overview

Use oxd's Python library to send users from a Python application to your Gluu Server OpenID Connect Provider (OP) for dynamic enrollment, single sign-on (SSO), strong authentication, and access management policy enforcement. 


## Public Member Functions

- setup_client (self)
- get_client_token (self)
- register_site (self, protection_access_token=None)
- update_site_registration (self, protection_access_token=None)
- get_authorization_url (self, scope=None, acr_values=None, prompt=None, custom_params=None, protection_access_token=None)
- get_tokens_by_code (self, code, state, protection_access_token=None)
- get_access_token_by_refresh_token (self, refresh_token, scope=None, protection_access_token=None)
- get_user_info (self, access_token, protection_access_token=None)
- get_logout_uri (self, id_token_hint=None, post_logout_redirect_uri=None, state=None, session_state=None, protection_access_token=None)
- uma_rs_protect (self, resources, protection_access_token=None)
- uma_rs_check_access (self, rpt, path, http_method, protection_access_token=None)
- uma_rp_get_rpt (self, ticket, claim_token=None, claim_token_format=None, pct=None, rpt=None, scope=None, state=None, protection_access_token=None)
- uma_rp_get_claims_gathering_url (self, ticket, claims_redirect_uri, protection_access_token=None)


## Public Attributes

- config: contains the values from the configuration file
- conn_type: oxd connection type. 'local' for oxd-server and 'web' for oxd-https-extension
- conn_type_value: 'oxd port number' for oxd-server type and ' oxd-https-extension URL' for oxd-https-extension type
- authorization_redirect_uri: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- oxd_id: oxd ID from client registration
- client_name: Client application name
- client_id: Client ID from OpenID Connect Provider (OP)
- client_secret: Client Secret from OpenID Connect Provider (OP)
- op_host: URL of the OpenID Connect Provider (OP)
- ophost_type: If the OpenID Provider has registration_endpoint then the value is dynamic, else static
- claims_redirect_uri: The URI to which the client wishes the authorization server to direct the requesting party’s user agent after completing its interaction
- opt_params: contains the optional parameter list of string type. It includes "op_host",
                           "post_logout_redirect_uri",
                           "client_name",
                           "client_jwks_uri",
                           "client_token_endpoint_auth_method",
                           "client_id",
                           "client_secret",
                           "client_secret_expires_at",
                           "application_type",
- opt_list_params: contains the optional parameter list of list type. It includes "grant_types",
                                "acr_values",
                                "contacts",
                                "client_frontchannel_logout_uris",
                                "client_request_uris",
                                "client_sector_identifier_uri",
                                "response_types",
                                "scope",
                                "ui_locales",
                                "claims_locales",
                                "claims_redirect_uri"

## Sample Code

### OpenID Connect

#### Setup Client

In order to use an OpenID Connect Provider (OP) for login, you need to setup your client application at the OP. During setup, oxd will dynamically register the OpenID Connect client and save its configuration. Upon successful setup, the `oxd-server` will assign a unique oxd ID, return a Client ID and Client Secret. This Client ID and Client Secret can be used for `get_client_token` method. 

If your OP does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be passed to the `setup_client` method as a parameter. The Setup Client method is a one time task to configure a client in the oxd-server and OpenID Connect Provider (OP).


```python
from oxdpython import Client

config = "/var/www/demosite/demosite.cfg"  # This should be writable by the server
oxc = Client(config)
```

**Request:**

```python
client_data = oxc.setup_client()

print client_data.oxd_id
print client_data.client_id
print client_data.client_secret
```

**Response:**

```python
oxd_id = 6F9619FF-8B86-D011-B42D-00CF4FC964FF
op_host = https://iam.centroxy.com
client_id = @!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!A125.EE21.F0E2.8C08
client_secret = f8eb25a0-f69b-4e4f-8058-ba9cbe6bf7eb
client_registration_access_token = 57a75a36-3cc1-4df7-a496-1a4204674bec
client_registration_client_uri = https://iam.centroxy.com/oxauth/restv1/register?client_id=@!4116.DF7C.62D4.D0CF!0001!D420.A5E5!0008!2B0D.4E1F.D675.859F
client_id_issued_at = 1511775097
client_secret_expires_at = 1511861497
```


#### Get Client Token

The `get_client_token` method is used to get a token which is sent as an input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Request:**

```python
response = oxc.get_client_token()

print response.access_token
```

**Response:**

```python
access_token = d9e64cbe-e096-4406-a030-8dd7542871d0
scope = openid
expires_in = 299 
refresh_token = null
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

- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
oxd_id = client.register_site(protection_access_token)

print oxd_id
```

**Response:**

```python
oxd_id = 6F9619FF-8B86-D011-B42D-00CF4FC964FF
```


#### Update Site Registration

The `update_site_registration` method can be used to update an existing client in the OpenID Connect Provider (OP). 
Fields like Authorization Redirect URL, Post Logout URL, Scope, Client Secret and other fields can be updated using this method.

**Parameters:**

- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
status = oxc.update_site_registration(protection_access_token)

print status
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

- scope: (Optional) A scope is an indication by the client that it wants to access some resource
- acr_values: (Optional) Custom authentication script from the Gluu server.  Required for extended authentication. 
- prompt: (Optional) Values that specifies whether the Authorization Server prompts the end user for reauthentication and consent
- custom_params: (Optional) Custom parameters
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
custom_params = {
    "param1":"value1",
    "param2":"value2"
}

authorization_url = oxc.get_authorization_url(scope, acr_values, prompt, custom_params, protection_access_token)

print authorization_url
```

**Response:**

```python
authorization_url = https://client.example.com/authorize?response_type=code&client_id=s6BhdRkqt3&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb&scope=openid%20profile&acr_values=basic&state=af0ifjsldkj&nonce=n-0S6_WzA2Mj&param2=value2&param1=value1
```


#### Get Tokens by Code

Upon successful login, the login result will return code and state. `get_tokens_by_code` method 
uses code and state to retrieve token which can be used to access user claims.

**Parameters:**

- code: The code from OpenID Connect Provider (OP) Authorization Redirect URL
- state: The state from OpenID Connect Provider (OP) Authorization Redirect URL
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
tokens = oxc.get_tokens_by_code(code, state, protection_access_token)

print tokens.access_token
print tokens.refresh_token
```

**Response:**

```python
access_token = SlAV32hkKG
expires_in = 299
id_token = eyJraW....5ZDE0MjVh
refresh_token = aaAV32hkKG1
id_token_claims.at_hash[0] = iTSn9fLxTMUF4vrQPN2dHg
id_token_claims.aud[0] = @!4116.DF7C.62D4.D0CF!0001!D420.A5E5!0008!4663.8750.9D20.5DF3
id_token_claims.sub[0] = tc-XhfG_JoxvDs-SXr1yNencSmOvi-sW73U1lipzrew
id_token_claims.auth_time[0] = 1511775378
id_token_claims.iss[0] = https://ophost_url
id_token_claims.exp[0] = 1511778988
id_token_claims.iat[0] = 1511775388
id_token_claims.nonce[0] = 38mrbnbe3fkkfqamro9s8171f4
id_token_claims.oxOpenIDConnectVersion[0] = openidconnect-1.0
```


#### Get Access Token by Refresh Token

The `get_access_token_by_refresh_token` method is used to get a new access token and a new refresh token by using the refresh token which is obtained from `get_tokens_by_code` method.

**Parameters:**

- refreshToken: Obtained from the get_tokens_by_code method
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
newtokens = oxc.get_access_token_by_refresh_token(tokens.refresh_token, scope, protection_access_token)

print newtokens.access_token
print newtokens.refresh_token
```

**Response:**

```python
access_token = ALSAKLDDKN9787DYAH
scope = openid
expires_in = 299
refresh_token = gh75UHGHHS88sd
```


#### Get User Info

Once the user has been authenticated by the OpenID Connect Provider (OP), 
the `get_user_info` method returns the claims (First Name, Last Name, E-Mail ID, etc.) about the authenticated end user.

**Parameters:**

- access_token: access_token from get_tokens_by_code or get_access_token_by_refresh_token
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
user = oxc.get_user_info(access_token, protection_access_token)

print user.name[0]
print user.email[0]
```

**Response:**

```python
user = {given_name=["Jane"], family_name=["Doe"], email=["janedoe@example.com"], sub=["248289761001"], name=["Jane Doe"] }
```


#### Logout

`get_logout_uri` method returns the OpenID Connect Provider (OP) Logout URL. 
Client application uses this Logout URL to end the user session.

**Parameters:**

- id_token_hint: (Optional) ID Token previously issued by the Authorization Server being passed as a hint about the end user's current or past authenticated session with the client
- post_logout_redirect_uri: (Optional) URL to which user is redirected to after successful logout
- state: (Optional) Value used to maintain state between the request and the callback
- session_state: (Optional) JSON string that represents the end user’s login state at the OpenID Connect Provider (OP)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
uri = oxc.get_logout_uri(id_token_hint, post_logout_redirect_uri, state, session_state, protection_access_token)

print uri
```

**Response:**

```python
uri = https://<server>/end_session?id_token_hint=<id token>&state=<state>&post_logout_redirect_uri=<...>
```


### UMA (User Managed Access)

#### RS Protect

`uma_rs_protect` method is used for protecting resources by the Resource Server. The Resource Server is needed to construct the command which will protect the resource.
The command will contain an API path, HTTP methods (POST, GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy is mapped, uma_rs_check_access method will always return access as granted. For more information about uma_rpt_policies you can reference this [document](https://gluu.org/docs/oxd/3.1.1/api/#uma-2-client-apis).

**Parameters:**

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

result = oxc.uma_rs_protect(resources, protection_access_token)

print result.status
```

**Response:**

```python
status = ok
```


#### RS Check Access 

`uma_rs_check_access` method is used in the UMA Resource Server to check the access to the resource.

**Parameters:**

- rpt: Requesting Party Token
- path: Path of the resource to be checked 
- http_method: HTTP methods (POST, GET and PUT)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
result = oxc.uma_rs_check_access(rpt, path, http_method, protection_access_token)

print result.data.access
```

**Response:**

***Access Granted Response:***

```javascript
access = granted
```

***Access Denied with Ticket Response:***

```javascript
access = denied
www-authenticate_header = UMA realm=\"example\",
                                   as_uri=\"https://as.example.com\",
                                   error=\"insufficient_scope\",
                                   ticket=\"016f84e8-f9b9-11e0-bd6f-0021cc6004de\"
ticket = 016f84e8-f9b9-11e0-bd6f-0021cc6004de
```

***Access Denied without Ticket Response:***

```javascript
access = denied
```

***Resource is not Protected Response:***

```javascript
error = invalid_request
error_description = Resource is not protected. Please protect your resource first with uma_rs_protect command.
```


#### RP Get RPT 

The method uma_rp_get_rpt is called in order to obtain the RPT (Requesting Party Token). 

**Parameters:**

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
result = oxc.uma_rp_get_rpt(ticket, claim_token, claim_token_format, pct, rpt, scope, state, protection_access_token)

print result.data.access_token
```

**Response:**

***Success Response:***

```javascript
access_token = SSJHBSUSSJHVhjsgvhsgvshgsv
token_type = Bearer
pct = c2F2ZWRjb25zZW50
upgraded = true
```

***Needs Info Error Response:***

```javascript
error = need_info
error_description = The authorization server needs additional information in order to determine whether the client is authorized to have these permissions.
details.error = need_info
details.ticket = ZXJyb3JfZGV0YWlscw==
details.required_claims[0].claim_token_format[0] = http://openid.net/specs/openid-connect-core-1_0.html#IDToken"
details.required_claims[0].claim_type = urn:oid:0.9.2342.19200300.100.1.3
details.required_claims[0].friendly_name = email
details.required_claims[0].issuer = https://example.com/idp
details.required_claims[0].name = email23423453ou453
details.redirect_user = https://as.example.com/rqp_claims?id=2346576421
```

***Invalid Ticket Error Response:***

```javascript
error = invalid_ticket
error_description = Ticket is not valid (outdated or not present on Authorization Server)
```


#### RP Get Claims Gathering URL 

**Parameters:**

- ticket: Client Access Ticket generated by uma_rs_check_access method
- claims_redirect_uri: The URI to which the client wishes the authorization server to direct the requesting party’s user agent after completing its interaction
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```python
result = oxc.uma_rp_get_claims_gathering_url(ticket, claims_redirect_uri, protection_access_token)

print result.data.url
```

**Response:**

```javascript
url = https://as.com/restv1/uma/gather_claims
              ?client_id=@!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!AB77!1A2B
              &ticket=4678a107-e124-416c-af79-7807f3c31457
              &claims_redirect_uri=https://client.example.com/cb
              &state=af0ifjsldkj
state = af0ifjsldkj
```


## Sample Project

Download a [Sample Project](https://github.com/GluuFederation/oxd-python/archive/3.1.1.zip) specific to this oxd-python library.

### Software Requirements

System Requirements:

- Ubuntu / Debian / CentOS / RHEL / Windows Server 2008 or higher
- Python 2.7
- Flask
- Flask-SSLify

To use the oxd-python library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google.    
- An active installation of the [oxd-server](../../../install/index.md) running on the same server as the client application.
- If you want to make RESTful (https) calls from your app to your `oxd-server`, you will need an active installation of the [oxd-https-extension](../../../oxd-https/start/index.md)).
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

```
$ cd /opt/
$ git clone -b 3.1.1 --single-branch https://github.com/GluuFederation/oxd-python.git
cd /opt/oxd-python/
```

- This oxd python library uses a configuration file (demosite.cfg) to specify 
information needed by OpenID Connect dynamic client registration, and to save 
information that is returned, like the oxd_id. So the config file needs to be 
writable by the Client application.


	The minimal configuration required to get oxd-python working:

```
    [oxd]
    connection_type = local
    connection_type_value = 8099
    id = <oxd_id>
    
    [client]
    op_host = <https://ophost_url>
    authorization_redirect_uri = https://client.example.com:8080/userinfo
    post_logout_redirect_uri = https://client.example.com:8080
    client_frontchannel_logout_uris = https://client.example.com:8080/logout
    scope = openid,profile,email
    client_id = <client_id>
    client_secret = <client_secret>
    dynamic_registration = true
```

**oxd-server configuration sample:**
```
connection_type = local
connection_type_value = 8099
```

**oxd-https-extension configuration sample:**
```
connection_type = web
connection_type_value = https://127.0.0.1:8443
```
- run below command to start oxd-python Sample client application
```
cd /opt/oxd-python/demosite/
$ python demosite.py
```
- With the oxd-server running, navigate to the URL's below to run the sample client application. To register a client in the oxd-server use the Setup Client URL. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, navigate to the Login URL for authentication.

    - Setup Client URL: https://client.example.com:8080/setupClient
    - Login URL: https://client.example.com:8080
    - UMA URL: https://client.example.com:8080/uma
- Upon successful registration (Setup client url) of the client application oxd_id is set to the 
field `id` in demosite.cfg.


!!!Note: 
    The [sample.cfg](https://github.com/GluuFederation/oxd-python/blob/3.1.1/sample.cfg)
    file contains detailed documentation about the configuration values.


## Support

Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.
