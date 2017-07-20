# oxd-php

## Overview
The following documentation demonstrates 
how to use the [oxd client software](http://oxd.gluu.org) PHP library to send users from a PHP application to an 
OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 


## Sample Project

[Download a sample project](https://github.com/GluuFederation/oxd-php-library/archive/v3.0.1.zip) specific to this oxd library.

### System Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows 7 + / Windows Server 2008 +
- PHP 5.3 or higher
- Apache 2.4 or higher
- oxd-php-library 3.0.1

## Prerequisites

### Required Software
To use the oxd PHP library, you will need:

- A valid OpenID Connect Provider (OP), like Google or the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/);    
- An active installation of the [oxd server](../../install/index.md) running in the same server as the client application;      
- A Windows server or Windows installed machine / Linux server or Linux installed machine.

### Install oxd-php-library from Composer
To install oxd PHP library from composer follow the following commands

```composer
$ composer require "gluufederation/oxd-php-api"
```

### Configure the Client Application

- The oxd-php-library configuration file is located at `oxdlibrary/oxd-rp-settings.json`. The values here are used during Client application registration in OpenID Connect Provider. For a full list of supported oxd configuration parameters, check this [document](../api-guide/openid-connect-api/). Below is a typical configuration data set required for registration:

    ```javascript
    {
    "op_host": "<OpenID Connect Provider Host>",
    "oxd_host_port":8099,
    "authorization_redirect_uri" : "https://client.example.com/login/index.php",
    "post_logout_redirect_uri" : "https://client.example.com/logout/index.php",
    "scope" : [ "openid", "profile","email" ],
    "application_type" : "web",
    "response_types" : ["code"],
    "grant_types":["authorization_code"],
    "acr_values" : [ "basic" ]
    }
                            
    ```

- The PHP class `Oxd_RP_config` in the file `Oxd_RP_config.php` maps the above json setting and provides a config object to the sample project.


- Your client application must have a valid ssl cert, so the url includes: `https://`    

- The client hostname should be a valid hostname, not localhost or an IP Address. You can configure the hostname by adding the following entry in your `/etc/host` (for Ubuntu, might be different for different flavours of linux) file:

    `127.0.0.1  client.example.com`

- Enable SSL by	adding the following lines on virtual host file of Apache in the location 

    **Linux**

    `/etc/apache2/sites-available/000-default.conf`:

   **Windows**

    `<apache installation directory>/conf/extra/httpd-vhosts.conf`:


    ```apache
        <VirtualHost *>
            ServerName client.example.com
            ServerAlias client.example.com
            DocumentRoot "<apache web root directory>"
        </VirtualHost>

        <VirtualHost *:443>
            DocumentRoot "<apache web root directory>"
            ServerName client.example.com
            ServerAlias www.client.example.com
            SSLEngine on
            SSLCertificateFile "<Path to your ssl certificate file>"
            SSLCertificateKeyFile "<Path to your ssl certificate key file>"
            <Directory "<apache web root directory>">
                AllowOverride All
                Order allow,deny
                Allow from all
            </Directory>
        </VirtualHost>
    ```



## oxd Server Methods
The oxd server provides the following six methods for authenticating users with an OpenID Connect Provider (OP):

- [Register Site](../protocol/#register-site)    
- [Update Site Registration](../protocol/#update-site-registration)    
- [Get Authorization URL](../protocol/#get-authorization-url)   
- [Get Tokens by Code](../protocol/#get-tokens-id-access-by-code)    
- [Get User Info](../protocol/#get-user-info)   
- [Get Logout URI](../protocol/#log-out-uri)   

## Sample code

### Register Site
In order to use an OpenID Connect Provider (OP) for login, you need to register your client application at the OP. During registration oxd will dynamically register the OpenID Connect client and save its configuration. Upon successful registration a unique identifier will be issued by the oxd server. If your OpenID Connect Provider does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be set in the `Register_site` class as a property. The Register Site is a one time task to configure a client in the oxd server and OP.

**Required parameters:**

- op_host: The URL of the OpenID Connect Provider (OP)

- authorization_redirect_uri: A URL which the OP is authorized to redirect the user after authorization.

**Request:**

```php

include_once '../Register_site.php';

$register_site = new Register_site();
$register_site->setRequestOpHost(Oxd_RP_config::$op_host);
$register_site->setRequestAcrValues(Oxd_RP_config::$acr_values);
$register_site->setRequestAuthorizationRedirectUri(Oxd_RP_config::$authorization_redirect_uri);
$register_site->setRequestPostLogoutRedirectUri(Oxd_RP_config::$post_logout_redirect_uri);
$register_site->setRequestContacts(["test@test.test"]);
$register_site->setRequestGrantTypes(Oxd_RP_config::$grant_types);
$register_site->setRequestResponseTypes(Oxd_RP_config::$response_types);
$register_site->setRequestScope(Oxd_RP_config::$scope);

$register_site->request();
$_SESSION['oxd_id'] = $register_site->getResponseOxdId();                
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
The `Update_site_registration` class can be used to update an existing client in the OP. Fields like Authorization redirect url, post logout url, scope, client secret etc. can be updated using this method.

**Required parameters:**

- oxd_id: oxd Id from client registration

- authorization_redirect_uri: A URL which the OP is authorized to redirect the user after authorization.

- post_logout_redirect_uri: URL to which the RP is requesting that the End-User's User Agent be redirected after a logout has been performed.

**Request:**

```php

include_once '../Update_site_registration.php';

$update_site_registration = new Update_site_registration();
$update_site_registration->setRequestAcrValues(Oxd_RP_config::$acr_values);
$update_site_registration->setRequestOxdId($_SESSION['oxd_id']);
$update_site_registration->setRequestAuthorizationRedirectUri(Oxd_RP_config::$authorization_redirect_uri);
$update_site_registration->setRequestPostLogoutRedirectUri(Oxd_RP_config::$post_logout_redirect_uri);
$update_site_registration->setRequestContacts(["test@test.test"]);
$update_site_registration->setRequestGrantTypes(Oxd_RP_config::$grant_types);
$update_site_registration->setRequestResponseTypes(Oxd_RP_config::$response_types);
$update_site_registration->setRequestScope(Oxd_RP_config::$scope);
$update_site_registration->request();
print_r($update_site_registration->getResponseObject());                    
```
**Response:**

```javascript
{
    "status":"ok"
}
```

### Get Authorization URL
The `getResponseAuthorizationUrl` method of class `Get_authorization_url` returns the OpenID Connect Provider authentication URL to which the client application must redirect the user to authorize the release of personal data. The response URL includes state value, which can be used to obtain tokens required for authentication. This state value used to maintain state between the request and the callback.

**Required parameters:**

- oxd_id: oxd Id from client registration

**Request:**

```php

require_once '../Get_authorization_url.php';

$get_authorization_url = new Get_authorization_url();
$get_authorization_url->setRequestOxdId($_SESSION['oxd_id']);
$get_authorization_url->setRequestAcrValues(Oxd_RP_config::$acr_values);
$get_authorization_url->setRequestScope(Oxd_RP_config::$scope);
$get_authorization_url->request();
echo $get_authorization_url->getResponseAuthorizationUrl();
                        
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

Upon successful login, the login result will return code and state. `Get_tokens_by_code` class uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**

- oxd_id: oxd Id from client registration

- code: The Code from OP authorization redirect url 

- state: The State from OP authorization redirect url

**Request:**

```php

require_once '../Get_tokens_by_code.php';

$get_tokens_by_code = new Get_tokens_by_code();
$get_tokens_by_code->setRequestOxdId($_SESSION['oxd_id']);
//getting code from redirecting url, when user allowed.
$get_tokens_by_code->setRequestCode($_GET['code']);
$get_tokens_by_code->setRequestState($_GET['state']);
$get_tokens_by_code->request();
$_SESSION['id_token'] = $get_tokens_by_code->getResponseIdToken();
$_SESSION['access_token'] = $get_tokens_by_code->getResponseAccessToken();
print_r($get_tokens_by_code->getResponseObject());

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
Once the user has been authenticated by the OpenID Connect Provider, the `getResponseObject` method of class `Get_user_info` returns Claims (Like First Name, Last Name, emailId, etc.) about the authenticated end user.

**Required parameters:**

- oxd_id: oxd Id from client registration

- access_token: accessToken from GetTokenByCode

**Request:**

```php

require_once '../Get_user_info.php';

echo '<br/>Get_user_info <br/>';
$get_user_info = new Get_user_info();
$get_user_info->setRequestOxdId($_SESSION['oxd_id']);
$get_user_info->setRequestAccessToken($_SESSION['access_token']);
$get_user_info->request();
print_r($get_user_info->getResponseObject());
                        
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

`getResponseHtml` method of class `Logout` returns the OpenID Connect Provider logout url. Client application  uses this logout url to end the user session.


**Required parameters:**

- oxd_id: oxd Id from client registration

**Request:**

```php

require_once '../Logout.php';

$logout = new Logout();
$logout->setRequestOxdId($_SESSION['oxd_id']);
$logout->setRequestPostLogoutRedirectUri(Oxd_RP_config::$logout_redirect_uri);
$logout->setRequestIdToken($_SESSION['user_oxd_access_token']);
$logout->setRequestSessionState($_SESSION['session_states']);
$logout->setRequestState($_SESSION['states']);
$logout->request();

echo $logout->getResponseHtml();
                        
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