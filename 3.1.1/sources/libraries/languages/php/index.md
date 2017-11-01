# oxd-php

## Overview

The following documentation demonstrates 
how to use oxd's PHP library to 
send users from a PHP application to an OpenID Connect Provider (OP), 
like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 


## Sample Project

Download a [Sample Project](https://github.com/GluuFederation/oxd-php-library/archive/3.1.1.zip) specific to this oxd-php library.


### System Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows Server 2008 or higher
- php 5.4 or higher
- Apache 2.4 or higher
- composer
- oxd-php-library 3.1.1


## Prerequisites

### Required Software

To use the oxd-php library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google.    
- An active installation of the [oxd-server](https://gluu.org/docs/oxd/3.1.1/install/
) running on the same server as the client application.
- An active installation of the [oxd-https-extension](https://gluu.org/docs/oxd/3.1.1/install/
) if oxd-https-extension connection is used. In this case, client applications can be on different servers but will be able to access oxd-https-extension.
- A Windows server or Windows installed machine / Linux server or Linux installed machine.


### Configure the Client Application

- Your client application must have a valid SSL certificate, so the URL includes: `https://`

- Enable SSL by setting the valid certificate and key in your virtual host file:

```code
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
    
- The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP address. You can configure the hostname by adding the following entry in the host file:

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`

- Open the downloaded [Sample Project](https://github.com/GluuFederation/oxd-php-library/archive/3.1.1.zip) and navigate to `client.example.com` directory inside the project.


- With the oxd-server running, navigate to the URL's below to run the sample client application. To register a client in the oxd-server use the Setup Client URL. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, navigate to the Login URL for authentication.

    - Setup Client URL: https://client.example.com/Settings.php
    - Login URL: https://client.example.com/Login.php
    - UMA URL: https://client.example.com/Uma.php

- The oxd-php library uses two configuration files (oxdId.json and oxdlibrary/oxd-rp-settings.json) to specify information needed by the OpenID Connect dynamic client registration. In order to save the information that is returned (oxd_id, client_id, client_secret, etc.) the configuration files need to be writable by the client application.


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
- oxd_host_port: (Optional) 'oxd port number' for oxd-server type (Required if oxd-server is used)
- config: (Optional) 'oxd-https-extension config' for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '/oxdlibrary/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
    $oxdRpConfig->oxd_host_port = $_POST['oxd_local_value'];
    $setup_client = new Setup_client();
}
else if($oxdRpConfig->conn_type == "web"){
    $oxdRpConfig->oxd_host = $_POST['oxd_web_value'];
    $setup_client = new Setup_client($config);
}
$setup_client->setRequestOpHost(Oxd_RP_config::$op_host);
$setup_client->setRequestAcrValues(Oxd_RP_config::$acr_values);
$setup_client->setRequestAuthorizationRedirectUri(Oxd_RP_config::$authorization_redirect_uri);
$setup_client->setRequestPostLogoutRedirectUri(Oxd_RP_config::$post_logout_redirect_uri);
$setup_client->setRequestGrantTypes(Oxd_RP_config::$grant_types);
$setup_client->setRequestResponseTypes(Oxd_RP_config::$response_types);
$setup_client->setRequestScope(Oxd_RP_config::$scope);
$setup_client->setRequestClientName($_POST['client_name']);
$setup_client->setRequestClaimsRedirectUri($request_claims_redirect_uris);
$setup_client->setRequestClientJwksUri($request_client_jwks_uri);
$setup_client->setRequestClientTokenEndpointAuthMethod($request_client_token_endpoint_auth_method);
$setup_client->setRequestClientLogoutUris($request_client_logout_uris);
$setup_client->setRequestUiLocales($request_ui_locales);
$setup_client->setRequestClaimsLocales($request_claims_locales);
$setup_client->setRequestAcrValues($request_acr_values);
$setup_client->setRequestGrantTypes($request_grant_types);
$register_site->request();
```

**Response:**

```php
$oxdObject->oxd_id = $register_site->getResponseOxdId();
$oxdObject->oxd_client_id = $register_site->getResponse_client_id();
$oxdObject->oxd_client_secret = $register_site->getResponse_client_secret();
```


#### Get Client Token

The `get_client_token` method is used to get a token which is sent as input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Parameters:**

- client_id: Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- op_host: URL of the OpenID Connect Provider (OP)
- op_discovery_path: (Optional) Path to discovery document. For example if it's https://client.example.com/.well-known/openid-configuration then path is blank . But if it is https://client.example.com/oxauth/.well-known/openid-configuration then path is oxauth
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```php
global $oxdRpConfig;
$config = include('./oxdlibrary/oxdHttpConfig.php');
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
$getClientAccessToken->setRequestScope($request_scope);
$getClientAccessToken->request();
```

**Response:**

```php
$getClientAccessToken->getResponse_access_token();
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
- application_type: (Optional) Kind of the application. The default, if omitted, is web. The defined values are native or web
- response_types: (Optional) Determines the authorization processing flow to be used
- grant_types: (Optional) Grant Types that the Client is declaring that it will restrict itself to using
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- acr_values: (Optional) Required for extended authentication. Custom authentication script from Gluu server.
- client_name: (Optional) Client application name
- client_jwks_uri: (Optional) URL for the Client's JSON Web Key Set (JWKS) document
- client_token_endpoint_auth_method: (Optional) Requested Client Authentication method for the Token Endpoint
- client_request_uris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OpenID Connect Provider (OP)
- client_frontchannel_logout_uris: (Optional) Client application Logout URL
- client_sector_identifier_uri: (Optional) URL using the https scheme to be used in calculating Pseudonymous Identifiers by the OP
- contacts: (Optional) Array of e-mail addresses of people responsible for this Client
- ui_locales: (Optional) End-User's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- claims_locales: (Optional) End-User's preferred languages and scripts for Claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- client_id: (Optional) Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret
- client_secret: (Optional) Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID
- claims_redirect_uri: (Optional)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '/oxdlibrary/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
    $register_site = new Register_site();
}
else if($oxdRpConfig->conn_type == "web"){
    $register_site = new Register_site($config);
}
$register_site->setRequestOpHost(Oxd_RP_config::$op_host);
$register_site->setRequestAuthorizationRedirectUri(Oxd_RP_config::$authorization_redirect_uri);
$register_site->setRequestPostLogoutRedirectUri(Oxd_RP_config::$post_logout_redirect_uri);
$register_site->setRequestResponseTypes(Oxd_RP_config::$response_types);
$register_site->setRequestScope(Oxd_RP_config::$scope);
$register_site->setRequestClientName($_POST['client_name']);
$register_site->setRequestClaimsRedirectUri($request_claims_redirect_uris);
$setup_cregister_sitelient->setRequestClientJwksUri($request_client_jwks_uri);
$register_site->setRequestClientTokenEndpointAuthMethod($request_client_token_endpoint_auth_method);
$register_site->setRequestClientLogoutUris($request_client_logout_uris);
$register_site->setRequestUiLocales($request_ui_locales);
$register_site->setRequestClaimsLocales($request_claims_locales);
$register_site->setRequestAcrValues($request_acr_values);
$register_site->setRequestGrantTypes($request_grant_types);
$register_site->request();
```

**Response:**

```javascript
$oxdObject->oxd_id = $register_site->getResponseOxdId();
```


#### Update Site Registration

The `update_site_registration` method can be used to update an existing client in the OpenID Connect Provider (OP). 
Fields like Authorization Redirect URL, Post Logout URL, Scope, Client Secret and other fields can be updated using this method.

**Parameters:**

- oxd_id: oxd ID from client registration
- authorization_redirect_uri:  (Optional) URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization.
- post_logout_uri: (Optional) URL to which the RP is requesting the end user's user agent be redirected to after a logout has been performed.
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
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '/oxdlibrary/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
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
$update_site_registration->setRequestClientLogoutUris($request_client_logout_uris);
$update_site_registration->setRequestGrantTypes($request_grant_types);
$update_site_registration->setRequestClaimsRedirectUri($request_claims_redirect_uris);
$update_site_registration->setRequestAcrValues($request_acr_values);
$update_site_registration->setRequestClientJwksUri($request_client_jwks_uri);
$update_site_registration->setRequestClientTokenEndpointAuthMethod($request_client_token_endpoint_auth_method);
$update_site_registration->setRequestClientRequestUris($request_client_request_uris);
$update_site_registration->request($update_site_registration->getUrl());
```

**Response:**

```php
$update_site_registration->getResponseObject();
```


#### Get Authorization URL

The `get_authorization_url` method returns the OpenID Connect Provider (OP) 
Authentication URL to which the client application must redirect the user to 
authorize the release of personal data. The Response URL includes state value, 
which can be used to obtain tokens required for authentication. This state value is used 
to maintain state between the request and the callback.

**Parameters:**

- oxd_id: oxd ID from client registration
- scope: (Optional) A scope is an indication by the client that it wants to access some resource.
- acr_values: (Optional) Custom authentication script from the Gluu server.  Required for extended authentication. 
- prompt: (Optional) Values that specifies whether the Authorization Server prompts the end user for reauthentication and consent
- custom_params: (Optional) custom parameters
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '/oxdlibrary/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
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
$get_authorization_url->setRequestAcrValues($request_acr_values);
$get_authorization_url->setRequestPrompt($request_prompt);
$get_authorization_url->request();
```

**Response:**

```php
$get_authorization_url->getResponseAuthorizationUrl();
```


#### Get Tokens by Code

Upon successful login, the login result will return code and state. `get_tokens_by_code` 
uses code and state to retrieve token which can be used to access user claims.

**Parameters:**

- oxd_id: oxd ID from client registration
- code: The code from OpenID Connect Provider (OP) Authorization Redirect URL
- state: The state from OpenID Connect Provider (OP) Authorization Redirect URL
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '/oxdlibrary/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if ($oxdRpConfig->conn_type == "local") {
    $get_tokens_by_code = new Get_tokens_by_code();
} else if ($oxdRpConfig->conn_type == "web") {
    $get_tokens_by_code = new Get_tokens_by_code($config);
}
$get_tokens_by_code->setRequestOxdId($oxdId);
$get_tokens_by_code->setRequestCode($_GET['code']);
$get_tokens_by_code->setRequestState($_GET['state']);
$get_tokens_by_code->setRequest_protection_access_token(getClientProtectionAccessToken());
$get_tokens_by_code->setRequestScopes($request_scopes);
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

**Parameters:**

- oxd_id: oxd ID from client registration
- refreshToken: Obtained from the get_tokens_by_code method
- scope: (Optional) A scope is an indication by the client that it wants to access some resource.
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)


**Request:**

```php
$getAccessTokenFromRefreshToken = new Get_access_token_by_refresh_token($config);
$getAccessTokenFromRefreshToken->setRequestOxdId($oxdOBJECT->oxd_id);
$getAccessTokenFromRefreshToken->setRequestRefreshToken($refreshToken);
$getAccessTokenFromRefreshToken->setRequestScopes($request_scopes);
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

**Parameters:**

- oxd_id: oxd ID from client registration
- access_token: access_token from GetTokenByCode or GetAccessTokenbyRefreshToken
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '/oxdlibrary/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
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


#### Logout

`get_logout_uri` method returns the OpenID Connect Provider (OP) Logout URL. 
Client application  uses this Logout URL to end the user session.

**Parameters:**

- oxd_id: oxd ID from client registration
- id_token_hint: (Optional) ID Token previously issued by the Authorization Server being passed as a hint about the end user's current or past authenticated session with the client
- post_logout_redirect_uri: (Optional) URL to which user is redirected to after successful logout
- state: (Optional) Value used to maintain state between the request and the callback
- session_state: (Optional) JSON string that represents the end user’s login state at the OpenID Connect Provider (OP)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '/oxdlibrary/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
if($oxdRpConfig->conn_type == "local"){
    $get_logout_uri = new Logout();
}else if($oxdRpConfig->conn_type == "web"){
    $get_logout_uri = new Logout($config);
}
$get_logout_uri->setRequestOxdId($oxdId);
$get_logout_uri->setRequestIdToken($request_id_token);
$get_logout_uri->setRequestPostLogoutRedirectUri($request_post_logout_redirect_uri);
$get_logout_uri->setRequestState($request_state);
$get_logout_uri->setRequestSessionState($request_session_state);
$get_logout_uri->setRequest_protection_access_token(getClientProtectionAccessToken());
$get_logout_uri->request();
```

**Response:**

```php
$data["logoutUri"] = $get_logout_uri->getResponseObject()->data->uri;
```


### UMA (User Managed Access)

#### RS Protect

`uma_rs_protect` method is used for protecting resources by the Resource Server. The Resource Server is needed to construct the command which will protect the resource.
The command will contain an API path, HTTP methods (POST, GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy is mapped, uma_rs_check_access method will always return access as granted. For more information about uma_rpt_policies you can reference this [document](https://gluu.org/docs/oxd/3.1.1/api/#uma-2-client-apis).

**Parameters:**

- oxd_id: oxd ID from client registration
- resources: One or more protected resources that a resource server manages, abstractly, as a set. In authorization policy terminology, a resource set is the "object" being protected. 
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '/oxdlibrary/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
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


#### RS Check Access 

`uma_rs_check_access` method is used in the UMA Resource Server to check the access to the resource.

**Parameters:**

- oxd_id: oxd ID from client registration
- rpt: Requesting Party Token
- path: Path of the resource to be checked 
- http_method: HTTP methods (POST, GET and PUT)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '/oxdlibrary/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
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
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```php
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '/oxdlibrary/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
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
- oxd_host_port: (Optional) oxd port number for oxd-server type (Required if oxd-server is used)
- config: (Optional) oxd-https-extension configuration for oxd-https-extension type. It is an array returned by "oxdlibrary/oxdHttpConfig.php" file (Required if oxd-https-extension is used)

**Request:**

```javascript
$oxdRpConfig = json_decode(file_get_contents($baseUrl . '/oxdlibrary/oxd-rp-settings.json'));
$config = include('./oxdlibrary/oxdHttpConfig.php');
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