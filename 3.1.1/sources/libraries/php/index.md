# oxd-php

## Overview
The following documentation demonstrates 
how to use the [oxd Client Software](http://oxd.gluu.org) PHP library to 
send users from a PHP application to an OpenID Connect Provider (OP), 
like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 


## Sample Project

Download the [Sample Project](https://github.com/GluuFederation/oxd-php-library/archive/3.1.1.zip) specific to this oxd-php library.

### System Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows 7 or higher / Windows Server 2008 or higher
- php 5.4 or higher
- Apache 2.4 or higher
- composer
- oxd-php-library 3.1.1

## Prerequisites

### Required Software
To use the oxd-php library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/) or Google.    
- An active installation of the [oxd-server](../../install/index.md) running on the same server as the client application.
- An active installation of the [oxd-https-extension](../../install/index.md) if oxd-https-extension connection is used. In this case, client applications can be on different servers but will be able to access oxd-https-extension.
- A Windows server or Windows installed machine / Linux server or Linux installed machine.

### Configure the Client Application

- Your client application must have a valid SSL certificate, so the URL includes: `https://`

- Enable SSL by	setting the valid certificate and key in your virtual host file:

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
    
- The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP Address. 
You can configure the hostname by adding the following entry in your host file.

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`

- Open the downloaded [Sample Project](https://github.com/GluuFederation/oxd-php-library/archive/3.1.1.zip) and navigate to `client.example.com` directory inside the project.


- With the oxd-server running, navigate to the URL's below to run the sample client application. To register a client in the oxd-server use the Setup Client URL. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, navigate to the Login URL for uthentication.

    - setup_client url: https://client.example.com:5053/Settings.php
    - Login URL: https://client.example.com:5053/Login.php
    - UMA URL: https://client.example.com:5053/Uma.php

- This oxd-php library uses two configuration files (oxdId.json and oxdlibrary/oxd-rp-settings.json) to specify information needed by OpenID Connect Dynamic Client Registration. The configuration file needs to be writable by the client application to save information that is returned (oxd_id,client_id, client_secret, etc.).

## Endpoints

The oxd-server and oxd-https-extension provide the following methods for authenticating users with an OpenID Connect Provider (OP):

 Available OpenID Connect Endpoints 
  - [Setup Client](../../protocol/#setup-client)  
  - [Get Client Token](../../protocol/#get-client-token)
  - [Register Site](../../protocol/#register-site) 
  - [Update Site Registration](../../protocol/#update-site-registration)   
  - [Get Authorization URL](../../protocol/#get-authorization-url)   
  - [Get Tokens by Code](../../protocol/#get-tokens-id-access-by-code)
  - [Get Access Token by Refresh Token](../../protocol/#get-access-token-by-refresh-token)    
  - [Get User Info](../../protocol/#get-user-info)   
  - [Get Logout URI](../../protocol/#log-out-uri)

The oxd-server provides the following methods for performing access management with a UMA Authorization Server (AS):

 Available UMA (User Managed Access) Endpoints 
  - [RS Protect](../../protocol/#uma-rs-protect) 
  - [RS Check Access](../../protocol/#uma-rs-check-access) 
  - [RP Get RPT](../../protocol/#uma-rp-get-rpt) 
  - [RP Get Claims Gathering URL](../../protocol/#uma-rp-get-claims-gathering-url) 


## Sample Code

### OpenID Connect 

#### Setup Client

In order to use an OpenID Connect Provider (OP) for login, 
you need to setup your client application at the OpenID Connect Provider (OP). 
During setup, oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful setup, the oxd-server will assign a unique 
oxd ID, Client ID and Client Secret. This Client ID and Client Secret can be used for `get_client_token` method. If your OpenID Connect Provider (OP) does not support dynamic registration (like Google), you will need to obtain a 
ClientID and Client Secret which can be passed to the `setup_client` method as a 
parameter. The Setup Client method is a one time task to configure a client in the 
oxd-server and OpenID Connect Provider (OP).


 

**Required parameters:**

- op_host: URL of the OpenID Connect Provider (OP)
- authorization_redirect_uri: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- connection_type: 'local' for oxd-server and 'web' for oxd-https-extension
- connection_type_value: 'oxd port number' for oxd-server type and ' oxd-https-extension url' for  oxd-https-extension type
- client_name: Client application name
- post_logout_redirect_uri: URL to which the user is redirected to after successful logout
- clientId: Client ID from OpenID Connect Provider (OP).  Should be passed with the Client Secret
- clientSecret: Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.

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

#### Get Client Token

The `get_client_token` method is used to get a token which is sent as an input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Required parameters:**

- op_host: OpenID Connect Provider (OP)
- client_id: Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.

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
you need to register your client application at the OpenID Connect Provider (OP). 
During registration oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful registration, a unique 
identifier will be issued by the oxd-server. If your OpenID Connect Provider (OP)
does not support dynamic registration (like Google), you will need to obtain a 
ClientID and Client Secret which can be passed to the `register_site` method as a 
parameter. The Register Site method is a one time task to configure a client in the 
oxd-server and OpenID Connect Provider (OP).

!!!Note: 
    The `register_site` endpoint is not required if the client is registered using `setup_client`

**Required parameters:**
- op_host: (required) OpenID Connect Provider (OP)
- authorization_redirect_uri: (required) URI to redirect after successful login
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

#### Update Site Registration

The `update_site_registration` method can be used to update an existing client in the OpenID Connect Provider (OP). 
Fields like Authorization Redirect URL, Post Logout URL, Scope, Client Secret, etc. can be updated using this method.

**Required parameters:**
- oxd_id: (required) oxd ID from client registration
- client_name: (Optional) Client application name
- contacts: (Optional) User's e-mail ID
- authorization_redirect_uri:  (Optional) URL to which the OpenID Connect Provider (OP) is authorized to redirect the user after authorization.
- post_logout_redirect_uri: (Optional) URL to which the RP is requesting that the 
End-User's User Agent be redirected to after a logout has been performed.
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

#### Get Authorization URL

The `get_authorization_url` method returns the OpenID Connect Provider (OP) Authentication URL to which the client application must redirect the user to 
authorize the release of personal data. The Response URL includes state value, 
which can be used to obtain tokens required for authentication. This state value is used 
to maintain the state between the request and the callback.

**Required parameters:**
- oxd_id: (required) oxd ID from client registration
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by OpenID Connect Provider (OP).
- acr_values: (Optional) Custom authentication script from the Gluu server which is required for extened authenication  
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

#### Get Tokens by Code

Upon successful login, the login result will return code and state. `get_tokens_by_code` 
uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**
- oxd_id: (required) oxd ID from client registration
- code: The code from OpenID Connect Provider (OP) Authorization Redirect URL
- state: The state from OpenID Connect Provider (OP) Authorization Redirect URL
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
#### Get Access Token by Refresh Token

The `get_access_token_by_refresh_token` method is used to get a new access token and a new refresh token by using the refresh token which is obtained from `get_tokens_by_code` method.

**Required parameters:**
- oxd_id: (required) oxd ID from client registration
- refreshToken: Obtained from the get_tokens_by_code method
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by OpenID Connect Provider (OP).
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

#### Get User Info

Once the user has been authenticated by the OpenID Connect Provider (OP), 
the `get_user_info` method returns the claims (First Name, Last Name, E-Mail ID, etc.) about the authenticated end user.

**Required parameters:**
- oxd_id: (required) oxd ID from client registration
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

`get_logout_uri` method returns the OpenID Connect Provider (OP) Logout URL. Client application  uses this Logout URL to end the user session.

**Required parameters:**
- oxd_id: (required) oxd ID from client registration
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

### UMA (User Managed Access)

#### RS Protect

`uma_rs_protect` method is used for protecting resource by the Resource Server. The Resource Server is needed to construct the command which will protect the resource.
The command will contain an API path, HTTP methods (POST,GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy mapped, uma_rs_check_access method will always return access as granted. To know more about uma_rpt_policies you can reference this [Document](https://gluu.org/docs/oxd/3.1.1/api/#uma-2-client-apis).

**Required parameters:**
- oxd_id: (required) oxd ID from client registration
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

### RS Check Access 

`uma_rs_check_access` method used in a UMA Resource Server to check the access to the resource.

**Required parameters:**
- oxd_id: (required) oxd ID from client registration
- rpt: Requesting Party Token
- path: Path of the resource to be checked 
- http_method: HTTP methods (POST, GET and PUT)
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

***Access Granted Response***

```javascript
{
    "status":"ok",
    "data":{
        "access":"granted"
    }
}
```

***Access Denied with Ticket Response***

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

***Access Denied without Ticket Response***

```javascript
{
    "status":"ok",
    "data":{
        "access":"denied"
    }
}
```

***Resource is not Protected***

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

**Required parameters:**

- ticket: Client Access Ticket generated by uma_rs_check_access method
- claim_token: (Optional)
- claim_token_format: (Optional)
- pct: (Optional) Persisted Claims Token
- rpt: (Optional) Requesting Party Token
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by OOpenID Connect Provider (OP).
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

***Invalid Ticket error Response***

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
Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.