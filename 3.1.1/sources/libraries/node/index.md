# oxd-node

## Overview
The following documentation demonstrates 
how to use the [oxd client software](http://oxd.gluu.org) Node.js library to 
send users from a Node.js application to an OpenID Connect Provider (OP), 
like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 


## Sample Project

[Download a sample project](https://github.com/GluuFederation/oxd-node/archive/3.1.1.zip) specific to this oxd library.

### System Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows 7 or higher / Windows Server 2008 or higher
- Node 6.11.0
- npm 3.10.10
- oxd-node 3.1.2

## Prerequisites

### Required Software
To use the oxd Node.js library, you will need:

- A valid OpenID Connect Provider (OP), like Google or the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/).    
- An active installation of the [oxd server](../../install/index.md) running in the same server as the client application.
- An active installation of the [oxd https extension](../../install/index.md) if client applications are on different server than oxd server.
- A Windows server or Windows installed machine / Linux server or Linux installed machine.


### Install oxd-node via npm

To install oxd-node via npm, run following commands in Linux terminal or 
Windows command window

``` {.code }
npm install oxd-node@3.1.2
```

### Configure the Client Application

- Your client application must have a valid ssl cert, so the url includes: `https://`

- Enable SSL by	setting the valid certificate and key in your application "index.js" file:

```javascript
var options = {
    key: fs.readFileSync(__dirname + '/<key file name>.pem'),
    cert: fs.readFileSync(__dirname + '/<certificate file name>.pem')
};

```
    
- The client host name should be a valid `hostname`(FQDN), not localhost or an IP Address. 
You can configure the host name by adding the following entry in your host file.

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`

- To install dependencies for this application navigate to the "oxd-node-demo" folder in terminal(linux)/commandprompt(windows) and run

```shell
npm install
```
- Open the downloaded [sample project](https://github.com/GluuFederation/oxd-node/archive/3.1.1.zip) and navigate to `oxd-node-demo` directory inside the project.

- The port number for this application to run can be set in "properties.js" file, which you can use to run this application in any free port. By default the port number is set to "5053" which we will assume as the port number of this application for further parts of this documentation.


- Run this project with "Node.js" with the following command from the same folder i.e. "oxd-node-demo".
```shell
node index.js
```
- That's it!. Now navigate to following url to run Sample client application. Make sure oxd server is running. Setup client url can be used for registering Client in oxd server. Upon successful registration of the client application, oxd Id will be displayed in the UI. Then navigate to Login URL for authentication.

    - setup_client url: https://client.example.com:5053/settings
    - Login URL: https://client.example.com:5053/login
    - UMA URL: https://client.example.com:5053/uma


- This oxd Node.js library uses two configuration file (`settings.json` and `parameters.json`) to specify information needed by OpenID Connect dynamic client registration, and to save information that is returned, like the oxd_id,clieni_id,client_secret etc. So the config file needs to be writable by the Client application.


## oxd Server Methods

TThe oxd server provides the following methods for authenticating users with 
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


## oxd https extension methods

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


**Request:**

```javascript
oxd.Request.authorization_redirect_uri = req.body.redirect_uri;
oxd.Request.op_host = req.body.op_host;
oxd.Request.client_frontchannel_logout_uris = req.body.post_logout_uri;
oxd.Request.post_logout_redirect_uri = req.body.post_logout_uri;
oxd.Request.port = req.body.oxd_local_value;
oxd.Request.scopes = gluu_scopes;
oxd.Request.client_name = req.body.client_name;
oxd.Request.url = url;
oxd.setup_client(oxd.Request, function(response) {
    console.log(response);
}
```

**Response:**

```javascript
{
   "status":"ok",
   "data":{
      "oxd_id":"3157eea6-ae23-4fab-bc30-67bf7b232fa2",
      "op_host":"<op host name>",
      "client_id":"@!81F6.6C2A.5FCE.C408!0001!28BA.CADF!0008!8BAE.6164.744B.1179",
      "client_secret":"fe1125ad-a690-4445-80c0-07689b150567",
      "client_registration_access_token":"1aa82ea6-5d6d-4e30-9bb6-65d2b9a76ace",
      "client_registration_client_uri":"<op host name>/oxauth/restv1/register?client_id=@!81F6.6C2A.5FCE.C408!0001!28BA.CADF!0008!8BAE.6164.744B.1179",
      "client_id_issued_at":1506538221,
      "client_secret_expires_at":1506624621
   }
}
```

### Get Client Token

The `get_client_token` method is used to get a token which is sent as input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Required parameters:**

- op_host: OpenId Connect Provider
- client_id: Client Id from OpenID Connect Provider (Should be passed with Client Secret)
- client_secret: Client Secret from OpenID Connect Provider (Should be passed with Client Id)

**Request:**

```javascript
oxd.Request.op_host = req.body.op_host;
oxd.Request.client_id = obj.client_id;
oxd.Request.client_secret = obj.client_secret;
oxd.get_client_access_token(oxd.Request, function(access_token_response){
    console.log(access_token_response);
}
```

**Response:**

```javascript
{
   "status":"ok",
   "data":{
      "scope":"openid",
      "access_token":"c9561a2b-0d20-4dc1-a047-9dd36a9ef932",
      "expires_in":299,
      "refresh_token":null
   }
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
- op_host: (required) OpenID Connect provider
- authorization_redirect_uri: (required) URI to redirect after successful login.
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```javascript
param.op_host = request.op_host;
param.authorization_redirect_uri = request.authorization_redirect_uri;
param.scope = request.scope;
param.contacts = request.contacts;
param.application_type = request.application_type;
param.post_logout_redirect_uri = request.post_logout_redirect_uri;
param.redirect_uris = request.redirect_uris;
param.response_types = request.response_types;
param.client_id = request.client_id;
param.client_secret = request.client_secret;
param.client_jwks_uri = request.client_jwks_uri;
param.client_token_endpoint_auth_method = request.client_token_endpoint_auth_method;
param.client_request_uris = request.client_request_uris;
param.client_logout_uris = request.client_logout_uris;
param.client_sector_identifier_uri = request.client_sector_identifier_uri;
param.ui_locales = request.ui_locales;
param.claims_locales = request.claims_locales;
param.acr_values = request.acr_values;
param.grant_types = request.grant_types;
```

**Response:**

```javascript
{
    "status":"ok",
    "data":{
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF"
    }
}
```

### Update Site Registration

TThe `update_site_registration` method can be used to update an existing client in the OP. 
Fields like Authorization redirect url, post logout url, scope, client secret and other fields, 
can be updated using this method.

**Required parameters:**
- oxd_id: (required) oxd Id from client registration
- client_name: (Optional) Client application name
- contacts: (Optional) User's email Id
- authorization_redirect_uri:  (Optional) URL which the OP is authorized to redirect the user after authorization.
- post_logout_uri: (Optional) URL to which the RP is requesting that the 
End-User's User Agent be redirected after a logout has been performed.
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```javascript
authorization_redirect_uri = request.form['redirect_uri']
client_name = request.form['client_name']
post_logout_uri = request.form['post_logout_uri']
conn_type_value = request.form['conn_type_name']
conn_type = request.form['conn_type_radio']

status = oxc.update_site_registration(client_name=str(client_name), authorization_redirect_uri=str(redirect_uri), post_logout_uri=str(post_logout_uri), connection_type_value=str(conn_type_value), connection_type=str(conn_type), protection_access_token=str(protection_access_token))
```
**Response:**

```javascript
{
   "status":"ok",
   "data":{
      "oxd_id":"3157eea6-ae23-4fab-bc30-67bf7b232fa2"
   }
}
```

### Get Authorization URL

The `get_authorization_url` method returns the OpenID Connect Provider 
authentication URL to which the client application must redirect the user to 
authorize the release of personal data. The response URL includes state value, 
which can be used to obtain tokens required for authentication. This state value used 
to maintain state between the request and the callback.

**Required parameters:**
- oxd_id: (required) oxd Id from client registration
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by OP
- acr_values: (Optional) Required for extened authenication. Custom Authentication script from Gluu server. 
- prompt: (Optional)
- custom_params: (Optional) custom parameters
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.scope=["openid","profile","email","uma_protection","uma_authorization"];
oxd.Request.custom_parameters = {
    param1 : "value1",
    param2 : "value2"
};
oxd.Request.protection_access_token = access_token_data.data.access_token;

```

**Response:**

```javascript
{
   "status":"ok",
   "data":{
      "authorization_url":"<op host name>/oxauth/restv1/authorize?response_type=code&client_id=@!81F6.6C2A.5FCE.C408!0001!28BA.CADF!0008!4556.1F73.37B4.58A3&redirect_uri=https://client.example.com:5053/login_redirect&scope=openid+profile+email+uma_protection+uma_authorization&state=59qn4ebb3b2v3idnvbge92ja52&nonce=cdrnf8dgpm1rjbora0q9ev0u0&custom_response_headers=%5B%7B%22param1%22%3A%22value1%22%7D%2C%7B%22param2%22%3A%22value2%22%7D%5D"
   }
}
```

### Get Tokens by Code

Upon successful login, the login result will return code and state. `get_tokens_by_code` 
uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**
- oxd_id: (required) oxd Id from client registration
- code: The Code from OP authorization redirect url
- state: The State from OP authorization redirect url
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.code = url_parts.query.code;
oxd.Request.state = url_parts.query.state;
oxd.Request.protection_access_token = access_token_data.data.access_token;
```

**Response:**

```javascript
{
   "status":"ok",
   "data":{
      "access_token":"1fd5b6b4-ffe3-49e3-b979-c277eacc53e9",
      "expires_in":299,
      "id_token":"eyJraWQiOiJjYTU3NmIzZC0zNjVmLTRjN2EtOGFmYi1lYzE4NTM1N2E2YWYiLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwczovL2lhbTMxMC5jZW50cm94eS5jb20iLCJhdWQiOiJAITgxRjYuNkMyQS41RkNFLkM0MDghMDAwMSEyOEJBLkNBREYhMDAwOCE0NTU2LjFGNzMuMzdCNC41OEEzIiwiZXhwIjoxNTA2NTQyNTEzLCJpYXQiOjE1MDY1Mzg5MTMsIm5vbmNlIjoia3NscGE2NnVyaXBvY2JjbTg0MG82aTZ2bjIiLCJhdXRoX3RpbWUiOjE1MDY1Mzg5MDAsImF0X2hhc2giOiJFR3RFVTNsa3B6aVkxbENwWXVsVEZ3Iiwib3hPcGVuSURDb25uZWN0VmVyc2lvbiI6Im9wZW5pZGNvbm5lY3QtMS4wIiwic3ViIjoiM011MThmSzBfWk9LYjU2cFZVRHAwc1FFbHV3RTVqMXRwUGZ1MFBLMVlaRSJ9.o-FvttJmt5gNeSiqyQWio5r4_Gw20DAeNsWnlMQM03XPtRhZQ8Jd0a_8Y_vOZr5YolwsBJQOwkqAZZkDnHJT_utlMRYEZ_vP5AoGS_kwTHFH37g3S1xhc4HxmJiJGl0Rv3id6w5pJyxkiInKKSAJexWIlM_wGp8iWHfcQ4NU9NgdsHkPmpyMZHhdaqV_6MpPut9BqjWez8BQazY5oJ8WTE-nQ_bBhlS8RSzIES1UTbdYVZsizQ77lfrroybZTPQxdfl0a_iCLy4BjR_ih4V9dhsY6NK7sFh6WNArOjjIOTPdk3MStwnI19hJW5xDDsyRFs9YZ7X0kecPC87xw20ntg",
      "refresh_token":"1ffe5bf0-6cbc-44e9-a1a9-cfdb48f2a499",
      "id_token_claims":{}
   }
}
```
### Get Access Token by Refresh Token

The `get_access_token_by_refresh_token` method is used to get a new access token and a new refresh token by using the refresh token which is obtained from `get_tokens_by_code` method.

**Required parameters:**
- oxd_id: (required) oxd Id from client registration
- refreshToken: Obtained from the get_tokens_by_code method
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by OP
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.refresh_token = response.data.refresh_token;
oxd.Request.scope = oxd.Request.scope=["openid","profile","email","uma_protection","uma_authorization"];
oxd.Request.protection_access_token = access_token_data.data.access_token;
```

**Response:**

```javascript
{  
   "status":"ok",
   "data":{  
      "scope":"openid profile uma_protection uma_authorization email",
      "access_token":"147a8147-b7bc-40f4-b280-18449a140d42",
      "expires_in":299,
      "refresh_token":"57ad0c8b-4da8-45d7-8cf0-e348dca75dad"
   }
}
```

### Get User Info

Once the user has been authenticated by the OpenID Connect Provider, 
the `get_user_info` method returns Claims (Like First Name, Last Name, emailId, etc.) 
about the authenticated end user.

**Required parameters:**
- oxd_id: (required) oxd Id from client registration
- access_token: access_token from GetTokenByCode or GetAccessTokenbyRefreshToken
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.access_token = access_token_data.data.access_token;
oxd.Request.protection_access_token = access_token_data.data.access_token;
```

**Response:**

```javascript
{  
   "status":"ok",
   "data":{  
      "claims":{  
         "sub":[  
            "3Mu18fK0_ZOKb56pVUDp0sQEluwE5j1tpPfu0PK1YZE"
         ],
         "updated_at":[  
            "20170925070950.669Z"
         ],
         "name":[  
            "Jane Doe"
         ],
         "given_name":[  
            "Jane"
         ],
         "locale":[  
            "en"
         ],
         "family_name":[  
            "Doe"
         ],
         "email":[  
            "janedoe@example.com"
         ]
      },
      "refresh_token":null,
      "access_token":null
   }
}
```

### Logout

`get_logout_uri` method returns the OpenID Connect Provider logout url. 
Client application  uses this logout url to end the user session.

**Required parameters:**
- oxd_id: (required) oxd Id from client registration
- id_token_hint: (Optional)
- post_logout_redirect_uri: (Optional) URL to which user is redirected after successful logout
- state: (Optional)
- session_state: (Optional)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.protection_access_token = access_token_data.data.access_token;
```

**Response:**

```javascript
{  
   "status":"ok",
   "data":{  
      "uri":"<op host name>/oxauth/restv1/end_session?id_token_hint=eyJraWQiOiJjYTU3NmIzZC0zNjVmLTRjN2EtOGFmYi1lYzE4NTM1N2E2YWYiLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwczovL2lhbTMxMC5jZW50cm94eS5jb20iLCJhdWQiOiJAITgxRjYuNkMyQS41RkNFLkM0MDghMDAwMSEyOEJBLkNBREYhMDAwOCE0NTU2LjFGNzMuMzdCNC41OEEzIiwiZXhwIjoxNTA2NTQyNTEzLCJpYXQiOjE1MDY1Mzg5MTMsIm5vbmNlIjoia3NscGE2NnVyaXBvY2JjbTg0MG82aTZ2bjIiLCJhdXRoX3RpbWUiOjE1MDY1Mzg5MDAsImF0X2hhc2giOiJFR3RFVTNsa3B6aVkxbENwWXVsVEZ3Iiwib3hPcGVuSURDb25uZWN0VmVyc2lvbiI6Im9wZW5pZGNvbm5lY3QtMS4wIiwic3ViIjoiM011MThmSzBfWk9LYjU2cFZVRHAwc1FFbHV3RTVqMXRwUGZ1MFBLMVlaRSJ9.o-FvttJmt5gNeSiqyQWio5r4_Gw20DAeNsWnlMQM03XPtRhZQ8Jd0a_8Y_vOZr5YolwsBJQOwkqAZZkDnHJT_utlMRYEZ_vP5AoGS_kwTHFH37g3S1xhc4HxmJiJGl0Rv3id6w5pJyxkiInKKSAJexWIlM_wGp8iWHfcQ4NU9NgdsHkPmpyMZHhdaqV_6MpPut9BqjWez8BQazY5oJ8WTE-nQ_bBhlS8RSzIES1UTbdYVZsizQ77lfrroybZTPQxdfl0a_iCLy4BjR_ih4V9dhsY6NK7sFh6WNArOjjIOTPdk3MStwnI19hJW5xDDsyRFs9YZ7X0kecPC87xw20ntg"
   }
}
```
### UMA RS Protect

`uma_rs_protect` method is used for protecting resource by the Resource Server. Resource server need to construct the command which will protect the resource.
The command will contain api path, http methods (POST,GET, PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy mapped, uma_rs_check_access method will always return access as granted. To know more about uma_rpt_policies you can check this [document]().

**Required parameters:**
- oxd_id: (required) oxd Id from client registration
- resources: One or more protected resources that a resource server manages as a set, abstractly. In authorization policy terminology, a resource set is the "object" being protected. 
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```javascript
var resources = [
                    {
                        path: "/photo",
                        conditions: [
                            {
                                httpMethods:["GET"],
                                scopes:["https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1"],
                                ticketScopes:["https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1"]
                            }
                        ]
                    }
                ];
oxd.Request.resources = resources;
oxd.Request.protection_access_token = access_token_data.data.access_token;
```

**Response:**

```javascript
result = true
```

### UMA RS Check Access 

`uma_rs_check_access` method used in a UMA Resource Server to check the access to the resource.

**Required parameters:**
- oxd_id: (required) oxd Id from client registration
- rpt: Requesting Party Token
- path: Path of the resource to be checked 
- http_method: http metho like POST, GET, PUT
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.rpt = "";
oxd.Request.http_method = "GET";
oxd.Request.path = "/photo";
oxd.Request.protection_access_token = access_token_data.data.access_token;
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

The method uma_rp_get_rpt is called in order to obtain the RPT (Requesting Party Token).

**Required parameters:**

- ticket: Client Access Ticket generated by uma_rs_check_access method
- claim_token: (Optional)
- claim_token_format: (Optional)
- pct: (Optional) Persisted Claims Token
- rpt: (Optional) Requesting Party Token
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by OP
- state: (Optional) state that is returned from uma_rp_get_claims_gathering_url method
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.ticket = "09f32169-de52-42fe-9796-59ba21637a64";
oxd.Request.protection_access_token = access_token_data.data.access_token;
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

```javascript
oxd.Request.ticket = "09f32169-de52-42fe-9796-59ba21637a64";
oxd.Request.claims_redirect_uri = "https://node.oxdexample.com:5053/settings";
oxd.Request.protection_access_token = access_token_data.data.access_token;
```

**Response:**

```javascript
{
    "status":"ok",
    "data":{
        "url":"https://<op host>/restv1/uma/gather_claims
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