# oxd Php

##Overview
The following documentation demonstrates 
how to use Gluu's commercial OAuth 2.0 client software,
 [oxd](http://oxd.gluu.org), to send users from a Php app to an 
 OpenID Connect Provider (OP) for login. You can securely send users 
 to any standard OP for login, including Google and 
 the [free open source Gluu Server](http://gluu.org/gluu-server).


## Installation

### Source

oxd-php-library source is available on Github:

- [Github sources](https://github.com/GluuFederation/oxd-php-library)

### Composer: oxd-php-api

- [Compose API source](https://github.com/GluuFederation/oxdphpapi)
- [Library version 3.0.1](https://github.com/GluuFederation/oxd-php-api/releases/tag/v3.0.1)

This is the preferred method. See the [composer](https://getcomposer.org) 
website for 
[installation instructions](https://getcomposer.org/doc/00-intro.md) if 
you do not already have it installed. 

To install oxd-php-api via Composer, execute the following command 
in your project root:

```
$ composer install `composer require "gluufederation/oxd-php-api": "3.0.1"

```

!!!**Note**: 
    OpenID Connect requires *https.* This library will not 
work if your website uses *http* only.

## Configuration 

The oxd-php-library configuration file is located in 
'oxd-rp-settings.json'. The values here are used during 
registration. For a full list of supported
oxd configuration parameters, see the 
[oxd documentation](./protocol/#register-site)
Below is a typical configuration data set for registration:

``` {.code }
{
    "oxd_host_port":8099,
    "authorization_redirect_uri" : ["https://www.myapplication.com/welcome" ],
    "post_logout_redirect_uri" : "https://www.myapplication.com/logout",
    "scope" : ["openid", "profile"],
    "acr_values" : ["u2f"]
}
                        
```

-   oxd_host_port - oxd port or socket


## Sample code


### Register\_site.php 

- [Register_site protocol description](./protocol/#register-site).

**Example**

``` {.code}
Register_site_test:

session_start();
session_destroy();
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

### Update\_site\_registration.php 

- [Update_site_registration protocol description](./protocol/#update-site-registration).

**Example**

``` {.code}
Update_site_registration_test:

session_start();
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

### Get\_authorization\_url.php 

- [Get_authorization_url protocol description](./protocol/#get-authorization-url).

**Example**

``` {.code}
Get_authorization_url_test:
session_start();
require_once '../Get_authorization_url.php';

$get_authorization_url = new Get_authorization_url();
$get_authorization_url->setRequestOxdId($_SESSION['oxd_id']);
$get_authorization_url->setRequestAcrValues(Oxd_RP_config::$acr_values);
$get_authorization_url->setRequestScope(Oxd_RP_config::$scope);
$get_authorization_url->request();
echo $get_authorization_url->getResponseAuthorizationUrl();
                        
```

### Get\_tokens\_by\_code.php

- [Get_tokens_by_code protocol description](./protocol/#get-tokens-id-access-by-code).

**Example**

``` {.code}
Get_tokens_by_code_test:
session_start();
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

### Get\_user\_info.php

- [Get_user_info protocol description](./protocol/#get-user-info).

**Example**

``` {.code}
Get_user_info_test:

session_start();
require_once '../Get_user_info.php';
echo '<br/>Get_user_info <br/>';
$get_user_info = new Get_user_info();
$get_user_info->setRequestOxdId($_SESSION['oxd_id']);
$get_user_info->setRequestAccessToken($_SESSION['access_token']);
$get_user_info->request();
print_r($get_user_info->getResponseObject());
                        
```

### Logout.php

- [Get_logout_uri protocol description](./protocol/#log-out-uri).

**Example**

``` {.code}
Logout_test:
session_start();
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

### Uma\_rs\_protect.php

- [Uma_rs_protect protocol description](./protocol/#uma-protect-resources).

**Example**

``` {.code}
Uma_rs_protect_test:

$uma_rs_protect = new Uma_rs_protect();
$uma_rs_protect->setRequestOxdId($register_site->getResponseOxdId());

$uma_rs_protect->addConditionForPath(["GET"],["http://vlad.umatest.com/dev/actions/view"], ["http://vlad.umatest.com/dev/actions/view"]);
$uma_rs_protect->addConditionForPath(["POST"],[ "http://vlad.umatest.com/dev/actions/add"],[ "http://vlad.umatest.com/dev/actions/add"]);
$uma_rs_protect->addConditionForPath(["DELETE"],["http://vlad.umatest.com/dev/actions/remove"], ["http://vlad.umatest.com/dev/actions/remove"]);
$uma_rs_protect->addResource('/uma/testresource');

$uma_rs_protect->request();
var_dump($uma_rs_protect->getResponseObject());

```

### Uma\_rs\_check\_access.php

- [Uma_rs_check_access protocol description](./protocol/#uma-check-access).

**Example**

``` {.code}
Uma_rs_check_access_test:

session_start();
require_once '../Uma_rs_check_access.php';

$uma_rs_authorize_rpt = new Uma_rs_check_access();
$uma_rs_authorize_rpt->setRequestOxdId($_SESSION['oxd_id']);
$uma_rs_authorize_rpt->setRequestRpt($_SESSION['uma_rpt']);
$uma_rs_authorize_rpt->setRequestPath("/uma/testresource");
$uma_rs_authorize_rpt->setRequestHttpMethod("GET");
$uma_rs_authorize_rpt->request();

var_dump($uma_rs_authorize_rpt->getResponseObject());

$_SESSION['uma_ticket'] = $uma_rs_authorize_rpt->getResponseTicket();

```

### Uma\_rp\_get\_rpt.php

- [Uma_rp_get_rpt protocol description](./protocol/#uma-rp-get-rpt).

**Example**

``` {.code}
Uma_rp_get_rpt_test:

$uma_rp_get_rpt = new Uma_rp_get_rpt();
$uma_rp_get_rpt->0setRequestOxdId($_SESSION['oxd_id']);
$uma_rp_get_rpt->request();

var_dump($uma_rp_get_rpt->getResponseObject());

$_SESSION['uma_rpt']= $uma_rp_get_rpt->getResponseRpt();
echo $uma_rp_get_rpt->getResponseRpt();

```

### Uma\_rp\_authorize\_rpt.php

- [Uma_rp_authorize_rpt protocol description](./protocol/#uma-rp-authorize-rpt).

**Example**

``` {.code}
Uma_rp_authorize_rpt_test:

session_start();
require_once '../Uma_rp_authorize_rpt.php';

$uma_rp_authorize_rpt = new Uma_rp_authorize_rpt();
$uma_rp_authorize_rpt->setRequestOxdId($_SESSION['oxd_id']);
$uma_rp_authorize_rpt->setRequestRpt($_SESSION['uma_rpt']);
$uma_rp_authorize_rpt->setRequestTicket($_SESSION['uma_ticket']);
$uma_rp_authorize_rpt->request();

var_dump($uma_rp_authorize_rpt->getResponseObject());
                        
```

### Uma\_rp\_get\_gat.php

- [Uma_rp_get_gat protocol description](./protocol/#gluu-oauth2-access-management-apis).

**Example**

``` {.code}
Uma_rp_get_gat_test:

$uma_rp_get_gat = new Uma_rp_get_gat();
$uma_rp_get_gat->setRequestOxdId($_SESSION['oxd_id']);
$uma_rp_get_gat->setRequestScopes(["http://photoz.example.com/dev/actions/add","http://photoz.example.com/dev/actions/view", "http://photoz.example.com/dev/actions/edit"]);
$uma_rp_get_gat->request();

var_dump($uma_rp_get_gat->getResponseObject());

$_SESSION['uma_gat']= $uma_rp_get_gat->getResponseGat();
echo $uma_rp_get_gat->getResponseGat();
                        
```


## Sample App

[View the sample app](https://github.com/GluuFederation/oxd-php-library/tree/master/client.example.com)
