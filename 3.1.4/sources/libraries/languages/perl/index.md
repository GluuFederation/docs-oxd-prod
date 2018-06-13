# Perl

## Installation

### Prerequisites

- Perl 5
- Apache 2.4.4 +
- CGI::Session module
- Net::SSLeay module
- IO::Socket::SSL module
- Gluu oxd server - [Installation docs](https://gluu.org/docs/oxd/install/)

### Library

- _Install from CPAN_ - `CPAN> install GLUU/oxdperl-0.04.tar.gz`
- _Source from Github_ -   [Download](https://github.com/GluuFederation/oxd-perl/archive/3.1.3.zip) the zip of the oxd Perl library

### Important Links

- [oxd docs](https://gluu.org/docs/oxd)
- oxd-perl [API docs](https://rawgit.com/GluuFederation/oxd-perl/3.1.3/docs/html/index.html) for the auto-generated perl docs, which includes more in-depth information about the various functions and parameters
- See the code of a [sample perl app](https://github.com/GluuFederation/oxd-perl/tree/3.1.3/OxdPerlModule/example) built using oxd-perl
- Browse the oxd-perl [source code on Github](https://github.com/GluuFederation/oxd-perl)



## Configuration

oxd-perl uses a configuration file to specify information needed to configure the OpenID Connect client. If OpenID dynamic client registration is used, the config file needs to be *writable by the app*, because oxd will save the client id and client secret to this file.

oxd-perl can communicate with the oxd server via sockets or HTTPS.

Below are minimal configuration examples for sockets and https transport. The [oxd-settings.json](https://github.com/GluuFederation/oxd-perl/blob/3.1.3/OxdPerlModule/example/oxd-settings.json) file contains a full list of configuration parameters and sample values. 

!!! Note
    The client hostname should be a valid hostname (FQDN), not a localhost or an IP Address

**Configuration for oxd-server via sockets:**

```ini
  "connection_type": "local",
  "oxd_host_port": 8099
```

**Configuration for oxd-https-extension:**

```ini
   "connection_type": "web",
   "rest_service_url": "https://127.0.0.1:8443"
```


## Sample Code

**`index.cgi`**

Use the following code to assign the required parameter values from `oxd-settings.json` to the Perl client application. These values can be used as parameters for the oxd-server methods.

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

### Set Up Client

```perl
my $setup_client = new OxdSetupClient( );
	
$setup_client->setRequestOpHost($opHost);
$setup_client->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
$setup_client->setRequestGrantTypes($grantType);
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

### Introspect Access Token

```perl
my $introspect_access_token = new IntrospectAccessToken( );

$introspect_access_token->setRequestOxdId($oxd_id);
$introspect_access_token->setRequestAccessToken($access_token);
$introspect_access_token->request();

print Dumper($introspect_access_token->getResponseObject());
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

```perl
my $register_site = new OxdRegister( );
		
$register_site->setRequestOpHost($opHost);
$register_site->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
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

### Update Site

```perl
my $update_site_registration = new UpdateRegistration();
			
$update_site_registration->setRequestOxdId($oxd_id);
$update_site_registration->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$update_site_registration->setRequestContacts([$contacts]);
$update_site_registration->setRequestProtectionAccessToken($protection_access_token);
$update_site_registration->request();

print Dumper($update_site_registration->getResponseObject());
```

**Response:**

```javascript
{
    "status":"ok"
}
```

### Remove Site

```perl
my $oxd_remove = new OxdRemove();
 
$oxd_remove->setRequestOxdId($oxd_id);
$oxd_remove->setRequestProtectionAccessToken($protection_access_token);
$oxd_remove->request();

print Dumper($oxd_remove->getResponseObject());
```

**Response:**

```javascript
{
    "status":"ok",
    "data": {
        "oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6"
    }
}
```

### Get Authorization URL

```perl
my $get_authorization_url = new GetAuthorizationUrl( );
%customParams = ('param1' => 'value1', 'param2' => 'value2');
$get_authorization_url->setRequestOxdId($oxd_id);
$get_authorization_url->setRequestCustomParam(\%customParams);
$get_authorization_url->setRequestProtectionAccessToken($protection_access_token);
$get_authorization_url->request();
 
my $authurl = $get_authorization_url->getResponseAuthorizationUrl();
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

```perl
my $code = $cgi->escapeHTML($cgi->param("code"));
my $state = $cgi->escapeHTML($cgi->param("state"));

my $get_tokens_by_code = new GetTokenByCode();

$get_tokens_by_code->setRequestOxdId($oxd_id);
$get_tokens_by_code->setRequestCode($code);
$get_tokens_by_code->setRequestState($state);
$get_tokens_by_code->setRequestProtectionAccessToken($protection_access_token);
$get_tokens_by_code->request();

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

### Get Access Token by Refresh Token

```perl
my $get_access_token_by_refresh_token = new GetAccessTokenByRefreshToken();

$get_access_token_by_refresh_token->setRequestOxdId($oxd_id);
$get_access_token_by_refresh_token->setRequestRefreshToken($refresh_token);
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

### Get User Info

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

### Get Logout URI

```perl
my $logout = new OxdLogout();

$logout->setRequestOxdId($oxd_id);
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

### UMA RS Protect

```perl
my $uma_rs_protect = new UmaRsProtect();

$uma_rs_protect->setRequestOxdId($oxd_id);
$uma_rs_protect->setOverwrite(true);
$uma_rs_protect->addConditionForPath(["GET"],["https://photoz.example.com/dev/actions/view"], ["https://photoz.example.com/dev/actions/view"]);
$uma_rs_protect->addConditionForPath(["POST"],[ "https://photoz.example.com/dev/actions/add"],[ "https://photoz.example.com/dev/actions/add"]);
$uma_rs_protect->addConditionForPath(["DELETE"],["https://photoz.example.com/dev/actions/remove"], ["https://photoz.example.com/dev/actions/remove"]);
$uma_rs_protect->addResource('/photo');
$uma_rs_protect->setRequestProtectionAccessToken($protection_access_token);
$uma_rs_protect->request();

print Dumper( $uma_rs_protect->getResponseObject() );
```

**RS Protect with scope_expression**

```perl
my $uma_rs_protect = new UmaRsProtect();
$uma_rs_protect->setRequestOxdId($oxdId);
$uma_rs_protect->setOverwrite(true);

%rule = ('and' => [{'or' => [{'var' => 0},{'var' => 1}]},{'var' => 2}]);
$data = ["http://photoz.example.com/dev/actions/all", "http://photoz.example.com/dev/actions/add", "http://photoz.example.com/dev/actions/internalClient"];

$uma_rs_protect->addConditionForPath(["GET"],["https://client.example.com:44300/api"], ["https://client.example.com:44300/api"], $uma_rs_protect->getScopeExpression(\%rule, $data));
$uma_rs_protect->addResource('/values');
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

### UMA RS Check Access 

```perl
my $uma_rs_check_access = new UmaRsCheckAccess();

$uma_rs_check_access->setRequestOxdId($oxd_id);
$uma_rs_check_access->setRequestRpt($rpt);
$uma_rs_check_access->setRequestPath($path);
$uma_rs_check_access->setRequestHttpMethod($http_method);
$uma_rs_check_access->setRequestProtectionAccessToken($protection_access_token);
$uma_rs_check_access->request();

my $uma_ticket= $uma_rs_check_access->getResponseTicket();
print Dumper($uma_rs_check_access->getResponseObject());
```

**_Access Granted Response:_**

```javascript
{
    "status":"ok",
    "data":{
        "access":"granted"
    }
}
```

**_Access Denied with Ticket Response:_**

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

**_Access Denied without Ticket Response:_**

```javascript
{
    "status":"ok",
    "data":{
        "access":"denied"
    }
}
```

**_Resource is not Protected:_**

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

```perl
my $introspect_rpt = new UmaIntrospectRpt();
$introspect_rpt->setRequestOxdId($oxdId);
$introspect_rpt->setRequestRPT($rpt);
$introspect_rpt->request();

print Dumper($introspect_rpt->getResponseObject());
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

```perl
my $uma_rp_get_rpt = new UmaRpGetRpt();
$uma_rp_get_rpt->setRequestOxdId($oxd_id);
$uma_rp_get_rpt->setRequestTicket($ticket);
$uma_rp_get_rpt->setRequestProtectionAccessToken($protection_access_token);
$uma_rp_get_rpt->request();

my $uma_rpt= $uma_rp_get_rpt->getResponseRpt();
print Dumper($uma_rp_get_rpt->getResponseObject());
```

**_Success Response:_**

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

**_Needs Information Error Response:_**

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

**_Invalid Ticket Error Response:_**

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

```perl
my $uma_rp_get_claims_gathering_url = new UmaRpGetClaimsGatheringUrl();
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

## Example

- The `OxdPerlModule/example` directory contains apps and scripts written using oxd-perl for OpenID Connect
- The `OxdPerlModule/UMAExample` directory contains the app and scripts written using oxd-perl for UMA AS.

## Support
Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.
