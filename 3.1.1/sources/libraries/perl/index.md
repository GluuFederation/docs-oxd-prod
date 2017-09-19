# oxd-perl

## Overview
The following documentation demonstrates 
how to use the [oxd client software](http://oxd.gluu.org) Perl library to send users from a Perl application to an 
OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 


## Sample Project

[Download a sample project](https://github.com/GluuFederation/oxd-perl/archive/master.zip) specific to this oxd library.

### System Requirements


- Ubuntu / Debian / CentOS / RHEL / Windows 7 or higher / Windows Server 2008 or higher
- Perl 5
- Apache 2.4.4 +
- OxdPerlModule 3.0.1
- CGI::Session module
- Net::SSLeay module
- IO::Socket::SSL module


## Prerequisites
### Required Software
To use the oxd Perl library, you will need:

- A valid OpenID Connect Provider (OP), like Google or 
the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/).    
- An active installation of the [oxd server](../../oxd-server/install/index.md) running in the same server as the client application.     
- A Windows server or Windows installed machine / Linux server or Linux installed machine.

### Install oxdPerlModule from CPAN

To install oxdPerlModule from CPAN run the following command in 
Linux terminal or Windows command window

``` {.code }
cpan > install INDERPAL/OxdPerlModule-0.01.tar.gz
```

To install CGI::Session, Net::SSLeay and IO::Socket::SSL modules run the following commands in CPAN

``` {.code }
cpan > install CGI::Session
cpan > install Net::SSLeay
cpan > install IO::Socket::SSL
```



### Configure the Client Application

- The oxd-perl configuration file is located at `oxd-settings.json`. This file has to 
be in the Client application root directory. The values here are used during Client 
application registration in OpenID Connect Provider. For a full list of supported oxd 
configuration parameters, check this [document](https://gluu.org/docs/ce/3.0.2/api-guide/openid-connect-api/). 
Below is a typical configuration data set required for registration:

```javascript
{
  "op_host": "<https://ophost_url>",
  "oxd_host_port":8099,
  "authorization_redirect_uri" : "https://client.example.com/login.cgi",
  "post_logout_redirect_uri" : "https://client.example.com/logout.cgi",
  "client_logout_uris" : "https://client.example.com/logout.cgi",
  "scope" : [ "openid", "profile", "email" ],
  "application_type" : "web",
  "response_types" : ["code"],
  "grant_types":["authorization_code"],
  "acr_values" : [ "basic" ]
}
```

- Your client application must have a valid ssl cert, so the url includes: `https://`    


#### Linux

Install Perl on ubuntu
```bash
$ sudo apt-get install perl
$ sudo apt-get install libapache2-mod-perl2 
```
Then create virtual host of oxd-perl `oxd-perl.conf` 
under `/etc/apache2/sites-available/`  file and add these lines :

```bash
$ cd /etc/apache2/sites-available
$ vim oxd-perl-example.conf
```
add below mention lines on  virtual host file

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

Then enable `oxd-perl-example.conf` virtual host by running:

```bash
$ sudo a2ensite oxd-perl-example.conf 
```

After that add domain name in virtual host file.
In console:

```bash
$ sudo nano /etc/hosts
```

Add these lines in virtual host file:
```
127.0.0.1 www.client.example.com
127.0.0.1  client.example.com
```

Reload the apache server

```bash
$ sudo service apache2 restart
```
**Setting up and running demo app**

Navigate to perl app root:

```bash
Copy example folder from oxdPerl directory and placed on root folder

cd /var/www/html/oxd-perl/example
```

#### Windows

- The client hostname should be a valid `hostname` (FQDN), not localhost or an IP Address. 
You can configure the hostname by adding the following entry in 
your `C:\Windows\System32\drivers\etc\hosts` file:

    `127.0.0.1  client.example.com`
    
- Enable SSL by	adding the following lines on virtual host file of Apache in the 
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

- [Register Site](../../oxd-server/api/#register-site)    
- [Update Site Registration](../../oxd-server/api/#update-site-registration)    
- [Get Authorization URL](../../oxd-server/api/#get-authorization-url)   
- [Get Tokens by Code](../../oxd-server/api/#get-tokens-id-access-by-code)    
- [Get User Info](../../oxd-server/api/#get-user-info)   
- [Get Logout URI](../../oxd-server/api/#log-out-uri) 


## Sample code

***index.cgi***

Use following code to assign required parameter values from `oxd-settings.json` to the perl client application. These values can be used as parameter for the oxd server methods.


```perl
	$object = new OxdConfig();
	my $opHost = $object->getOpHost();
	my $oxdHostPort = $object->getOxdHostPort();
	my $authorizationRedirectUrl = $object->getAuthorizationRedirectUrl();
	my $postLogoutRedirectUrl = $object->setPostLogoutRedirectUrl();
	my $scope = $object->getScope();
	my $applicationType = $object->getApplicationType();
	my $responseType = $object->getResponseType();
	my $grantType = $object->getGrantTypes();
	my $acrValues = $object->getAcrValues();
```

### Register Site

In order to use an OpenID Connect Provider (OP) for login, you need to register 
your client application at the OP. During registration oxd will dynamically register 
the OpenID Connect client and save its configuration. Upon successful registration a 
unique identifier will be issued by the oxd server. If your OpenID Connect Provider 
does not support dynamic registration (like Google), you will need to obtain a ClientID 
and Client Secret which can be passed to the `OxdRegister` module as a parameter. 
The Register Site method is a one time task to configure a client in the oxd server and OP.

**Required parameters:**

- opHost: The URL of the OpenID Connect Provider (OP)
- authorizationRedirectUrl: A URL which the OP is authorized to redirect the user 
after authorization.

**Request:**
```perl
my $register_site = new OxdRegister( );
		
$register_site->setRequestOpHost($opHost);
$register_site->setRequestAcrValues($acrValues);
$register_site->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
$register_site->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$register_site->setRequestScope($scope);
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

### Update Site Registration

The `UpdateRegistration` module can be used to update an existing client in the OP. 

Fields like Authorization redirect url, post logout url, scope, client secret and other fields 
can be updated using this method.

**Required parameters:**

- oxd_id: oxd Id from client registration
- authorization_redirect_uri: A URL which the OP is authorized to redirect the user after authorization.

- post_logout_redirect_uri: URL to which the RP is requesting that the End-User's User Agent be redirected after a logout has been performed.

**Request:**

```perl
my $update_site_registration = new UpdateRegistration();
			
$update_site_registration->setRequestOxdId($oxd_id);
$update_site_registration->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
$update_site_registration->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$update_site_registration->setRequestContacts([$email]);
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

### Get Authorization URL

The `GetAuthorizationUrl` module returns the OpenID Connect Provider authentication URL to which the client application must redirect the user to authorize the release of personal data. The response URL includes state value, which can be used to obtain tokens required for authentication. This state value used to maintain state between the request and the callback.

**Required parameters:**

- oxd_id: oxd Id from client registration

**Request:**

```perl
my $get_authorization_url = new GetAuthorizationUrl( );

$get_authorization_url->setRequestOxdId($oxd_id);
$get_authorization_url->setRequestScope($scope);
$get_authorization_url->setRequestAcrValues($acrValues);
$get_authorization_url->request();

my $oxdurl = $get_authorization_url->getResponseAuthorizationUrl();

print Dumper($get_authorization_url->getResponseObject());
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

###  Get Tokens by Code

Upon successful login, the login result will return code and state. `GetTokenByCode` module uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**

- oxd_id: oxd Id from client registration
- code: The Code from OP authorization redirect url
- state: The State from OP authorization redirect url

**Request:**

```perl
my $code = $cgi->escapeHTML($cgi->param("code"));
my $state = $cgi->escapeHTML($cgi->param("state"));
my $session_state = $cgi->escapeHTML($cgi->param("session_state"));

my $get_tokens_by_code = new GetTokenByCode();

$get_tokens_by_code->setRequestOxdId($oxd_id);
$get_tokens_by_code->setRequestCode($code);
$get_tokens_by_code->setRequestState($state);
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

### Get User Info

Once the user has been authenticated by the OpenID Connect Provider, the `GetUserInfo` module returns Claims (Like First Name, Last Name, emailId, etc.) about the authenticated end user.

**Required parameters:**

- oxd_id: oxd Id from client registration
- accessToken: accessToken from GetTokenByCode

**Request:**

```perl
my $get_user_info = new GetUserInfo();

$get_user_info->setRequestOxdId($oxd_id);
$get_user_info->setRequestAccessToken($accessToken);
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


### Logout

`OxdLogout` module returns the OpenID Connect Provider logout url. Client application  uses this logout url to end the user session.

**Required parameters:**

- oxd_id: oxd Id from client registration

**Request:**

```perl
my $logout = new OxdLogout();

$logout->setRequestOxdId($oxd_id);
$logout->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$logout->setRequestIdToken($user_oxd_id_token);
$logout->setRequestSessionState($session_state);
$logout->setRequestState($state);
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


## Support
Please report technical issues and suspected bugs on our [support page](https://support.gluu.org/). You can use the same credentials you created to register for your oxd license to sign in on Gluu support.
