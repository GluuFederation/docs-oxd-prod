# oxd-perl

## Overview
The following documentation demonstrates 
how to use the [oxd Client Software](http://oxd.gluu.org) Perl library to send users from a Perl application to an 
OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 


## Sample Project

[Download a Sample Project](https://github.com/GluuFederation/oxd-perl/archive/3.1.1.zip) specific to this oxd-perl library.

### System Requirements


- Ubuntu / Debian / CentOS / RHEL / Windows 7 or higher / Windows Server 2008 or higher
- Perl 5
- Apache 2.4.4 +
- oxdperl 3.1.1
- CGI::Session module
- Net::SSLeay module
- IO::Socket::SSL module


## Prerequisites

### Required Software

To use the oxd-perl library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/) or Google.
- An active installation of the [oxd-server](../../install/index.md) running on the same server as the client application.
- An active installation of the [oxd-https-extension](../../install/index.md) if oxd-https-extension connection is used.  In this case, client applications can be on different servers but will be able to access oxd-https-extension.
- A Windows server or Windows installed machine / Linux server or Linux installed machine.

### Install oxdPerl from CPAN

To install oxdPerl from CPAN, run the following command in 
Linux terminal or Windows command window

``` {.code }
cpan > install GLUU/oxdperl-0.02.tar.gz
```

To install CGI::Session, Net::SSLeay and IO::Socket::SSL modules run the following commands in CPAN

``` {.code }
cpan > install CGI::Session
cpan > install Net::SSLeay
cpan > install IO::Socket::SSL
```



### Configure the Client Application

- Client application must have a valid SSL certificate, so the URL includes: `https://`    


#### Linux

Install Perl on ubuntu
```bash
$ sudo apt-get install perl
$ sudo apt-get install libapache2-mod-perl2 
```
Create a virtual host of oxd-perl `oxd-perl.conf` 
under `/etc/apache2/sites-available/`  file and add these lines:

```bash
$ cd /etc/apache2/sites-available
$ vim oxd-perl-example.conf
```
Add the following lines to the virtual host file:

```
<IfModule mod_ssl.c>
    <VirtualHost _default_:443>

        DocumentRoot /var/www/html/oxd-perl/example/
        ServerName www.client.example.com
        ServerAlias client.example.com

        <Directory /var/www/html/oxd-perl/example/>
                        AllowOverride All
        </Directory>

        ErrorLog /var/www/html/oxd-perl/example/logs/error.log
        CustomLog /var/www/html/oxd-perl/example/logs/access.log combined

        AddType application/x-httpd-php .php
           <Files 'xmlrpc.php'>
                   Order Allow,Deny
                   deny from all
           </Files>

        SSLEngine on
        SSLCertificateFile  /etc/certs/demosite.crt
        SSLCertificateKeyFile /etc/certs/demosite.key

                <FilesMatch "\.(cgi|shtml|phtml|php)$">
                        SSLOptions +StdEnvVars
                </FilesMatch>

                # processes .cgi and .pl as CGI scripts

                ScriptAlias /cgi-bin/ /var/www/html/oxd-perl/
                <Directory "/var/www/html/oxd-perl">
                        Options +ExecCGI
                        SSLOptions +StdEnvVars
                        AddHandler cgi-script .cgi .pl
                </Directory>

                BrowserMatch "MSIE [2-6]" \
            nokeepalive ssl-unclean-shutdown \
            downgrade-1.0 force-response-1.0
                BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown

        </VirtualHost>
</IfModule>
```

Enable `oxd-perl-example.conf` virtual host by running:

```bash
$ sudo a2ensite oxd-perl-example.conf 
```

Add domain name in the virtual host file. In console:

```bash
$ sudo nano /etc/hosts
```

In virtual host file add:
```
127.0.0.1 www.client.example.com
127.0.0.1  client.example.com
```

Reload the Apache Server:

```bash
$ sudo service apache2 restart
```
Set up and run the demo application. Navigate to perl app root:

```bash
Copy example folder from oxdPerl directory and placed on root folder

cd /var/www/html/oxd-perl/example
```

#### Windows

- The client hostname should be a valid `hostname` (FQDN), not a localhost or an IP Address. 
You can configure the hostname by adding the following entry in  `C:\Windows\System32\drivers\etc\hosts` file:

    `127.0.0.1  client.example.com`
    
- Enable SSL by	adding the following lines to the virtual host file of Apache in the 
location `C:/apache/conf/extra/httpd-vhosts.conf`:

    ```apache
    <VirtualHost *>
        ServerName client.example.com
        ServerAlias client.example.com
        DocumentRoot "<apache web root directory>"
    </VirtualHost>

    <VirtualHost *:443>
        DocumentRoot "<apache web root directory>"
        ServerName client.example.com
        SSLEngine on
        SSLCertificateFile "<Path to ssl certificate file>"
        SSLCertificateKeyFile "<Path to ssl certificate key file>"
        <Directory "<apache web root directory>">
            AllowOverride All
            Order allow,deny
            Allow from all
        </Directory>
    </VirtualHost>
    ```

- Download the [Sample Project](https://github.com/GluuFederation/oxd-perl/archive/3.1.1.zip)

- Configure Apache to treat the project directory as a script directory. In the following location, `C:\Program Files\Apache Group\Apache\conf\httpd.conf`, set the path to `httpd.conf` 

    ```
    ScriptAlias /cgi-bin/ "<path to CGI files>"
    ```

- To run CGI scripts and .pl extension anywhere in the domain, add the following line to `httpd.conf` file:

    ```
    AddHandler cgi-script .cgi .pl
    ```

- In the `Directory` section of `httpd.conf` file, add the folowing CGI path:

    ```code
    <Directory "<path to CGI files>">
        AllowOverride All
        Options None
        Require all granted
    </Directory>
    ```

- The first line of perl script contains `#!/usr/bin/perl`, replace it with the path of perl.exe `#!C:/program files/perl/bin/perl.exe` 

- Restart the Apache server.

- Move oxdperl module from lib directory to the lib directory of the perl installation (\perl\lib).

- With the oxd-server and Apache Server running, navigate to the URL's below to run Sample Client Application. To register a client in the oxd-server use the Setup client URL. Upon successful registration of the client application, oxd ID will be displayed in the UI. Next, navigate to the Login URL for authentication.
    - Setup Client URL: https://client.example.com:8090/cgi-bin/settings.cgi
    - Login URL: https://client.example.com:8090/cgi-bin/index.cgi
    - UMA URL: https://client.example.com:8090/cgi-bin/uma.cgi

- The input values used during Setup Client are stored in a configuration file (oxd-settings.json).


## Endpoints (oxd-server and oxd-https-extension)

The oxd-server provides the following methods for authenticating users with 
an OpenID Connect Provider (OP):

- Available OpenID Connect Endpoints
    - [Setup Client](../../protocol/#setup-client)  
    - [Get Client Token](../../protocol/#get-client-token)
    - [Register Site](../../protocol/#register-site) 
    - [Update Site Registration](../../protocol/#update-site-registration)
    - [Get Authorization URL](../../protocol/#get-authorization-url)   
    - [Get Tokens by Code](../../protocol/#get-tokens-id-access-by-code)
    - [Get Access Token by Refresh Token](../../protocol/#get-access-token-by-refresh-token)    
    - [Get User Info](../../protocol/#get-user-info)   
    - [Get Logout URI](../../protocol/#log-out-uri) 


The oxd-server provides the following methods for authenticating users with UMA Authorization Service (AS):

- Available UMA (User Managed Access) Endpoints  
    - [UMA RS Protect](../../protocol/#uma-rs-protect) 
    - [UMA RS Check Access](../../protocol/#uma-rs-check-access) 
    - [UMA RP Get RPT](../../protocol/#uma-rp-get-rpt) 
    - [UMA RP Get Claims Gathering URL](../../protocol/#uma-rp-get-claims-gathering-url) 


## Sample Code

***index.cgi***

Use the following code to assign the required parameter values from `oxd-settings.json` to the perl client application. These values can be used as parameters for the oxd-server methods.


```perl
    $object = new OxdConfig();
    my $opHost = $object->getOpHost();
    my $oxdHostPort = $object->getOxdHostPort();
    my $authorizationRedirectUrl = $object->getAuthorizationRedirectUrl();
    my $postLogoutRedirectUrl = $object->getPostLogoutRedirectUrl();
    my $clientFrontChannelLogoutUrl = $object->getClientFrontChannelLogoutUris();
    my $scope = $object->getScope();
    my $applicationType = $object->getApplicationType();
    my $responseType = $object->getResponseType();
    my $grantType = $object->getGrantTypes();
    my $acrValues = $object->getAcrValues();
    my $restServiceUrl = $object->getRestServiceUrl();
    my $connectionType = $object->getConnectionType();
    my $oxd_id = $object->getOxdId();
    my $client_name = $object->getClientName();
    my $client_id = $object->getClientId();
    my $client_secret = $object->getClientSecret();
```

### OpenID Connect

#### Setup Client

In order to use an OpenID Connect Provider (OP) for login, 
you need to setup your client application at the OpenID Connect Provider (OP). During setup, oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful setup, the oxd-server will assign a unique 
oxd-ID, return a Client ID and Client Secret. This Client ID and Client Secret can be used for `GetClientToken` module. If your OpenID Connect Provider (OP)
does not support dynamic registration (like Google), you will need to obtain a 
ClientID and Client Secret which can be passed to the `OxdSetupClient` module as a 
parameter. The Setup Client method is a one time task to configure a client in the 
oxd-server and OpenID Connect Provider (OP).

**Required parameters:**

- opHost: The URL of the OpenID Connect Provider (OP)
- authorizationRedirectUrl: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to
after authorization.
- client_name: (Optional) Client application name
- postLogoutRedirectUrl: (Optional) URL to which the user is redirected to after successful logout
- client_id: (Optional) Client ID from the OpenID Connect Provider (OP). Should be passed with the Client Secret. 
- client_secret: (Optional) Client Secret from the OpenID Connect Provider (OP). Should be passed with the Client ID. 
- clientFrontChannelLogoutUrl: (Optional)
- grantType: (Optional)
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP).

**Request:**

```perl
my $setup_client = new OxdSetupClient( );
	
$setup_client->setRequestOpHost($opHost);
$setup_client->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
$setup_client->setRequestClientName($client_name);
$setup_client->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$setup_client->setRequestClientId($client_id);
$setup_client->setRequestClientSecret($client_secret);
$setup_client->setRequestClientLogoutUris([$clientFrontChannelLogoutUrl]);
$setup_client->setRequestGrantTypes($grantType);
$setup_client->setRequestResponseTypes($responseType);
$setup_client->setRequestScope($scope);
$setup_client->request();

my $oxd_id = $setup_client->getResponseOxdId();
my $client_id = $setup_client->getResponseClientId();
my $client_secret = $setup_client->getResponseClientSecret();
print Dumper($setup_client->getResponseObject());
```

**Response:**

```javascript
{
  "status": "ok",
  "data": {
    "oxd_id": "6F9619FF-8B86-D011-B42D-00CF4FC964FF",
    "op_host": "https://ce-dev3-gluu.org",
    "client_id": "@!E64E.B7E6.3AC4.6CB9!0001!C05E.F402!0008!98F7.EB7B.6213.6527",
    "client_secret": "173d55ff-5a4f-429c-b50d-7899b616912a",
    "client_registration_access_token": "f8975472-240a-4395-b96d-6ef492f50b9e",
    "client_registration_client_uri": "https://iam310.centroxy.com/oxauth/restv1/register?client_id=@!E64E.B7E6.3AC4.6CB9!0001!C05E.F402!0008!98F7.EB7B.6213.6527",
    "client_id_issued_at": 1504353408,
    "client_secret_expires_at": 1504439808
  }
}
```


#### Get Client Token

The `GetClientToken` module is used to get a token which is sent as input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Required parameters:**

- opHost: OpenID Connect Provider
- client_id: Client ID from the OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: Client Secret from the OpenID Connect Provider (OP). Should be passed with Client ID.

**Request:**

```perl
my $get_client_token = new GetClientToken( );

$get_client_token->setRequestClientId($client_id);
$get_client_token->setRequestClientSecret($client_secret);
$get_client_token->setRequestOpHost($opHost);
$get_client_token->request();

my $protection_access_token = $get_client_token->getResponseAccessToken();
print Dumper($get_client_token->getResponseObject());
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


#### Register Site

In order to use an OpenID Connect Provider (OP) for login, you need to register 
your client application at the OP. During registration oxd will dynamically register 
the OpenID Connect client and save its configuration. Upon successful registration, a 
unique identifier will be issued by the oxd-server. If your OpenID Connect Provider (OP)
does not support dynamic registration (like Google), you will need to obtain a ClientID 
and Client Secret which can be passed to the `OxdRegister` module as a parameter. 
The Register Site method is a one time task to configure a client in the oxd-server and OpenID Connect Provider (OP).

!!! Note: 
    The `Register Site` endpoint is not required if client is registered using `Setup Client`

**Required parameters:**

- opHost: URL of the OpenID Connect Provider (OP)
- authorizationRedirectUrl: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to
after authorization.
- postLogoutRedirectUrl: (Optional) URL to which the user is redirected to after successful logout.
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by OP
- protection_access_token: Generated from GetClientToken module (Optional, required if oxd-https-extension is used)

**Request:**
```perl
my $register_site = new OxdRegister( );
		
$register_site->setRequestOpHost($opHost);
$register_site->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
$register_site->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$register_site->setRequestScope($scope);
$register_site->setRequestProtectionAccessToken($protection_access_token);
$register_site->request();

my $oxd_id = $register_site->getResponseOxdId();
print Dumper($register_site->getResponseObject());
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


#### Update Site Registration

The `UpdateRegistration` module can be used to update an existing client in the OP. 

Fields like Authorization redirect url, post logout url, scope, client secret and other fields 
can be updated using this method.

**Required parameters:**

- oxd_id: oxd ID from client registration
- authorization_redirect_uri: (Optional) URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization.
- post_logout_redirect_uri: (Optional) URL to which the RP is requesting the End-User's User Agent be redirected to after a logout has been performed.
- grantType: 
- protection_access_token: Generated from GetClientToken module (Optional, required if oxd-https-extension is used)

**Request:**

```perl
my $update_site_registration = new UpdateRegistration();
			
$update_site_registration->setRequestOxdId($oxd_id);
$update_site_registration->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
$update_site_registration->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$update_site_registration->setRequestGrantTypes($grantType);
$update_site_registration->setRequestProtectionAccessToken($protection_access_token);
$update_site_registration->request();

my $oxd_id = $update_site_registration->getResponseOxdId();
print Dumper($update_site_registration->getResponseObject());
```

**Response:**
```javascript
{
    "status":"ok"
}
```


#### Get Authorization URL

The `GetAuthorizationUrl` module returns the OpenID Connect Provider (OP) authentication URL to which the client application must redirect the user to authorize the release of personal data. The response URL includes state value, which can be used to obtain tokens required for authentication. This state value used to maintain state between the request and the callback.

**Required parameters:**

- oxd_id: oxd ID from client registration
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by OP.
- acrValues: (Optional) Required for extened authenication. Custom Authentication script from Gluu server.
- customParams: (Optional) custom parameters
- protection_access_token: Generated from GetClientToken module (Optional, required if oxd-https-extension is used)

**Request:**

```perl
my $get_authorization_url = new GetAuthorizationUrl( );

$get_authorization_url->setRequestOxdId($oxd_id);
$get_authorization_url->setRequestScope($scope);
$get_authorization_url->setRequestAcrValues($acrValues);
$get_authorization_url->setRequestCustomParam($customParams);
$get_authorization_url->setRequestProtectionAccessToken($protection_access_token);
$get_authorization_url->request();

my $oxdurl = $get_authorization_url->getResponseAuthorizationUrl();
print Dumper($get_authorization_url->getResponseObject());
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


###  Get Tokens by Code

Upon successful login, the login result will return code and state. `GetTokenByCode` module uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**

- oxd_id: oxd ID from client registration
- code: The code from OpenID Connect Provider (OP) authorization redirect URL
- state: The state from OpenID Connect Provider (OP) authorization redirect URL
- protection_access_token: Generated from GetClientToken module (Optional, required if oxd-https-extension is used)

**Request:**

```perl
my $code = $cgi->escapeHTML($cgi->param("code"));
my $state = $cgi->escapeHTML($cgi->param("state"));
my $session_state = $cgi->escapeHTML($cgi->param("session_state"));

my $get_tokens_by_code = new GetTokenByCode();

$get_tokens_by_code->setRequestOxdId($oxd_id);
$get_tokens_by_code->setRequestCode($code);
$get_tokens_by_code->setRequestState($state);
$get_tokens_by_code->setRequestProtectionAccessToken($protection_access_token);
$get_tokens_by_code->request();

my $user_oxd_id_token = $get_tokens_by_code->getResponseIdToken();
my $accessToken = $get_tokens_by_code->getResponseAccessToken();
my $refreshToken = $get_tokens_by_code->getResponseRefreshToken();
print Dumper($get_tokens_by_code->getResponseObject());
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


#### Get Access Token by Refresh Token

The `GetAccessTokenByRefreshToken` module is used to get a new access token and a new refresh token by using the refresh token which is obtained from `GetTokensByCode` module.

**Required parameters:**

- oxd_id: oxd ID from client registration
- refresh_token: Generated from the GetTokensByCode module
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- protection_access_token: Generated from GetClientToken module (Optional, required if oxd-https-extension is used)

**Request:**

```perl
$get_access_token_by_refresh_token = new GetAccessTokenByRefreshToken();

$get_access_token_by_refresh_token->setRequestOxdId($oxd_id);
$get_access_token_by_refresh_token->setRequestRefreshToken($refresh_token);
$get_access_token_by_refresh_token->setRequestScopes($scope);
$get_access_token_by_refresh_token->setRequestProtectionAccessToken($protection_access_token);
$get_access_token_by_refresh_token->request();

$new_access_token = $get_access_token_by_refresh_token->getResponseAccessToken();
$new_refresh_token = $get_access_token_by_refresh_token->getResponseRefreshToken();
print Dumper($get_access_token_by_refresh_token->getResponseObject());
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


#### Get User Info

Once the user has been authenticated by the OpenID Connect Provider (OP), the `GetUserInfo` module returns the claims (First Name, Last Name, E-Mail ID, etc.) about the authenticated end user.

**Required parameters:**

- oxd_id: oxd ID from client registration
- accessToken: accessToken from GetTokenByCode or GetAccessTokenbyRefreshToken
- protection_access_token: Generated from GetClientToken module (Optional, required if oxd-https-extension is used)

**Request:**

```perl
my $get_user_info = new GetUserInfo();

$get_user_info->setRequestOxdId($oxd_id);
$get_user_info->setRequestAccessToken($accessToken);
$get_user_info->setRequestProtectionAccessToken($protection_access_token);
$get_user_info->request();

my $username = $get_user_info->getResponseObject()->{data}->{claims}->{name}[0];
my $useremail = $get_user_info->getResponseObject()->{data}->{claims}->{email}[0];
print Dumper($get_user_info->getResponseObject());
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


#### Logout

`OxdLogout` module returns the OpenID Connect Provider logout url. Client application  uses this logout url to end the user session.

**Required parameters:**

- oxd_id: oxd ID from client registration
- id_token_hint: (Optional)
- postLogoutRedirectUrl: (Optional) URL to which user is redirected to after successful logout
- state: (Optional)
- session_state: (Optional)
- protection_access_token: Generated from GetClientToken module (Optional, required if oxd-https-extension is used)

**Request:**

```perl
my $logout = new OxdLogout();

$logout->setRequestOxdId($oxd_id);
$logout->setRequestIdToken($id_token_hint);
$logout->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$logout->setRequestState($state);
$logout->setRequestSessionState($session_state);
$logout->setRequestProtectionAccessToken($protection_access_token);
$logout->request();

$session->delete();
$logoutUrl = $logout->getResponseObject()->{data}->{uri};
print Dumper($logout->getResponseObject());
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
### UMA

#### UMA RS Protect

`UmaRsProtect` module is used for protecting resources by the Resource Server. The Resource Server is needed to construct the command which will protect the resource.
The command will contain an API path, HTTP methods (POST,GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy is mapped, uma_rs_check_access method will always return access as granted. To know more about uma_rpt_policies you can check this [document](https://gluu.org/docs/oxd/3.1.1/api/#uma-2-client-apis).

**Required parameters:**

- oxd_id: oxd ID from client registration
- protection_access_token: Generated from GetClientToken module (Optional, required if oxd-https-extension is used)

**Request:**

```perl
$uma_rs_protect = new UmaRsProtect();

$uma_rs_protect->setRequestOxdId($oxd_id);

$uma_rs_protect->addConditionForPath(["GET"],["https://photoz.example.com/dev/actions/view"], ["https://photoz.example.com/dev/actions/view"]);
$uma_rs_protect->addConditionForPath(["POST"],[ "https://photoz.example.com/dev/actions/add"],[ "https://photoz.example.com/dev/actions/add"]);
$uma_rs_protect->addConditionForPath(["DELETE"],["https://photoz.example.com/dev/actions/remove"], ["https://photoz.example.com/dev/actions/remove"]);
$uma_rs_protect->addResource('/photo');
$uma_rs_protect->setRequestProtectionAccessToken($protection_access_token);
$uma_rs_protect->request();

print Dumper( $uma_rs_protect->getResponseObject() );
```

**Response:**

```javascript
{
    "status":"ok"
}
```


#### UMA RS Check Access 

`UmaRsCheckAccess` module used in a UMA Resource Server to check the access to the resource.

**Required parameters:**

- oxd_id: oxd ID from client registration
- rpt: Requesting Party Token
- path: Path of the resource to be checked
- http_method: HTTP methods (POST, GET and PUT)
- protection_access_token: Generated from GetClientToken module (Optional, required if oxd-https-extension is used)

**Request:**

```perl
$uma_rs_check_access = new UmaRsCheckAccess();

$uma_rs_check_access->setRequestOxdId($oxd_id);
$uma_rs_check_access->setRequestRpt($rpt);
$uma_rs_check_access->setRequestPath($path);
$uma_rs_check_access->setRequestHttpMethod($http_method);
$uma_rs_check_access->setRequestProtectionAccessToken($protection_access_token);
$uma_rs_check_access->request();

my $uma_ticket= $uma_rs_check_access->getResponseTicket();
print Dumper($uma_rs_check_access->getResponseObject());
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

***Resource is not Protected:***

```javascript
{
    "status":"error",
    "data":{
        "error":"invalid_request",
        "error_description":"Resource is not protected. Please protect your resource first with uma_rs_protect command."
    }
}
```


#### UMA RP Get RPT 

The module `UmaRpGetRpt` is called in order to obtain the RPT (Requesting Party Token).

**Required parameters:**

- oxd_id: oxd ID from client registration
- ticket: Client Access Ticket generated by UmaRsCheckAccess module
- claim_token: (Optional)
- claim_token_format: (Optional)
- pct: (Optional) Persisted Claims Token
- rpt: (Optional) Requesting Party Token
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by OP
- state: (Optional) state that is returned from uma_rp_get_claims_gathering_url method
- protection_access_token: Generated from GetClientToken module (Optional, required if oxd-https-extension is used)

**Request:**

```perl
$uma_rp_get_rpt = new UmaRpGetRpt();
$uma_rp_get_rpt->setRequestOxdId($oxd_id);
$uma_rp_get_rpt->setRequestTicket($ticket);
$uma_rp_get_rpt->setRequestClaimToken($claim_token);
$uma_rp_get_rpt->setRequestClaimTokenFormat($claim_token_format);
$uma_rp_get_rpt->setRequestPCT($pct);
$uma_rp_get_rpt->setRequestRPT($rpt);
$uma_rp_get_rpt->setRequestScope($scope);
$uma_rp_get_rpt->setRequestState($state);
$uma_rp_get_rpt->setRequestProtectionAccessToken($protection_access_token);
$uma_rp_get_rpt->request();

my $uma_rpt= $uma_rp_get_rpt->getResponseRpt();
print Dumper($uma_rp_get_rpt->getResponseObject());
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


#### UMA RP Get Claims Gathering URL 

**Required parameters:**

- oxd_id: oxd ID from client registration
- ticket: Client Access Ticket generated by UmaRsCheckAccess module
- claims_redirect_Uri: 
- protection_access_token: Generated from GetClientToken module (Optional, required if oxd-https-extension is used)

**Request:**

```perl
$uma_rp_get_claims_gathering_url = new UmaRpGetClaimsGatheringUrl();
$uma_rp_get_claims_gathering_url->setRequestOxdId($oxd_id);
$uma_rp_get_claims_gathering_url->setRequestTicket($ticket);
$uma_rp_get_claims_gathering_url->setRequestClaimsRedirectUri($claims_redirect_Uri);
$uma_rp_get_claims_gathering_url->setRequestProtectionAccessToken($protection_access_token);
$uma_rp_get_claims_gathering_url->request();

print Dumper($uma_rp_get_claims_gathering_url->getResponseObject());
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
