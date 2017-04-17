# Configuration

oxd configuration consists of two files :

* `conf/oxd-conf.json` - general configuration
* `conf/oxd-default-site-config.json` - fallback configuration for "Register site" command, see details on 
[Protocol page](../protocol/)

## oxd-conf.json

The contents of the configuration file is as follows:

```
oxd-conf.json
{
    "port":8099,
    "localhost_only":true,
    "time_out_in_seconds":0,
    "use_client_authentication_for_pat":true,
    "use_client_authentication_for_aat":true,
    "trust_all_certs":true,
    "trust_store_path":"",
    "trust_store_password":"",
    "license_id":"",
    "public_key":"",
    "public_password":"",
    "license_password":""
}
```

* port - oxD socket port
* localhost_only - flag to restrict communication
* time_out_in_seconds - time out for oxd socket in seconds. oxd closes sockets automatically after this time out (stops listen commands). Zero means listen indefinitely.
* use_client_authentication_for_pat - true if client authentication is required, if false than user authentication is performed which require user_id and user_secret specified during register_site command.
* use_client_authentication_for_aat - true if client authentication is required, if false than user authentication is performed which require user_id and user_secret specified during register_site command.
* trust_all_certs - true to trust all certificates, if false then trust_store_path must be specified to store with valid certificates
* trust_store_path - Path to Java .jks trust store to be used for an SSL connection.
* trust_store_password - password of trust store
* license_id - Will be supplied when you order a license.
* public_key - Will be supplied when you order a license. It's very big--make sure you it's one line with no spaces (if your mail client added line breaks).
* public_password - Will be supplied when you order a license.
* license_password - Will be supplied when you order a license.

## oxd-default-site-config.json

```json
conf/oxd-default-site-config.json
{
    "op_host":"",
    "authorization_redirect_uri":"",
    "post_logout_redirect_uri":"",
    "response_types":["code"],
    "grant_type":["authorization_code"],
    "acr_values":["basic"],
    "scope":["openid", "profile"],
    "ui_locales":["en"],
    "claims_locales":["en"],
    "client_jwks_uri":"",
    "contacts":[]
}
```

* op_host - must point to a valid 
[Gluu Server CE installation](https://gluu.org/docs/ce/3.0.1/installation-guide/install/). (Sample : "op_host":"https://idp.example.org")
* authorization_redirect_uri - URL that the OpenID Connect Provider (OP) will redirect the person to after  successful authentication
* post_logout_redirect_uri - URL to which the RP is requesting that the End-User's User Agent be redirected after a logout has been performed
* response_types - JSON array containing a list of the OAuth 2.0 response_type values that the site is declaring that it will restrict itself to using
* grant_type - JSON array containing a list of the OAuth 2.0 Grant Types that the Client is declaring that it will restrict itself to using
* acr_values - specified authentication method (basic, duo, u2f)
* scope - JSON array containing a list of the scopes that the Client is declaring that it will restrict itself to using
* ui_locales - End-User's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
* claims_locales - End-User's preferred languages and scripts for Claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
* contacts - array of e-mail addresses of people responsible for this Client
