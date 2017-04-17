# oxd Perl

## Overview
The following documentation demonstrates 
how to use Gluu's commercial OAuth 2.0 client software, 
[oxd](http://oxd.gluu.org), to send users from a Perl app to an 
OpenID Connect Provider (OP) for login. You can securely send users to 
any standard OP for login, including Google and 
the [free open source Gluu Server](http://gluu.org/gluu-server).

## Installation

**Source**

oxd-perl source is available on Github:

- [Github sources](https://github.com/GluuFederation/oxd-perl)


oxd-perl source is available on cpan:

- [OxdPerlModule](http://search.cpan.org/~inderpal/)

**Download and install manually using following command**

``` {.code }   

Path : /var/www/html/oxd-perl/oxdPerl/

perl Build.PL
./Build
./Build test
sudo ./Build install

```
 
**Install from http://search.cpan.org**

``` {.code }

Run following commands in terminal

cpan 
cpan > install INDERPAL/OxdPerlModule-0.01.tar.gz
 
```
## Configuration 

The oxd-perl configuration file is located in 'oxd-settings.json'. The values here are used during registration. For a full list of supported oxd configuration parameters, see the oxd documentation Below is a typical configuration data set for registration:
``` {.code }
	{
	  "op_host": "https://ce-dev2.gluu.org",
	  "oxd_host_port":8099,
	  "authorization_redirect_uri" : "https://oxd-perl-example.com/login.cgi",
	  "post_logout_redirect_uri" : "https://oxd-perl-example.com/logout.cgi",
	  "scope" : [ "openid", "profile","uma_protection","uma_authorization" ],
	  "application_type" : "web",
	  "response_types" : ["code"],
	  "grant_types":["authorization_code"],
	  "acr_values" : [ "basic" ]
	}
```        
-    oxd_host_port - oxd port or socket

### Sample code

#### OxdConfig.pm

    Class description.
    Oxd RP config.

**Example**
``` {.code}

OxdConfig:

	Configuration Values from oxd-settings.json

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

####OxdRegister.pm

- [Register_site protocol description](./protocol/#register-site).

**Example**

``` {.code}
OxdRegister:
my $register_site = new OxdRegister( );
		
$register_site->setRequestOpHost($gluu_server_url);
$register_site->setRequestAcrValues($acrValues);
$register_site->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
$register_site->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$register_site->setRequestContacts([$emal]);
$register_site->setRequestGrantTypes($grantType);
$register_site->setRequestResponseTypes($responseType);
$register_site->setRequestScope($scope);
$register_site->setRequestApplicationType($applicationType);
$register_site->request();

# storing data in the session
$session->param('oxd_id', $register_site->getResponseOxdId());
	
```

#### UpdateRegistration.pm

- [Update_site_registration protocol description](./protocol/#update-site-registration).

**Example**

``` {.code}
UpdateRegistration:

$update_site_registration = new UpdateRegistration();
			
$update_site_registration->setRequestAcrValues($acrValues);
$update_site_registration->setRequestOxdId($oxd_id);
$update_site_registration->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
$update_site_registration->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$update_site_registration->setRequestContacts([$emal]);
$update_site_registration->setRequestGrantTypes($grantType);
$update_site_registration->setRequestResponseTypes($responseType);
$update_site_registration->setRequestScope($scope);
$update_site_registration->request();

$session->param('oxd_id', $update_site_registration->getResponseOxdId());

```

####GetAuthorizationUrl.pm


- [Get_authorization_url protocol description](./protocol/#get-authorization-url).

**Example**

``` {.code}
GetAuthorizationUrl:

$get_authorization_url = new GetAuthorizationUrl( );
$get_authorization_url->setRequestOxdId($session->param('oxd_id'));
$get_authorization_url->setRequestScope($scope);
$get_authorization_url->setRequestAcrValues($acrValues);
$get_authorization_url->request();
my $oxdurl = $get_authorization_url->getResponseAuthorizationUrl();
```

####  GetTokenByCode.pm
- [Get_tokens_by_code protocol description](./protocol/#get-tokens-id-access-by-code).

**Example**

``` {.code}
GetTokenByCode:

my $oxd_id = $cgi->escapeHTML($session->param('oxd_id'));
my $code = $cgi->escapeHTML($cgi->param("code"));
my $state = $cgi->escapeHTML($cgi->param("state"));

$get_tokens_by_code = new GetTokenByCode();
$get_tokens_by_code->setRequestOxdId($oxd_id);
$get_tokens_by_code->setRequestCode($code);
$get_tokens_by_code->setRequestState($state);
$get_tokens_by_code->request();
#store values in sessions
$session->param('user_oxd_id_token', $get_tokens_by_code->getResponseIdToken());
$session->param('state', $state);
$session->param('session_state', $cgi->escapeHTML($cgi->param("session_state")));

print Dumper( $get_user_info->getResponseObject() );        

```

#### GetUserInfo.pm

- [Get_user_info protocol description](./protocol/#get-user-info).

**Example**

``` {.code}
GetUserInfo:

my $oxd_id = $cgi->escapeHTML($session->param('oxd_id'));

$get_user_info = new GetUserInfo();
$get_user_info->setRequestOxdId($oxd_id);
$get_user_info->setRequestAccessToken($get_tokens_by_code->getResponseAccessToken());
$get_user_info->request();

print Dumper( $get_user_info->getResponseObject() );
                        
```



#### OxdLogout.pm

- [Get_logout_uri protocol description](./protocol/#log-out-uri).

**Example**

``` {.code}
OxdLogout:

my $oxd_id = $cgi->escapeHTML($session->param('oxd_id'));
my $user_oxd_id_token = $cgi->escapeHTML($session->param("user_oxd_id_token"));
my $session_state = $cgi->escapeHTML($session->param("session_state"));
my $state = $cgi->escapeHTML($session->param("state"));

$logout = new OxdLogout();
$logout->setRequestOxdId($oxd_id);
$logout->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$logout->setRequestIdToken($user_oxd_id_token);
$logout->setRequestSessionState($session_state);
$logout->setRequestState($state);
$logout->request();

$session->delete();
$logoutUrl = $logout->getResponseObject()->{data}->{uri};
                        
```

####For UMA authentications open this url in browser
- [https://oxd-perl-example.com/uma.cgi](https://oxd-perl-example.com/uma.cgi).

####UmaRsProtect.pm

- [Uma_rs_protect protocol description](./protocol/#uma-protect-resources).


**Example**

``` {.code}
UmaRsProtect:

my $oxdId = $session->param('oxd_id');

$uma_rs_protect = new UmaRsProtect();
$uma_rs_protect->setRequestOxdId($oxdId);

$uma_rs_protect->addConditionForPath(["GET"],["https://photoz.example.com/dev/actions/view"], ["https://photoz.example.com/dev/actions/view"]);
$uma_rs_protect->addConditionForPath(["POST"],[ "https://photoz.example.com/dev/actions/add"],[ "https://photoz.example.com/dev/actions/add"]);
$uma_rs_protect->addConditionForPath(["DELETE"],["https://photoz.example.com/dev/actions/remove"], ["https://photoz.example.com/dev/actions/remove"]);
$uma_rs_protect->addResource('/photo');

$uma_rs_protect->request();
print Dumper( $uma_rs_protect->getResponseObject() );


```

####UmaRsCheckAccess.pm

- [Uma_rs_check_access protocol description](./protocol/#uma-check-access).

**Example**

``` {.code}
UmaRsCheckAccess:

my $oxdId = $session->param('oxd_id');
my $umaRpt = $session->param('uma_rpt');

$uma_rs_authorize_rpt = new UmaRsCheckAccess();
$uma_rs_authorize_rpt->setRequestOxdId($oxdId);
$uma_rs_authorize_rpt->setRequestRpt($umaRpt);
$uma_rs_authorize_rpt->setRequestPath("/photo");
$uma_rs_authorize_rpt->setRequestHttpMethod("GET");
$uma_rs_authorize_rpt->request();

print Dumper($uma_rs_authorize_rpt->getResponseObject());
my $uma_ticket= $uma_rs_authorize_rpt->getResponseTicket();
$session->param('uma_ticket', $uma_ticket);

```

####UmaRpGetRpt.pm

- [Uma_rp_get_rpt protocol description](./protocol/#uma-rp-get-rpt).

**Example**

``` {.code}
UmaRpGetRpt:

my $oxdId = $session->param('oxd_id');


$uma_rp_get_rpt = new UmaRpGetRpt();
$uma_rp_get_rpt->setRequestOxdId($oxdId);
$uma_rp_get_rpt->request();

print Dumper($uma_rp_get_rpt->getResponseObject());

my $uma_rpt= $uma_rp_get_rpt->getResponseRpt();
$session->param('uma_rpt', $uma_rpt);

```

#### UmaRpAuthorizeRpt.pm

- [Uma_rp_authorize_rpt protocol description](./protocol/#uma-rp-authorize-rpt).

**Example**

``` {.code}
UmaRpAuthorizeRpt:

my $oxdId = $session->param('oxd_id');
my $uma_rpt = $session->param('uma_rpt');
my $uma_ticket = $session->param('uma_ticket');

$uma_rp_authorize_rpt = new UmaRpAuthorizeRpt();
$uma_rp_authorize_rpt->setRequestOxdId($oxdId);
$uma_rp_authorize_rpt->setRequestRpt($uma_rpt);
$uma_rp_authorize_rpt->setRequestTicket($uma_ticket);
$uma_rp_authorize_rpt->request();

print Dumper($uma_rp_authorize_rpt->getResponseObject());
                        
```

####UmaRpGetGat.pm

- [Uma_rp_get_gat protocol description](./protocol/#gluu-oauth2-access-management-apis).

**Example**

``` {.code}
UmaRpGetGat:

my $oxdId = $session->param('oxd_id');

$uma_rp_get_gat = new UmaRpGetGat();
$uma_rp_get_gat->setRequestOxdId($oxdId);
$uma_rp_get_gat->setRequestScopes(["https://photoz.example.com/dev/actions/add","https://photoz.example.com/dev/actions/view", "https://photoz.example.com/dev/actions/edit"]);
$uma_rp_get_gat->request();

print Dumper( $uma_rp_get_gat->getResponseObject() );

my $uma_gat= $uma_rp_get_gat->getResponseGat();
$session->param('uma_gat', $uma_gat);
print Dumper( $uma_rp_get_gat->getResponseGat() );
                        
```
