# oxd-node

## Overview
The following documentation demonstrates 
how to use the [oxd client software](http://oxd.gluu.org) Node.js library to send users from a Node.js application to an 
OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login.

## Sample Project

[Download a sample project](https://github.com/GluuFederation/oxd-node/archive/3.0.1.zip) specific to this oxd library.

### System Requirements


- Ubuntu / Debian / CentOS / RHEL / Windows 7 + / Windows Server 2008 +
- Node.js 6.11.0 or higher
- npm 3.10.10 or higher
- oxd-node library

## Prerequisites
### Required Software
To use the oxd Node.js library, you will need:

- A valid OpenID Connect Provider (OP), like Google or the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/);    
- An active installation of the [oxd server](../../install/index.md) running in the same server as the client application;      
- A Windows server or Windows installed machine / Linux server or Linux installed machine.

### Install oxd-node-library from npm
To install oxd Node.js library from npm, run the following command, inside the current project directory.

```npm
$ npm install oxd-node
```

### Configure the Client Application



- The oxd-node-library configuration file is located at 
'node_modules\oxd-node\model\request_param.js'. This file contains the [parameters](../api-guide/openid-connect-api/) required for client application registration and other oxd methods.


```javascript
{
    exports.scope= [ "openid", "profile", "email" ];
    exports.op_host= "<OpenID Connect Provider Host>";
    exports.authorization_redirect_uri=null;
    exports.post_logout_redirect_uri=null;
    exports.contacts=null;
    exports.application_type= null;
    exports.redirect_uris= null;
    exports.response_types=null;
    exports.client_id=null;
    exports.client_secret=null;
    exports.client_jwks_uri=null;
    exports.client_token_endpoint_auth_method=null;
    exports.client_request_uris=null;
    exports.client_logout_uris=[""];
    exports.client_sector_identifier_uri=null;
    exports.ui_locales=null;
    exports.claims_locales=null;
    exports.acr_values=null;
    exports.grant_types=null;
    exports.oxd_id=null;
    exports.code=null;
    exports.state=null;
    exports.access_token=null;
    exports.id_token_hint=null;
    exports.session_state=null;
    exports.port=8099;
}
                        
```

- Your client application must have a valid ssl cert, so the url includes: `https://`    

- The client hostname should be a valid hostname, not localhost or an IP Address. You can configure the hostname by adding the following entry in your host file.

    #### Linux
  Host file location `/etc/host` :

    `127.0.0.1  client.example.com` 


    #### Windows
  Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`


- Enable SSL by	adding the following lines on the Node.js application :
```javascript
    ...
    var oxd = require("oxd-node");
    var vhost = require('vhost');
    var properties = require('./properties');
    var https = require('https');
    ...
    ...
    ...
    app.use('/', index);
    var options = {
        key: fs.readFileSync(__dirname + '<Path to your ssl certificate key file>'),
        cert: fs.readFileSync(__dirname + '<Path to your ssl certificate file>')
    };
    app.use(vhost('client.example.com', app)); // Serves top level domain via Main server app

    var a = https.createServer(options, app).listen(properties.app_port);
    module.exports = app;
```
## oxd Server Methods
The oxd server provides the following six methods for authenticating users with an OpenID Connect Provider (OP):

- [Register Site](../protocol/#register-site)    
- [Update Site Registration](../protocol/#update-site-registration)    
- [Get Authorization URL](../protocol/#get-authorization-url)   
- [Get Tokens by Code](../protocol/#get-tokens-id-access-by-code)    
- [Get User Info](../protocol/#get-user-info)   
- [Get Logout URI](../protocol/#log-out-uri)   

## Sample Code

### Register Site

In order to use an OpenID Connect Provider (OP) for login, you need to register your client application at the OP. During registration oxd will dynamically register the OpenID Connect client and save its configuration. Upon successful registration a unique identifier will be issued by the oxd server. If your OpenID Connect Provider does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be passed to the `register_site` method as a parameter. The `register_site` method is a one time task to configure a client in the oxd server and OP.

**Required parameters:**

- op_host: The URL of the OpenID Connect Provider (OP)

- authorization_redirect_uri: A URL which the OP is authorized to redirect the user after authorization.

**Request:**

```javascript
var oxd = require("oxd-node");
oxd.request.op_host = "OP Host url";
oxd.Request.authorization_redirect_uri= "Redirect url of your client application";
oxd.register_site(oxd.Request,function(response){
});
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
The `update_site_registration` method can be used to update an existing client in the OP. Fields like Authorization redirect url, post logout url, scope, client secret etc. can be updated using this method.

**Required parameters:**

- op_host: The URL of the OpenID Connect Provider (OP)

- oxd_id: oxd Id from client registration

- authorization_redirect_uri: A URL which the OP is authorized to redirect the user after authorization.

**Request:**

```javascript
var oxd = require("oxd-node");
oxd.request.op_host = "OP Host url";
oxd.Request.oxd_id = "Client oxd-id";
oxd.Request.authorization_redirect_uri=  "Redirect url of your client application";
oxd.update_site_registration(oxd.Request,function(response){
});
```

**Response:**

```javascript
{
    "status":"ok"
}
```

### Get Authorization URL
The `get_authorization_url` method returns the OpenID Connect Provider authentication URL to which the client application must redirect the user to authorize the release of personal data. The response URL includes state value, which can be used to obtain tokens required for authentication. This state value used to maintain state between the request and the callback.

**Required parameters:**

- op_host: The URL of the OpenID Connect Provider (OP)


- oxd_id: oxd Id from client registration

**Request:**

```javascript
var oxd = require("oxd-node");
oxd.request.op_host = "OP Host url";
oxd.Request.oxd_id = "Client oxd-id";
oxd.Request.acr_values = ["basic"]; //optional, may be skipped (default: basic) 
oxd.get_authorization_url(oxd.Request,function(response){
});
```

**Response:**
```javascript
{
    "status":"ok",
    "data":{
        "authorization_url":"https://client.example.com/authorize?response_type=code&client_id=s6BhdRkqt3&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb&scope=openid%20profile&acr_values=duo&state=af0ifjsldkj&nonce=n-0S6_WzA2Mj"
    }
}
```

### Get Tokens by Code
Upon successful login, the login result will return code and state. `get_tokens_by_code` uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**

- op_host: The URL of the OpenID Connect Provider (OP)

- oxd_id: oxd Id from client registration

- code: The Code from OP authorization redirect url 

- state: The State from OP authorization redirect url

**Request:**

```javascript
var oxd = require("oxd-node");
oxd.request.op_host = "OP Host url";
oxd.Request.oxd_id = "Client oxd-id";
oxd.Request.code = "code from OP redirect url";
oxd.Request.state = "state from OP redirect url";
oxd.get_tokens_by_code(oxd.Request,function(response){
});
```

**Response:**
```javascript
{
    "status":"ok",
    "data":{
        "access_token":"SlAV32hkKG",
        "expires_in":3600,
        "refresh_token":"aaAV32hkKG1"
        "id_token":"eyJ0 ... NiJ9.eyJ1c ... I6IjIifX0.DeWt4Qu ... ZXso",
        "id_token_claims": {
             "iss": "https://client.example.com",
             "sub": "24400320",
             "aud": "s6BhdRkqt3",
             "nonce": "n-0S6_WzA2Mj",
             "exp": 1311281970,
             "iat": 1311280970,
             "at_hash": "MTIzNDU2Nzg5MDEyMzQ1Ng"
        }
    }
}

```

### Get User Info

Once the user has been authenticated by the OpenID Connect Provider, the `get_user_info` method returns Claims (Like First Name, Last Name, emailId, etc.) about the authenticated end user.

**Required parameters:**

- op_host: The URL of the OpenID Connect Provider (OP)

- oxd_id: oxd Id from client registration

- access_token: accessToken from GetTokenByCode


**Request:**

```javascript
var oxd = require("oxd-node");
oxd.request.op_host = "OP Host url";
oxd.Request.oxd_id = "Client oxd-id";
oxd.Request.access_token = "access_token from GetTokenByCode";
oxd.get_user_info(oxd.Request,function(response){
});
```

**Response:**
```javascript
{
    "status":"ok",
    "data":{
        "claims":{
            "sub": ["248289761001"],
            "name": ["Jane Doe"],
            "given_name": ["Jane"],
            "family_name": ["Doe"],
            "preferred_username": ["j.doe"],
            "email": ["janedoe@example.com"],
            "picture": ["http://example.com/janedoe/me.jpg"]
        }
    }
}
```

### Logout

`get_logout_uri` method returns the OpenID Connect Provider logout url. Client application  uses this logout url to end the user session.


**Required parameters:**

- op_host: The URL of the OpenID Connect Provider (OP)

- oxd_id: oxd Id from client registration

**Request:**

```javascript
var oxd = require("oxd-node");

oxd.request.op_host = "OP Host url";
oxd.Request.oxd_id = "Client oxd-id";
oxd.get_logout_uri(oxd.Request,function(response){
});
```

**Response:**
```javascript
{
    "status":"ok",
    "data":{
        "uri":"https://<server>/end_session?id_token_hint=<id token>&state=<state>&post_logout_redirect_uri=<...>"
    }
}
```

## Support
Please report technical issues and suspected bugs on our [support page](https://support.gluu.org/). You can use the same credentials you created to register for your oxd license to sign in on Gluu support.