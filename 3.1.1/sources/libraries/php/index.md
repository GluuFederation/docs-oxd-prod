# oxd-php-library

## Overview
The following documentation demonstrates 
how to use the [oxd client software](http://oxd.gluu.org) PHP library to 
send users from a PHP application to an OpenID Connect Provider (OP), 
like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 


## Sample Project

[Download a sample project](https://github.com/GluuFederation/oxd-php-library/archive/3.1.1.zip) specific to this oxd library.

### System Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows 7 or higher / Windows Server 2008 or higher
- php 5.4 or higher
- Apache 2.4 or higher
- composer
- oxd-php-library 3.1.1

## Prerequisites

### Required Software
To use the oxd PHP library, you will need:

- A valid OpenID Connect Provider (OP), like Google or the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/).    
- An active installation of the [oxd server](../../install/index.md) running in the same server as the client application.
- An active installation of the [oxd https extension](../../install/index.md) if client applications are on different server than oxd server.
- A Windows server or Windows installed machine / Linux server or Linux installed machine.

### Configure the Client Application

- Your client application must have a valid ssl cert, so the url includes: `https://`

- Enable SSL by	setting the valid certificate and key in your virtual host file file:

```apache
<VirtualHost *:443>
    ServerAdmin postmaster@dummy-host.localhost
	DocumentRoot "<path to client.example.com folder>"
    ServerName client.example.com
    ServerAlias www.client.example.com
	ServerAlias gluuwordpress.com
    SSLEngine on
    SSLCertificateFile "<certificate file name>.crt"	
    SSLCertificateKeyFile "<key file name>.key"
		<Directory "<path to client.example.com folder>" >
            AllowOverride All
            Order allow,deny
            Allow from all
        </Directory> 
</VirtualHost>
```
    
- The client host name should be a valid `hostname`(FQDN), not localhost or an IP Address. 
You can configure the host name by adding the following entry in your host file.

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`

- Open the downloaded [sample project](https://github.com/GluuFederation/oxd-php-library/archive/3.1.1.zip) and navigate to `client.example.com` directory inside the project.


- That's it!. Now navigate to following url to run Sample client pplication. Make sure oxd server is running. Setup client url can be used for egistering Client in oxd server. Upon successful registration of the client application, oxd Id will be displayed in the UI. Then navigate to Login URL for uthentication.

    - setup_client url: https://client.example.com:5053/Settings.php
    - Login URL: https://client.example.com:5053/Login.php
    - UMA URL: https://client.example.com:5053/Uma.php

- This oxd PHP library uses 2 configuration file (oxdId.json and oxdlibrary/oxd-rp-settings.json) to specify information needed by OpenID Connect dynamic client registration, and to save information that is returned, like the oxd_id,client_id,client_secret etc. So the config file needs to be writable by the Client application.

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

```php
if($oxdRpConfig->conn_type == "local"){
$register_site = new Setup_client();
}
else if($oxdRpConfig->conn_type == "web"){
$register_site = new Setup_client($config);
}
$register_site->setRequestOpHost(Oxd_RP_config::$op_host);
$register_site->setRequestAcrValues(Oxd_RP_config::$acr_values);
$register_site->setRequestAuthorizationRedirectUri(Oxd_RP_config::$authorization_redirect_uri);
$register_site->setRequestPostLogoutRedirectUri(Oxd_RP_config::$post_logout_redirect_uri);
$register_site->setRequestGrantTypes(Oxd_RP_config::$grant_types);
$register_site->setRequestResponseTypes(Oxd_RP_config::$response_types);
$register_site->setRequestScope(Oxd_RP_config::$scope);
$register_site->setRequestClientName($_POST['client_name']);
$register_site->request();
```

**Response:**

```php
$oxdObject->oxd_id = $register_site->getResponseOxdId();
$oxdObject->oxd_client_id = $register_site->getResponse_client_id();
$oxdObject->oxd_client_secret = $register_site->getResponse_client_secret();
```

### Get Client Token

The `get_client_token` method is used to get a token which is sent as input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Required parameters:**

- op_host: OpenId Connect Provider
- client_id: Client Id from OpenID Connect Provider (Should be passed with Client Secret)
- client_secret: Client Secret from OpenID Connect Provider (Should be passed with Client Id)

**Request:**

```php
if ($oxdRpConfig->conn_type == "local") {
    $getClientAccessToken = new Get_client_access_token();
} else if ($oxdRpConfig->conn_type == "web") {   
    $getClientAccessToken = new Get_client_access_token($config);
}
$getClientAccessToken->setRequestOpHost(Oxd_RP_config::$op_host);
$getClientAccessToken->setRequestAcrValues(Oxd_RP_config::$acr_values);
$getClientAccessToken->setRequestAuthorizationRedirectUri(Oxd_RP_config::$authorization_redirect_uri);
$getClientAccessToken->setRequestPostLogoutRedirectUri(Oxd_RP_config::$post_logout_redirect_uri);
$getClientAccessToken->setRequestGrantTypes(Oxd_RP_config::$grant_types);
$getClientAccessToken->setRequestResponseTypes(Oxd_RP_config::$response_types);
$getClientAccessToken->setRequestScope(Oxd_RP_config::$scope);
$getClientAccessToken->setRequest_oxd_id(getOxdId());
$getClientAccessToken->setRequest_client_id(getOxdClientId());
$getClientAccessToken->setRequest_client_secret(getOxdClientSecret());
$getClientAccessToken->request();
```

**Response:**

```php
$getClientAccessToken->getResponse_access_token();
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

```php
if($oxdRpConfig->conn_type == "local"){
    $register_site = new Register_site();
}
else if($oxdRpConfig->conn_type == "web"){
    $register_site = new Register_site($config);
}
$register_site->setRequestOpHost(Oxd_RP_config::$op_host);
$register_site->setRequestAcrValues(Oxd_RP_config::$acr_values);
$register_site->setRequestAuthorizationRedirectUri(Oxd_RP_config::$authorization_redirect_uri);
$register_site->setRequestPostLogoutRedirectUri(Oxd_RP_config::$post_logout_redirect_uri);
$register_site->setRequestGrantTypes(Oxd_RP_config::$grant_types);
$register_site->setRequestResponseTypes(Oxd_RP_config::$response_types);
$register_site->setRequestScope(Oxd_RP_config::$scope);
$register_site->setRequestClientName($_POST['client_name']);
$register_site->request();
```

**Response:**

```javascript
$oxdObject->oxd_id = $register_site->getResponseOxdId();
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

```php
if($oxdRpConfig->conn_type == "local"){
    $update_site_registration = new Update_site_registration();
}
else if($oxdRpConfig->conn_type == "web"){
    $update_site_registration = new Update_site_registration($config);
}
$update_site_registration->setRequestAcrValues(Oxd_RP_config::$acr_values);
$update_site_registration->setRequestOxdId($oxdId);
$update_site_registration->setRequestAuthorizationRedirectUri(Oxd_RP_config::$authorization_redirect_uri);
$update_site_registration->setRequestPostLogoutRedirectUri(Oxd_RP_config::$post_logout_redirect_uri);
$update_site_registration->setRequestGrantTypes(Oxd_RP_config::$grant_types);
$update_site_registration->setRequestResponseTypes(Oxd_RP_config::$response_types);
$update_site_registration->setRequestScope(Oxd_RP_config::$scope);
$update_site_registration->setRequest_protection_access_token(getClientProtectionAccessToken());
$update_site_registration->request($update_site_registration->getUrl());
```
**Response:**

```php
$update_site_registration->getResponseObject();
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

```php
if($oxdRpConfig->conn_type == "local"){
    $get_authorization_url = new Get_authorization_url();
}
else if($oxdRpConfig->conn_type == "web"){
    $get_authorization_url = new Get_authorization_url($config);
}
$get_authorization_url->setRequestOxdId($oxdId);
$get_authorization_url->setRequestScope(Oxd_RP_config::$scope);
$get_authorization_url->setRequestAcrValues(Oxd_RP_config::$acr_values);
$get_authorization_url->addCustom_parameters("param1", "value1");
$get_authorization_url->addCustom_parameters("param2", "value2");
$get_authorization_url->setRequest_protection_access_token(getClientProtectionAccessToken());
$get_authorization_url->request();

```

**Response:**

```php
$get_authorization_url->getResponseAuthorizationUrl();
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

```php
if ($oxdRpConfig->conn_type == "local") {
    $get_tokens_by_code = new Get_tokens_by_code();
} else if ($oxdRpConfig->conn_type == "web") {
    $get_tokens_by_code = new Get_tokens_by_code($config);
}
$get_tokens_by_code->setRequestOxdId($oxdId);
$get_tokens_by_code->setRequestCode($_GET['code']);
$get_tokens_by_code->setRequestState($_GET['state']);
$get_tokens_by_code->setRequest_protection_access_token(getClientProtectionAccessToken());
$get_tokens_by_code->request();
```

**Response:**

```php
$data['accessToken'] = $get_tokens_by_code->getResponseAccessToken();
$data['refreshToken'] = $get_tokens_by_code->getResponseRefreshToken();
$data['idToken'] = $get_tokens_by_code->getResponseIdToken();
$data['idTokenClaims'] = $get_tokens_by_code->getResponseIdTokenClaims();
```
### Get Access Token by Refresh Token

The `get_access_token_by_refresh_token` method is used to get a new access token and a new refresh token by using the refresh token which is obtained from `get_tokens_by_code` method.

**Required parameters:**
- oxd_id: (required) oxd Id from client registration
- refreshToken: Obtained from the get_tokens_by_code method
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by OP
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```php
$getAccessTokenFromRefreshToken = new Get_access_token_by_refresh_token($config);
$getAccessTokenFromRefreshToken->setRequestOxdId($oxdOBJECT->oxd_id);
$getAccessTokenFromRefreshToken->setRequestRefreshToken($refreshToken);
if($oxdOBJECT->has_registration_endpoint){
    $getAccessTokenFromRefreshToken->setRequest_protection_access_token(getClientProtectionAccessToken($config));
}
$getAccessTokenFromRefreshToken->request();
```

**Response:**

```php
$getAccessTokenFromRefreshToken->getResponseAccessToken();
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

```php
if ($oxdRpConfig->conn_type == "local") {
    $get_user_info = new Get_user_info();
}else{
    $get_user_info = new Get_user_info($config);
}
$get_user_info->setRequestOxdId($oxdId);
$get_user_info->setRequestAccessToken($accessToken);
$get_user_info->setRequest_protection_access_token(getClientProtectionAccessToken());
$get_user_info->request();
```

**Response:**

```php
$data = $get_user_info->getResponseClaims();
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

```php
if($oxdRpConfig->conn_type == "local"){
    $get_logout_uri = new Logout();
}else if($oxdRpConfig->conn_type == "web"){
    $get_logout_uri = new Logout($config);
}
$get_logout_uri->setRequestOxdId($oxdId);
$get_logout_uri->setRequest_protection_access_token(getClientProtectionAccessToken());
$get_logout_uri->request();
```

**Response:**

```php
$data["logoutUri"] = $get_logout_uri->getResponseObject()->data->uri;
```
### UMA RS Protect

`uma_rs_protect` method is used for protecting resource by the Resource Server. Resource server need to construct the command which will protect the resource.
The command will contain api path, http methods (POST,GET, PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy mapped, uma_rs_check_access method will always return access as granted. To know more about uma_rpt_policies you can check this [document]().

**Required parameters:**
- oxd_id: (required) oxd Id from client registration
- resources: One or more protected resources that a resource server manages as a set, abstractly. In authorization policy terminology, a resource set is the "object" being protected. 
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```php
if($oxdRpConfig->conn_type == "local"){
    $uma_rs_protect = new Uma_rs_protect();
}
else if($oxdRpConfig->conn_type == "web"){
    $uma_rs_protect = new Uma_rs_protect($config);
}
$uma_rs_protect->setRequestOxdId($oxdObject->oxd_id);
$uma_rs_protect->addConditionForPath(["GET"], ["https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1"], ["https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1"]);
$uma_rs_protect->addResource("/photo");
$uma_rs_protect->setRequest_protection_access_token(getClientProtectionAccessToken());
$uma_rs_protect->request();
```

**Response:**

```php
$uma_rs_protect->getResponseJSON();
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

```php
if($oxdRpConfig->conn_type == "local"){
    $uma_rs_check_access = new Uma_rs_check_access();
}
else if($oxdRpConfig->conn_type == "web"){
    $uma_rs_check_access = new Uma_rs_check_access($config);
}
$uma_rs_check_access->setRequestOxdId($oxdObject->oxd_id);
$uma_rs_check_access->setRequestRpt("7f127550-211b-4933-b5c1-21934a478021_11E0.A770.B55E.A7F9.27F5.B594.A4AE.A7DE");
$uma_rs_check_access->setRequestPath("/photo");
$uma_rs_check_access->setRequestHttpMethod('GET');
$uma_rs_check_access->setRequest_protection_access_token(getClientProtectionAccessToken());
$uma_rs_check_access->request();
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
if($oxdRpConfig->conn_type == "local"){
    $uma_rp_get_rpt = new Uma_rp_get_rpt();
}
else if($oxdRpConfig->conn_type == "web"){
    $uma_rp_get_rpt = new Uma_rp_get_rpt($config);
}
$uma_rp_get_rpt->setRequest_oxd_id($oxdObject->oxd_id);
$uma_rp_get_rpt->setRequest_ticket("8d736e57-e407-4ec2-ab8b-92df70d90edb");
$uma_rp_get_rpt->setRequest_protection_access_token(getClientProtectionAccessToken());
$uma_rp_get_rpt->request();
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
if($oxdRpConfig->conn_type == "local"){
    $uma_rp_get_claims_gathering_url = new Uma_rp_get_claims_gathering_url();
}
else if($oxdRpConfig->conn_type == "web"){
    $uma_rp_get_claims_gathering_url = new Uma_rp_get_claims_gathering_url($config);
}
$uma_rp_get_claims_gathering_url->setRequest_oxd_id($oxdObject->oxd_id);
$uma_rp_get_claims_gathering_url->setRequest_ticket("eeb13b4d-fd18-43a4-916e-c6b61ea4249b");
$uma_rp_get_claims_gathering_url->setRequest_claims_redirect_uri("https://client.example.com/");
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