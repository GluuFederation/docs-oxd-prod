# PHP

## Installation

### Prerequisites

- PHP 5.6 or higher
- Apache server 2.4 or higher
- Gluu oxd server - [Installation docs](https://gluu.org/docs/oxd/install/)

### Library

- *Composer* - This is the preferred method. See the [composer](https://getcomposer.org) website for [installation instructions](https://getcomposer.org/doc/00-intro.md) if 
you do not already have it installed. To install oxd-php-api via Composer, execute the following command 
in your project root:

Composer requires "gluufederation/oxd-php-api": "3.1.3"

- *Source from Github* -   [Download](https://github.com/GluuFederation/oxd-php-library/archive/v3.1.3.zip) the zip of the oxd PHP library.

#### Important Links

- [oxd docs](https://gluu.org/docs/oxd)
- oxd-php-library [API docs](https://rawgit.com/GluuFederation/oxd-php-library/3.1.3/docs/html/index.html) for the auto-generated php docs, which includes more in-depth information about the various functions and parameters.
- See the code of a [sample php app](https://github.com/GluuFederation/oxd-php-library/tree/3.1.3/client.example.com) built using oxd-php-library.
- Browse the oxd-php-library [source code on Github](https://github.com/GluuFederation/oxd-php-library).

## Configuration 

The oxd-php-library configuration file is located in 
'oxd-rp-settings.json'. The values here are used during 
registration. For a full list of supported
oxd configuration parameters, see the 
[oxd documentation](https://gluu.org/docs/oxd/protocol/)

!!! Note: 
    The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP address.

**oxd-server Configuration**

Below is a typical configuration data set for registration:

``` {.code }
{
   "op_host":"<GLUU Server url>",
   "oxd_host":"<OXD server host IP>",
   "oxd_host_port":8099,
   "authorization_redirect_uri":"[https://client.example.com/welcome]",
   "post_logout_redirect_uri":"[https://client.example.com/welcome]",
   "scope":[
      "openid",
      "profile",
      "uma_protection",
      "uma_authorization"
   ],
   "application_type":"web",
   "response_types":[
      "code"
   ],
   "grant_types":[
      "authorization_code"
   ],
   "acr_values":[
      ""
   ]
}
                        
```


**oxd-https-extension Configuration**

The oxd-https-extension configuration file is located in 
'oxdHttpConfig.php'. The values here are used during 
the usage of all oxd protocols.For a full list of supported protocols, see the [oxd protocol](https://gluu.org/docs/oxd/protocol/) documentation.

By passing this configuration into any oxd php library class constructor, we can enable oxd-https-extension to connect oxd through HTTPS.

``` {.code }
return [
    'host' => '<OXD-TO-HTTP Host>',
    'get_authorization_url' => "get-authorization-url",
    'update_site_registration' => "update-site",
    'get_tokens_by_code' => "get-tokens-by-code",
    'get_user_info' => "get-user-info",
    'register_site' => "register-site",
    'setup_client' => "setup-client",
    'get_logout_uri' => "get-logout-uri",
    'get_client_token' => 'get-client-token',
    'get_access_token_by_refresh_token' => 'get-access-token-by-refresh-token',
    'uma_rs_protect' => 'uma-rs-protect',
    'uma_rs_check_access' => 'uma-rs-check-access',
    'uma_rp_get_rpt' => 'uma-rp-get-rpt',
    'uma_rp_get_claims_gathering_url' => 'uma-rp-get-claims-gathering-url',
    'introspect_access_token' => 'introspect-access-token',
    'introspect_rpt' => 'introspect-rpt',
    'remove_site' => 'remove-site'
];
                        
```


## Sample Code

### Setup Client

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('<path to php oxd library>/oxdHttpConfig.php');
require_once '<path to php oxd library>/Setup_client.php';
require_once '<path to php oxd library>/Oxd_RP_config.php';

if($oxdRpConfig->conn_type == "local"){
    $oxdRpConfig->oxd_host_port = <oxd server port>;
    $setup_client = new Setup_client();
}
else if($oxdRpConfig->conn_type == "web"){
    $oxdRpConfig->oxd_host = <oxd https extension host>;
    $setup_client = new Setup_client($config);
}
$setup_client->setRequestOpHost(Oxd_RP_config::$op_host);
$setup_client->setRequestAcrValues(Oxd_RP_config::$acr_values);
$setup_client->setRequestAuthorizationRedirectUri(Oxd_RP_config::$authorization_redirect_uri);
$setup_client->setRequestPostLogoutRedirectUri(Oxd_RP_config::$post_logout_redirect_uri);
$setup_client->setRequestGrantTypes(Oxd_RP_config::$grant_types);
$setup_client->setRequestResponseTypes(Oxd_RP_config::$response_types);
$setup_client->setRequestScope(Oxd_RP_config::$scope);
$setup_client->setRequestClientName(<client name>);
$setup_client->setRequestClaimsRedirectUri([<claims redirect uris>]);
$setup_client->request();
```
***Response:***

```javascript
{
  "status": "ok",
  "data": {
    "oxd_id": "6F9619FF-8B86-D011-B42D-00CF4FC964FF",
    "op_host": "https://idp.example.com",
    "client_id": "@!E64E.B7E6.3AC4.6CB9!0001!C05E.F402!0008!98F7.EB7B.6213.6527",
    "client_secret": "173d55ff-5a4f-429c-b50d-7899b616912a",
    "client_registration_access_token": "f8975472-240a-4395-b96d-6ef492f50b9e",
    "client_registration_client_uri": "https://idp.example.com/oxauth/restv1/register?client_id=@!E64E.B7E6.3AC4.6CB9!0001!C05E.F402!0008!98F7.EB7B.6213.6527",
    "client_id_issued_at": 1504353408,
    "client_secret_expires_at": 1504439808
  }
}
```


### Get Client Token

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('<path to php oxd library>/oxdHttpConfig.php');
require_once '<path to php oxd library>/Get_client_access_token.php';
require_once '<path to php oxd library>/Oxd_RP_config.php';

if ($oxdRpConfig->conn_type == "local") {
    $getClientAccessToken = new Get_client_access_token();
} else if ($oxdRpConfig->conn_type == "web") {
    $getClientAccessToken = new Get_client_access_token($config);
}
$getClientAccessToken->setRequestOpHost(Oxd_RP_config::$op_host);
$getClientAccessToken->setRequest_scope(Oxd_RP_config::$scope);
$getClientAccessToken->setRequest_client_id(<client id>);
$getClientAccessToken->setRequest_client_secret(<client secret>);
$getClientAccessToken->request();
```

**Response:**

```javascript
{
  "status": "ok",
  "data": {
    "scope": "openid",
    "access_token": "e88b9739-ab60-4170-ac53-ad5dfb2a1d8d",
    "expires_in": 299,
    "refresh_token": null
  }
}
```

### Introspect Access Token

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('<path to php oxd library>/oxdHttpConfig.php');
require_once '<path to php oxd library>/Introspect_access_token.php';
if ($oxdRpConfig->conn_type == "local") {
    $introspectaccesstoken = new Introspect_access_token();
} else if ($oxdRpConfig->conn_type == "web") {
    $introspectaccesstoken = new Introspect_access_token($config);
}
$introspectaccesstoken->setRequest_oxd_id(<oxd id>);
$introspectaccesstoken->setRequest_access_token(<access token>);
$introspectaccesstoken->request();
```

**Response:**

```javascript
{
    "status":"ok",
    "data":{
        "active": true,
        "client_id": "l238j323ds-23ij4",
        "username": "John Black",
        "scopes": ["read", "write"],
        "token_type":"bearer"
        "sub": "jblack",
        "aud": "l238j323ds-23ij4",
        "iss": "https://as.gluu.org/",
        "exp": 1419356238,
        "iat": 1419350238,
        "acr_values": ["basic","duo"],
        "jti": null
    }
}
```

### Register Site

!!! Note: 
    The `Register Site` endpoint is not required if client is registered using `Setup Client`
    
```php
require_once '<path to php oxd library>/Register_site.php';
require_once '<path to php oxd library>/Oxd_RP_config.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
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
$segment = explode('/',$_SERVER['REQUEST_URI']);
array_pop($segment);
$segment = implode("/",$segment);
$register_site->setRequestClaimsRedirectUri([<claims redirect uris>]);
$register_site->request();
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


### Update Site

```php
require_once '<path to php oxd library>/Update_site.php';
require_once '<path to php oxd library>/Oxd_RP_config.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
    $update_site = new Update_site();
}
else if($oxdRpConfig->conn_type == "web"){
    $update_site = new Update_site($config);
}
$update_site->setRequestAcrValues(Oxd_RP_config::$acr_values);
$update_site->setRequestOxdId(<oxd id>);
$update_site->setRequestAuthorizationRedirectUri(Oxd_RP_config::$authorization_redirect_uri);
$update_site->setRequestPostLogoutRedirectUri(Oxd_RP_config::$post_logout_redirect_uri);
$update_site->setRequestGrantTypes(Oxd_RP_config::$grant_types);
$update_site->setRequestResponseTypes(Oxd_RP_config::$response_types);
$update_site->setRequestScope(Oxd_RP_config::$scope);
$update_site->setRequest_protection_access_token(<protection access token>);
$update_site->request();
```

**Response:**

```javascript
{
    "status":"ok"
}
```


### Remove Site

```php
require_once '<path to php oxd library>/Remove_site.php';
require_once '<path to php oxd library>/Oxd_RP_config.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
//	    This is for OXD Socket
$remove_site = new Remove_site();
}
else if($oxdRpConfig->conn_type == "web"){
//	    This is for OXD Web
$remove_site = new Remove_site($config);
}
$remove_site->setRequestOxdId(<oxd id>);
$remove_site->setRequest_protection_access_token(getClientProtectionAccessToken());
$remove_site->request();
```

***Response:***

```javascript
{
    "status":"ok",
    "data": {
        "oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6"
    }
}
```


### Get Authorization URL

```php
require_once '<path to php oxd library>/Get_authorization_url.php';
require_once '<path to php oxd library>/Oxd_RP_config.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
    $get_authorization_url = new Get_authorization_url();
}
else if($oxdRpConfig->conn_type == "web"){
    $get_authorization_url = new Get_authorization_url($config);
}
$get_authorization_url->setRequestOxdId(<oxd id>);
$get_authorization_url->setRequestScope(Oxd_RP_config::$scope);
$get_authorization_url->setRequestAcrValues(Oxd_RP_config::$acr_values);
$get_authorization_url->addCustom_parameters("param1", "value1");
$get_authorization_url->addCustom_parameters("param2", "value2");
$get_authorization_url->setRequest_protection_access_token(<protection access token>);
$get_authorization_url->request();
```

**Response:**

```javascript
{
    "status":"ok",
    "data":{
        "authorization_url":"https://client.example.com/authorize?response_type=code&client_id=s6BhdRkqt3&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb&scope=openid%20profile&acr_values=duo&state=af0ifjsldkj&nonce=n-0S6_WzA2Mj&param2=value2&param1=value1"
    }
}
```


### Get Tokens by Code

```php
require_once '<path to php oxd library>/Get_tokens_by_code.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if ($oxdRpConfig->conn_type == "local") {
    $get_tokens_by_code = new Get_tokens_by_code();
} else if ($oxdRpConfig->conn_type == "web") {
    $get_tokens_by_code = new Get_tokens_by_code($config);
}
$get_tokens_by_code->setRequestOxdId(<oxd id>);
$get_tokens_by_code->setRequestCode($_GET['code']);
$get_tokens_by_code->setRequestState($_GET['state']);
$get_tokens_by_code->setRequest_protection_access_token(<protection access token>);
$get_tokens_by_code->request();
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


### Get Access Token by Refresh Token

```php
require_once '<path to php oxd library>/Get_access_token_by_refresh_token.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if ($oxdRpConfig->conn_type == "local") {
    $getAccessTokenFromRefreshToken = new Get_access_token_by_refresh_token();
} else if ($oxdRpConfig->conn_type == "web") {
    $getAccessTokenFromRefreshToken = new Get_access_token_by_refresh_token($config);

$getAccessTokenFromRefreshToken->setRequestOxdId(<oxd id>);
$getAccessTokenFromRefreshToken->setRequestRefreshToken(<refresh token from get tokens by code>);
if($oxdOBJECT->has_registration_endpoint){
    $getAccessTokenFromRefreshToken->setRequest_protection_access_token(<protection access token>);
}
$getAccessTokenFromRefreshToken->request();
```

**Response:**

```javascript
{
  "status": "ok",
  "data": {
    "scope": "openid",
    "access_token": "35bedaf4-88e3-4d64-86b9-e59eb0ebde75",
    "expires_in": 299,
    "refresh_token": "f687fb69-aa77-4a1e-a730-55f296ffa074"
  }
}
```


### Get User Info

```php
require_once '<path to php oxd library>/Get_user_info.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if ($oxdRpConfig->conn_type == "local") {
    $get_user_info = new Get_user_info();
}else{
    $get_user_info = new Get_user_info($config);
}
$get_user_info->setRequestOxdId(<oxd id>);
$get_user_info->setRequestAccessToken(<access token from get tokens by code>);
$get_user_info->setRequest_protection_access_token(<protection access token>);
$get_user_info->request();
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


### Get Logout URI

```php
require_once '<path to php oxd library>/Logout.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
    $get_logout_uri = new Logout();
}else if($oxdRpConfig->conn_type == "web"){
    $get_logout_uri = new Logout($config);
}
$get_logout_uri->setRequestOxdId(<oxd id>);
$get_logout_uri->setRequest_protection_access_token(<protection access token>);
$get_logout_uri->request();
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


### UMA RS Protect

```php
require_once '<path to php oxd library>/Uma_rs_protect.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
//	    This is for OXD Socket
    $uma_rs_protect = new Uma_rs_protect();
}
else if($oxdRpConfig->conn_type == "web"){
//	    This is for OXD-TO-HTTP
    $uma_rs_protect = new Uma_rs_protect($config);
}
$uma_rs_protect->setRequestOxdId(<oxd id>);

//without scope expression
$uma_rs_protect->addConditionForPath(
                                        ["GET","POST"],
                                        ['https://rsapi.com'], 
                                        ['https://rsapi.com']
                                    );
$uma_rs_protect->addResource(<URI to protect>);
$uma_rs_protect->setRequest_protection_access_token(<protection access token>);
$uma_rs_protect->request();
```

**RS Protect with scope_expression**

```php
require_once '<path to php oxd library>/Uma_rs_protect.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
//	    This is for OXD Socket
    $uma_rs_protect = new Uma_rs_protect();
}
else if($oxdRpConfig->conn_type == "web"){
//	    This is for OXD-TO-HTTP
    $uma_rs_protect = new Uma_rs_protect($config);
}
$uma_rs_protect->setRequestOxdId(<oxd id>);

//with scope expression
$rule = [
    'and' => [
        ['or' => [
            ['var' => 0],
            ['var' => 1]]
        ],
        ['var' => 2]
    ]
];
$data = [
    "https://rsapi.com",
    "https://rsapi2.com",
    "https://rsapi3.com"
];
$uma_rs_protect->addConditionForPath(
                                        ["GET","POST"],
                                        [], 
                                        [],
                                        ["rule"=>$rule,"data"=>$data]
                                    );

$uma_rs_protect->addResource(<URI to protect>);
$uma_rs_protect->setRequest_protection_access_token(<protection access token>);
$uma_rs_protect->request();
```


**Response:**

```javascript
{
    "status":"ok"
}
```


### UMA RS Check Access 

```php
require_once '<path to php oxd library>/Uma_rs_check_access.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
    $umaRsCheckAccess = new Uma_rs_check_access();
}
else if($oxdRpConfig->conn_type == "web"){
    $umaRsCheckAccess = new Uma_rs_check_access($config);
}
$umaRsCheckAccess->setRequestOxdId(<oxd id>);
$umaRsCheckAccess->setRequestRpt(<RPT>);
$umaRsCheckAccess->setRequestPath(<request path>);
$umaRsCheckAccess->setRequestHttpMethod(<request method>);
$umaRsCheckAccess->setRequest_protection_access_token(<protection access token>);
$umaRsCheckAccess->request();
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

### UMA Introspect RPT 

```php
require_once '<path to php oxd library>/Uma_introspect_rpt.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
    $introspectRpt = new Uma_introspect_rpt();
}
else if($oxdRpConfig->conn_type == "web"){
    $introspectRpt = new Uma_introspect_rpt($config);
}
$introspectRpt = new Uma_introspect_rpt($config);
$introspectRpt->setRequest_oxd_id(<oxd id>);
$introspectRpt->setRequest_rpt(<RPT>);
$introspectRpt->request();
```

**Response:**

```javascript
{
    "status":"ok",
    "data":{
        "active":true,
        "exp":1256953732,
        "iat":1256912345,
        "permissions":[  
            {  
                "resource_id":"112210f47de98100",
                "resource_scopes":[  
                    "view",
                    "http://photoz.example.com/dev/actions/print"
                ],
                "exp":1256953732
            }
        ]
    }
}
```

### UMA RP Get RPT 

```php
require_once '<path to php oxd library>/Uma_rp_get_rpt.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
    $uma_rp_get_rpt = new Uma_rp_get_rpt();
}
else if($oxdRpConfig->conn_type == "web"){
    $uma_rp_get_rpt = new Uma_rp_get_rpt($config);
}
$uma_rp_get_rpt->setRequest_oxd_id(<oxd id>);
$uma_rp_get_rpt->setRequest_ticket(<ticket>);
$uma_rp_get_rpt->setRequest_protection_access_token(<protection access token>);
$uma_rp_get_rpt->request();
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


### UMA RP Get Claims Gathering URL 

```php
require_once '<path to php oxd library>/Uma_rp_get_claims_gathering_url.php';
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '<path to php oxd library>/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
    $uma_rp_get_claims_gathering_url = new Uma_rp_get_claims_gathering_url();
}
else if($oxdRpConfig->conn_type == "web"){
    $uma_rp_get_claims_gathering_url = new Uma_rp_get_claims_gathering_url($config);
}
$uma_rp_get_claims_gathering_url->setRequest_oxd_id(<oxd id>);
$uma_rp_get_claims_gathering_url->setRequest_ticket(<ticket>);
$uma_rp_get_claims_gathering_url->setRequest_claims_redirect_uri(<claims redirect uri>);
$uma_rp_get_claims_gathering_url->setRequest_protection_access_token(<protection access token>);
$uma_rp_get_claims_gathering_url->request();
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

## Sample Project

Download a [Sample Project](https://github.com/GluuFederation/oxd-php-library/archive/3.1.3.zip) specific to this oxd-php library.


### Software Requirements

System Requirements:

- Ubuntu / Debian / CentOS / RHEL / Windows Server 2008 or higher
- php 5.4 or higher
- Apache 2.4 or higher
- composer

To use the oxd-php library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google.    
- An active installation of the [oxd-server](../../../install/index.md) running on the same server as the client application.
- If you want to make RESTful (https) calls from your app to your `oxd-server`, you will need an active installation of the [oxd-https-extension](../../../oxd-https/start/index.md)).
- A Windows server or Windows installed machine / Linux server or Linux installed machine.


### Configure the Client Application

- Your client application must have a valid SSL certificate, so the URL includes: `https://`

- Enable SSL by setting the valid certificate and key in your virtual host file:

```code
<VirtualHost *:443>
    ServerAdmin postmaster@dummy-host.localhost
	DocumentRoot "<path to client.example.com folder>"
    ServerName client.example.com
    ServerAlias client.example.com
	
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
    
- The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP address. You can configure the hostname by adding the following entry in the host file:

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`

- Open the downloaded [Sample Project](https://github.com/GluuFederation/oxd-php-library/archive/3.1.3.zip) and navigate to `client.example.com` directory inside the project.


- With the oxd-server running, navigate to the URLs below to run the sample client application. To register a client in the oxd-server use the Setup Client URL. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, navigate to the Login URL for authentication.

    - Setup Client URL: https://client.example.com/Settings.php
    - Login URL: https://client.example.com/Login.php
    - UMA URL: https://client.example.com/Uma.php

- The oxd-php library uses two configuration files (oxdId.json and oxdlibrary/oxd-rp-settings.json) to specify information needed by the OpenID Connect dynamic client registration. In order to save the information that is returned (oxd_id, client_id, client_secret, etc.) the configuration files need to be writable by the client application.

## Support

Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.
