# oxd-node

## Overview
The following documentation demonstrates how to use oxd's Node.js library to 
send users from a Node.js application to an OpenID Connect Provider (OP), 
like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 

Download a [Sample Project](https://github.com/GluuFederation/oxd-node/archive/3.1.1.zip) specific to this oxd-node library.

## Software Requirements

System Requirements:

- Ubuntu / Debian / CentOS / RHEL / Windows Server 2008 or higher
- Node 6.11.0
- npm 3.10.10

To use the oxd-node library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google.    
- An active installation of the [oxd-server](../../../install/index.md) running on the same server as the client application.
- If you want to make RESTful (https) calls from your app to your `oxd-server`, you will need an active installation of the [oxd-https-extension](../../../oxd-https/start/index.md)).
- A Windows server or Windows installed machine / Linux server or Linux installed machine.


## Install oxd-node

To install oxd-node via npm, run following commands in Linux terminal or Windows command window:

``` {.code }
npm install oxd-node@3.1.2
```


## Configure the Client Application

Your client application must have a valid SSL certificate, so the URL includes: `https://`

Enable SSL by setting the valid certificate and key in your application "index.js" file:

```javascript
var options = {
    key: fs.readFileSync(__dirname + '/<key file name>.pem'),
    cert: fs.readFileSync(__dirname + '/<certificate file name>.pem')
};

```
    
The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP Address. 
You can configure the hostname by adding the following entry in your host file:

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`

To install dependencies for this application navigate to the "oxd-node-demo" folder in terminal(linux)/command prompt(windows) and run:

```shell
npm install
```
Open the downloaded [Sample Project](https://github.com/GluuFederation/oxd-node/archive/3.1.1.zip) and navigate to `oxd-node-demo` directory inside the project.

The port number for this application to run can be set in "properties.js" file, which you can use to run this application in any free port. By default the port number is set to "5053" which we will assume as the port number of this application for further parts of this documentation.

Run this project with "Node.js" with the following command from the same folder i.e. "oxd-node-demo":

```shell
node index.js
```

With the oxd-server running, navigate to the URL's below to run the sample client application. To register a client in the oxd-server use the Setup Client URL. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, navigate to the Login URL for authentication.

    - Setup Client URL: https://client.example.com:5053/settings
    - Login URL: https://client.example.com:5053/login
    - UMA URL: https://client.example.com:5053/uma


This oxd Node.js library uses two configuration files (`settings.json` and `parameters.json`) to specify information needed by the OpenID Connect dynamic client registration. To save information that is returned (oxd_id, client_id, client_secret, etc.) the configuration file needs to be writable by the client application.


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



## Sample Code - OpenID Connect

### Setup Client

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
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.authorization_redirect_uri = req.body.redirect_uri;
oxd.Request.op_host = req.body.op_host;
oxd.Request.client_frontchannel_logout_uris = req.body.post_logout_uri;
oxd.Request.post_logout_redirect_uri = req.body.post_logout_uri;
oxd.Request.port = req.body.oxd_local_value;
oxd.Request.scopes = gluu_scopes;
oxd.Request.client_name = req.body.client_name;
if(req.body.conn_type == "web"){
    oxd.Request.url = req.body.oxd_web_value+"/setup-client";
}
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

The `get_client_token` method is used to get a token which is sent as an input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Parameters:**

- client_id: Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- op_host: URL of the OpenID Connect Provider (OP)
- op_discovery_path: (Optional) Path to discovery document. For example if it's https://client.example.com/.well-known/openid-configuration then path is blank. But if it is https://client.example.com/oxauth/.well-known/openid-configuration then path is oxauth
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)


**Request:**

```javascript
oxd.Request.op_host = req.body.op_host;
oxd.Request.client_id = obj.client_id;
oxd.Request.client_secret = obj.client_secret;
oxd.Request.port = req.body.oxd_local_value;
if(req.body.conn_type == "web"){
    var oxd.Request.url = req.body.oxd_web_value+"/get-client-token";
}
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
you need to register your client application at the OpenID Connect Provider (OP). 
During registration oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful registration, a unique 
identifier will be issued by the oxd-server. If your OpenID Connect Provider (OP) does not support dynamic registration (like Google), you will need to obtain a 
ClientID and Client Secret which can be passed to the `register_site` method as a 
parameter. The Register Site method is a one time task to configure a client in the 
oxd-server and OpenID Connect Provider (OP).

!!!Note: 
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
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.authorization_redirect_uri = req.body.redirect_uri;
oxd.Request.op_host = req.body.op_host;
oxd.Request.client_frontchannel_logout_uris = req.body.post_logout_uri;
oxd.Request.post_logout_redirect_uri = req.body.post_logout_uri;
oxd.Request.port = req.body.oxd_local_value;
oxd.Request.scopes = gluu_scopes;
oxd.Request.client_name = req.body.client_name;
if(req.body.conn_type == "web"){
    oxd.Request.url = req.body.oxd_web_value+"/register-site";
}
oxd.register_site(oxd.Request, function(response) {
    console.log(response);
}
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

The `update_site_registration` method can be used to update an existing client in the OpenID Connect Provider (OP). 
Fields like Authorization Redirect URL, Post Logout URL, Scope, Client Secret and other fields can be updated using this method.

**Parameters:**

- oxd_id: oxd ID from client registration
- authorization_redirect_uri:  (Optional) URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
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
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
    oxd.Request.authorization_redirect_uri = req.body.redirect_uri;
    oxd.Request.op_host = req.body.op_host;
    oxd.Request.client_frontchannel_logout_uris = req.body.post_logout_uri;
    oxd.Request.post_logout_redirect_uri = req.body.post_logout_uri;
    oxd.Request.port = req.body.oxd_local_value;
    oxd.Request.oxd_id = obj.oxd_id;
    oxd.Request.client_name = req.body.client_name;
    oxd.Request.protection_access_token = access_token_data.data.access_token;
    if(req.body.conn_type == "web"){
        oxd.Request.url = req.body.oxd_web_value+"/update-site";
    }
    oxd.update_site_registration(oxd.Request, function(response) {
        console.log(response); 
    });
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

The `get_authorization_url` method returns the OpenID Connect Provider (OP) 
Authentication URL to which the client application must redirect the user to 
authorize the release of personal data. The Response URL includes state value, 
which can be used to obtain tokens required for authentication. This state value is used 
to maintain state between the request and the callback.

**Parameters:**

- oxd_id: oxd ID from client registration
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- acr_values: (Optional) Custom authentication script from the Gluu server.  Required for extended authentication. 
- prompt: (Optional) Values that specifies whether the Authorization Server prompts the end user for reauthentication and consent
- custom_params: (Optional) Custom parameters
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.port = obj.port;
oxd.Request.scope=["openid","profile","email","uma_protection","uma_authorization"];
oxd.Request.custom_parameters = {
    param1 : "value1",
    param2 : "value2"
};
oxd.Request.protection_access_token = access_token_data.data.access_token;
if(obj.conn_type == "web"){
    var url = parametersData.httpBaseUrl+"/get-authorization-url";
}
oxd.get_authorization_url(oxd.Request, function(response) {
    console.log(response); 
});
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

Upon successful login, the login result will return code and state. `get_tokens_by_code` method
uses code and state to retrieve token which can be used to access user claims.

**Parameters:**

- oxd_id: oxd ID from client registration
- code: The code from OpenID Connect Provider (OP) Authorization Redirect URL
- state: The state from OpenID Connect Provider (OP) Authorization Redirect URL
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.port = obj.port;
oxd.Request.code = url_parts.query.code;
oxd.Request.state = url_parts.query.state;
oxd.Request.protection_access_token = access_token_data.data.access_token;
if(obj.conn_type == "web"){
    var url = parametersData.httpBaseUrl+"/get-tokens-by-code";
}
oxd.get_tokens_by_code(oxd.Request, function(response) {
    console.log(response);
});
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

**Parameters:**

- oxd_id: oxd ID from client registration
- refreshToken: Obtained from the get_tokens_by_code method
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.port = obj.port;
oxd.Request.refresh_token = response.data.refresh_token;
oxd.Request.scope = oxd.Request.scope=["openid","profile","email","uma_protection","uma_authorization"];
oxd.Request.protection_access_token = access_token_data.data.access_token;
if(obj.conn_type == "web"){
    oxd.Request.url = parametersData.httpBaseUrl+"/get-client-token";
}
oxd.get_access_token_by_refresh_token(oxd.Request, function(access_token){
    console.log(response);
});
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

Once the user has been authenticated by the OpenID Connect Provider (OP), 
the `get_user_info` method returns the claims (First Name, Last Name, E-Mail ID, etc.) about the authenticated end user.

**Parameters:**

- oxd_id: oxd ID from client registration
- access_token: access_token from get_tokens_by_code or get_access_token_by_refresh_token
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.port = obj.port;
oxd.Request.access_token = access_token_data.data.access_token;
oxd.Request.protection_access_token = access_token_data.data.access_token;
if(obj.conn_type == "web"){
    oxd.Request.url = parametersData.httpBaseUrl+"/get-user-info";
}
oxd.get_user_info(oxd.Request, function(response) {
    console.log(response);
});
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

`get_logout_uri` method returns the OpenID Connect Provider (OP) Logout URL. 
Client application uses this Logout URL to end the user session.

**Parameters:**

- oxd_id: oxd ID from client registration
- id_token_hint: (Optional) ID Token previously issued by the Authorization Server being passed as a hint about the end user's current or past authenticated session with the client
- post_logout_redirect_uri: (Optional) URL to which user is redirected to after successful logout
- state: (Optional) Value used to maintain state between the request and the callback
- session_state: (Optional) JSON string that represents the end user’s login state at the OpenID Connect Provider (OP)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.port = obj.port;
oxd.Request.protection_access_token = access_token_data.data.access_token;
if(obj.conn_type == "web"){
    oxd.Request.url = parametersData.httpBaseUrl+"/get-logout-uri";
}
oxd.get_logout_uri(oxd.Request, function(response) {
    console.log(response);
});
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

## Sample Code - UMA

### RS Protect

`uma_rs_protect` method is used for protecting resources by the Resource Server. The Resource Server is needed to construct the command which will protect the resource.
The command will contain an API path, HTTP methods (POST, GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy is mapped, uma_rs_check_access method will always return access as granted. For more information about uma_rpt_policies you can reference this [document](https://gluu.org/docs/oxd/3.1.1/api/#uma-2-client-apis).

**Parameters:**

- oxd_id: oxd ID from client registration
- resources: One or more protected resources that a resource server manages, abstractly, as a set. In authorization policy terminology, a resource set is the "object" being protected. 
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.port = obj.port;
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
if(obj.conn_type == "web"){
    oxd.Request.url = parametersData.httpBaseUrl+"/uma-rs-protect";
}
oxd.uma_rs_protect(oxd.Request, function(response) {
    console.log(response);
});
```

**Response:**

```javascript
result = true
```


### RS Check Access 

`uma_rs_check_access` method is used in the UMA Resource Server to check the access to the resource.

**Parameters:**

- oxd_id: oxd ID from client registration
- rpt: Requesting Party Token
- path: Path of the resource to be checked 
- http_method: HTTP methods (POST, GET and PUT)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.port = obj.port;
oxd.Request.rpt = "";
oxd.Request.http_method = "GET";
oxd.Request.path = "/photo";
oxd.Request.protection_access_token = access_token_data.data.access_token;
if(obj.conn_type == "web"){
    var oxd.Request.url = parametersData.httpBaseUrl+"/uma-rs-check-access";
}
oxd.uma_rs_check_access(oxd.Request, function(response) {
    console.log(response);
});
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


### RP Get RPT 

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
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.port = obj.port;
oxd.Request.ticket = "09f32169-de52-42fe-9796-59ba21637a64";
oxd.Request.protection_access_token = access_token_data.data.access_token;
if(obj.conn_type == "web"){
    oxd.Request.url = parametersData.httpBaseUrl+"/uma-rp-get-rpt";
}
oxd.uma_rp_get_rpt(oxd.Request, function(response) {
    console.log(response);
});
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


### RP Get Claims Gathering URL 

**Parameters:**

- oxd_id: oxd ID from client registration
- ticket: Client Access Ticket generated by uma_rs_check_access method
- claims_redirect_uri: The URI to which the client wishes the authorization server to direct the requesting party’s user agent after completing its interaction
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- url: (Optional) oxd-https-extension URL for oxd-https-extension type (Required if oxd-https-extension is used)

**Request:**

```javascript
oxd.Request.oxd_id = obj.oxd_id;
oxd.Request.port = obj.port;
oxd.Request.ticket = "09f32169-de52-42fe-9796-59ba21637a64";
oxd.Request.claims_redirect_uri = "https://client.example.com:5053/settings";
oxd.Request.protection_access_token = access_token_data.data.access_token;
if (obj.conn_type == "web") {
    oxd.Request.url = parametersData.httpBaseUrl + "/uma-rp-get-claims-gathering-url";
}
oxd.uma_rp_get_claims_gathering_url(oxd.Request, function (response) {
    console.log(response);
});
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
Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.

