# OpenID Connect & UMA Protocol Overview

The oxd server supports the OpenID Connect and UMA 2 profiles of OAuth 2.0. OpenID Connect can be used to send a user for authentication and gather identity information about the user. UMA 2 can be used to manage what digital resources the user should have access to.

## OpenID Connect Authentication

OpenID Connect is a simple identity layer on top of the OAuth 2.0 protocol. OpenID Connect is one of the most popular API's for an application to identify a person. Technically it is not an authentication protocol--
it enables a person to authorize the release of information to 
an application from a remote "identity provider". In the
process of authorizing this release of information, the person is authenticated (if 
no previous session exists). If you are familiar with Google 
authentication, you've used OpenID Connect. 

!!! Note
    If you need an OpenID Connect Provider you can [deploy the Gluu Server](https://gluu.org/docs/2.4.4/installation-guide/install/). 
    The Gluu Server will enable your organization to centralize authentication and authorization decisions to enable Single Sign-On (SSO) and strong authentication for many applications. 

### Authentication Flow
oxd uses the [Authorization Code Flow](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) 
for authentication. Future versions of oxd may support the Hybrid Flow. 
Implicit Flow is not supported because it is intended for Javascript client-side 
applications where the client does not authenticate.

Learn more about authentication flows in the 
[OpenID Connect spec](http://openid.net/specs/openid-connect-core-1_0.html). 

### oxd OpenID Connect APIs
`oxd-server` provides seven API's for OpenID Connect authentication. In general,
you can think of the Authorization Code Flow as a three step process: 

 - Redirect person to the authorization URL and obtain a code
 - Use code to obtain tokens (access, id_token, refresh)
 - Use access token to obtain user claims

The other four oxd API's are:
 
 - Register site (called once--the first time your application uses oxd)
 - Update site registration (not used often)
 - Logout
 - Get access token by refresh token
 
**IMPORTANT** : 

In `oxd-https-extension` case before using above workflow it is required to obtain access token to secure interaction between client application and `oxd-https-extension`. 

 - Setup client (returns `client_id` and `client_secret`)
 - Get client token (pass `client_id` and `client_secret` to obtain `access_token`)
 
 Pass obtained access token as `protection_access_token` to all further calls to `oxd-https-extension`.

#### Register site

First of all, the web site must register itself with oxd server. If 
registration is successful, ox will return an identifier for the 
application, which must be presented in subsequent API calls. This
is the `oxd_id`, not to be confused with the OpenID Connect client id.

During the registration operation, oxd will dynamically register an 
OpenID Connect client and save its configuration.

All parameters to `register_site` are optional except the 
`authorization_redirect_uri`. This is the URL on your website that the 
OpenID Connect Provider (OP) will redirect the person to after 
successful authorization.

`register_site` has many parameters, but you can ignore most of them!
Default configuration values are taken from
[conf/oxd-default-site-config.json](../conf/).
Even most of these options may be blank, with one exception: if the 
`op_host` is missing from the `register_site` command parameters, 
it must be present in this file--we need to know which OpenID Provider
will be used! 

The `register_site` command returns `oxd_id`. Several applications may 
share an instance of oxd, and this identifier is used by oxd to 
distinguish differences in configuration between them.

`op_host` must point to a valid OpenID Connect Provider that supports 
[client registration](http://openid.net/specs/openid-connect-registration-1_0.html), 
for example, a [Gluu Server CE installation](https://gluu.org/docs/2.4.4/installation-guide/install/). 
Sample: `"op_host":"https://idp.example.org"`

Request:

```json
{
    "command":"register_site",
    "params": {
        "authorization_redirect_uri": "https://client.example.org/cb", <- REQUIRED
        "op_host":"https://<ophostname>"                               <- OPTIONAL (But if missing, must be present in defaults)
        "post_logout_redirect_uri": "https://client.example.org/cb",   <- OPTIONAL 
        "application_type": "web",                                     <- OPTIONAL
        "response_types": ["code"],                                    <- OPTIONAL
        "grant_types": ["authorization_code"],                         <- OPTIONAL 
        "scope": ["openid"],                                           <- OPTIONAL
        "acr_values": ["basic"],                                       <- OPTIONAL
        "client_name": "",                                             <- OPTIONAL (But if missing, oxd will generate its own non-human readable name)
        "client_jwks_uri": "",                                         <- OPTIONAL
        "client_token_endpoint_auth_method": "",                       <- OPTIONAL
        "client_request_uris": [],                                     <- OPTIONAL
        "client_logout_uris": [],                                      <- OPTIONAL
        "client_sector_identifier_uri": [],                            <- OPTIONAL
        "contacts": ["foo_bar@spam.org"],                              <- OPTIONAL
        "ui_locales": [],                                              <- OPTIONAL
        "claims_locales": [],                                          <- OPTIONAL
        "client_id": "<client id of existing client>",                 <- OPTIONAL ignores all other parameters and skips new client registration forcing to use existing client (client_secret is required if this parameter is set)
        "client_secret": "<client secret of existing client>",         <- OPTIONAL must be used together with client_secret.
        "protection_access_token":"<access token of the client>"       <- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter        
    }
}
```

Response:

```json
{
    "status":"ok",
    "data":{
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF"
    }
}
```

#### Update site registration

API used to update a current registration.

Request:

```json
{
    "command":"update_site_registration",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",              <- REQUIRED
        "authorization_redirect_uri": "https://client.example.org/cb",<- OPTIONAL 
        "post_logout_redirect_uri": "https://client.example.org/cb",  <- OPTIONAL 
        "client_logout_uris":["https://client.example.org/logout"],   <- OPTIONAL
        "response_type":["code"],                                     <- OPTIONAL
        "grant_types":[],                                             <- OPTIONAL
        "scope": ["opeind", "profile"],                               <- OPTIONAL
        "acr_values": ["duo"],                                        <- OPTIONAL
        "client_name": "",                                            <- OPTIONAL
        "client_secret_expires_at":1335205592410,                     <- OPTIONAL can be used to extends client lifetime (milliseconds since 1970)
        "client_jwks_uri": "",                                        <- OPTIONAL
        "client_token_endpoint_auth_method": "",                      <- OPTIONAL
        "client_request_uris":[],                                     <- OPTIONAL
        "client_logout_uris":[],                                      <- OPTIONAL
        "client_sector_identifier_uri":"",                            <- OPTIONAL
        "contacts":["foo_bar@spam.org"],                              <- OPTIONAL
        "ui_locales":[],                                              <- OPTIONAL
        "claims_locales":[],                                          <- OPTIONAL
        "protection_access_token":"<access token of the client>"      <- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Response:

```json
{
    "status":"ok"
}
```


#### Get authorization url

Returns the URL at the OpenID Provider (OP) to which your application 
must redirect the person to authorize the release of personal data (and
perhaps be authenticated in the process if no previous session exists).
The Response from the OP will include the code and state 
values, which should be used to subsequently obtain tokens.

Request:

```json
{
    "command":"get_authorization_url",
    "params": {
        "oxd_id": "6F9619FF-8B86-D011-B42D-00CF4FC964FF", <- required, obtained after registration
        "scope": ["openid"],                              <- optional, may be skipped (by default takes scopes that was registered during register_site command)
        "acr_values": ["duo"],                            <- optional, may be skipped (default is basic)
        "prompt": "login",                                <- optional, skipped if no value specified or missed. prompt=login is required if you want to force alter current user session (in case user is already logged in from site1 and site2 construsts authorization request and want to force alter current user session)
        "custom_parameters": {                            <- optional, custom parameters
            "param1":"value1",
            "param2":"value2"
        },
        "protection_access_token":"<access token of the client>" <- optional for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Response:

```json
{
    "status":"ok",
    "data":{
        "authorization_url":"  https://server.example.com/authorize?response_type=code
    &client_id=s6BhdRkqt3
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
    &scope=openid%20profile
    &acr_values=duo
    &state=af0ifjsldkj
    &nonce=n-0S6_WzA2Mj"
    }
}
```

After redirecting to the above URL, the OpenID Provider will return a 
response that looks like this to the URL your application registered as 
the redirect URI (parse out the code and state):

```
HTTP/1.1 302 Found
Location: https://client.example.org/cb?code=SplxlOBeZQQYbYS6WxSbIA&state=af0ifjsldkj&scopes=openid%20profile
```

#### Get Tokens (ID & Access) by Code

Use the code and state obtained in the previous step to call this API to retrieve tokens.

Request:

```json
{
    "command":"get_tokens_by_code",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",          <- Required
        "code":"I6IjIifX0",                                       <- Required, code from OP redirect url (see example above)
        "state":"af0ifjsldkj",                                    <- Required
        "protection_access_token":"<access token of the client>"  <- Optional for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Response:

```
{
    "status":"ok",
    "data":{
        "access_token":"SlAV32hkKG",
        "expires_in":3600,
        "refresh_token":"aaAV32hkKG1"
        "id_token":"eyJ0 ... NiJ9.eyJ1c ... I6IjIifX0.DeWt4Qu ... ZXso",
        "id_token_claims": {
             "iss": "https://server.example.com",
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

#### Get User Info

Use the access token from the step above to retrieve a JSON object 
with the user claims.

Request:

```json
{
    "command":"get_user_info",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",               <- REQUIRED
        "access_token":"SlAV32hkKG",                                   <- REQUIRED
        "protection_access_token":"<access token of the client>"       <- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Response:

```json
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

#### Log out URI

Uses front channel logout--a page is returned with iFrames, each of 
which contains the logout URL of the applications that have a session 
in that browser. These iFrames should be loaded automatically--enabling 
each application to get a notification of logout, and to hopefully clean 
up any cookies in the person's browser. If the person blocks 
[third-party cookies](https://en.wikipedia.org/wiki/HTTP_cookie#Third-party_cookie)
in their browser, logout will not work.

Request:

```json
{
    "command":"get_logout_uri",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",
        "id_token_hint": "eyJ0 ... NiJ9.eyJ1c ... I6IjIifX0.DeWt4Qu ... ZXso",<- OPTIONAL (oxd server will use last used ID Token)
        "post_logout_redirect_uri": "<post logout redirect uri here>",        <- OPTIONAL
        "state": "<site state>",                                              <- OPTIONAL
        "session_state": "<session state>",                                   <- OPTIONAL
        "protection_access_token":"<access token of the client>"              <- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Response:

```json
{
    "status":"ok",
    "data":{
        "uri":"https://<server>/end_session?id_token_hint=<id token>&state=<state>&post_logout_redirect_uri=<...>"
    }
}
```

#### Setup Client

It is required to setup client if `oxd-https-extension` is used. For `oxd-server` it is not required.

Parameters for Setup Client are the same as for Register Site command. The command registers client for communication protection (the one that has to be used to obtain access token (via Get Client Token command) for further passing that access token as `protection_access_token` parameter to other commands.

Request:

```json
{
    "command":"setup_client",
    "params": {
        "authorization_redirect_uri": "https://client.example.org/cb", <- REQUIRED
        "op_host":"https://<ophostname>"                               <- OPTIONAL (But if missing, must be present in defaults)
        "post_logout_redirect_uri": "https://client.example.org/cb",   <- OPTIONAL 
        "application_type": "web",                                     <- OPTIONAL
        "response_types": ["code"],                                    <- OPTIONAL
        "grant_types": ["authorization_code"],                         <- OPTIONAL 
        "scope": ["openid"],                                           <- OPTIONAL
        "acr_values": ["basic"],                                       <- OPTIONAL
        "client_name": "",                                             <- OPTIONAL (But if missing, oxd will generate its own non-human readable name)
        "client_jwks_uri": "",                                         <- OPTIONAL
        "client_token_endpoint_auth_method": "",                       <- OPTIONAL
        "client_request_uris": [],                                     <- OPTIONAL
        "client_logout_uris": [],                                      <- OPTIONAL
        "client_sector_identifier_uri": [],                            <- OPTIONAL
        "contacts": ["foo_bar@spam.org"],                              <- OPTIONAL
        "ui_locales": [],                                              <- OPTIONAL
        "claims_locales": [],                                          <- OPTIONAL
        "client_id": "<client id of existing client>",                 <- OPTIONAL ignores all other parameters and skips new client registration forcing to use existing client (client_secret is required if this parameter is set)
        "client_secret": "<client secret of existing client>",         <- OPTIONAL must be used together with client_secret.
        "protection_access_token":"<access token of the client>"       <- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter        
    }
}
```

Response:

```json
{
    "status":"ok",
    "data":{
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",
        "op_host": "<op host>",
        "client_id":"<client id>"
        "client_secret":"<client secret>"
        "client_registration_access_token":"<Client registration access token>"
        "client_registration_client_uri":"<URI of client registration>"
        "client_id_issued_at":"<client_id issued at>"
        "client_secret_expires_at":"<client_secret expires at>"
    }
}
```

#### Get Client Token

Request:

```json
{
    "command":"get_client_token",
    "params": {
        "client_id": "<client id>",            <- REQUIRED
        "client_secret": "<client secret>",    <- REQUIRED
        "op_host":"https://<ophostname>"       <- REQUIRED
        "op_discovery_path":""                 <- OPTIONAL 
        "scope":[]                             <- OPTIONAL 
    }
}
```

Response:

```json
{
    "status":"ok",
    "data":{
        "access_token":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",
        "expires_in": 399,
        "refresh_token": "fr459f",
        "scope": "openid"
    }
}
```

#### Get Access Token by Refresh Token

Gets Access Token by Refresh Token.

Request:

```json
{
    "command":"get_access_token_by_refresh_token",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",          <- Required
        "refresh_token":"I6IjIifX0",                              <- Required, refresh_token from get_tokens_by_code command
        "scope":["openid","profile"],                             <- Optional. If not specified should grant access with scope provided in previous request
        "protection_access_token":"<access token of the client>"  <- Optional for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Response:

```
{
    "status":"ok",
    "data":{
        "access_token":"SlAV32hkKG",
        "expires_in":3600,
        "refresh_token":"aaAV32hkKG1"
    }
}
```

## UMA 2 Authorization 
UMA 2 is a profile of OAuth 2.0 that defines RESTful, JSON-based, standardized flows and constructs for coordinating the protection of any API or web resource. oxd makes it easy to secure applications with UMA 2 so that access management decisions--like who should be able to access which resources, using which devices, from which networks, etc.--can be delegated to your Gluu Server. 

### UMA 2 Resource Server API's

A client, acting as an [OAuth2 Resource Server](https://tools.ietf.org/html/rfc6749#section-1.1),
MUST:

- Register a protected resource (with the `uma_rs_protect` command)
- Intercept the HTTP call (before the actual REST resource call) and check whether it's allowed to proceed or should be rejected according to the `uma_rs_check_access` command response:
    - Allow access: if the response from `uma_rs_check_access` is `allowed` or `not_protected` an error is returned.
    - If `uma_rs_check_access` returns `denied` then return back HTTP response.
    
The `uma_rs_check_access` operation checks access using the "or" rule when looking for scopes.

For example, a resource like `/photo` protected with scopes `read`, `all` (by `uma_rs_protect` command) assumes that if either of the two scopes is present then access is granted (this follows the "or" rule).

If the "and" rule is preferred it can be achieved with an additional scope, for example:

`Resource1` scopes: `read`, `write`.

`Resource2` scopes: `read_write` (and associate `read` and `write` policies with `read_write` scope)

If access is not granted then unauthorized http code and registered ticket are returned, for example: 

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: UMA realm="example",
      as_uri="https://as.example.com",
      ticket="016f84e8-f9b9-11e0-bd6f-0021cc6004de"
```

The `uma_rs_check_access` returns `denied` then returns back an HTTP response:

```http
HTTP/1.1 403 Forbidden
Warning: 199 - "UMA Authorization Server Unreachable"
```

#### UMA RS Protect resources

Request:

```json
{
    "command":"uma_rs_protect",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",   <- REQUIRED
        "resources":[        <-  REQUIRED as parameter here we have protection json that describes resources on RS
            {
                "path":"/photo",
                "conditions":[
                    {
                        "httpMethods":["GET"],
                        "scopes":[
                            "http://photoz.example.com/dev/actions/view"
                        ]
                    },
                    {
                        "httpMethods":["PUT", "POST"],
                        "scopes":[
                            "http://photoz.example.com/dev/actions/all",
                            "http://photoz.example.com/dev/actions/add"
                        ],
                        "ticketScopes":[
                            "http://photoz.example.com/dev/actions/add"
                        ]
                    }
                ]
            },
            {
                "path":"/document",
                "conditions":[
                    {
                        "httpMethods":["GET"],
                        "scopes":[
                            "http://photoz.example.com/dev/actions/view"
                        ]
                    }
                ]
            }
        ],
        "protection_access_token":"<access token of the client>"      <- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Response:

```json
{
    "status":"ok"
}
```

#### UMA RS Check Access

Request:

```json
{
    "command":"uma_rs_check_access",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",
        "rpt":"eyJ0 ... NiJ9.eyJ1c ... I6IjIifX0.DeWt4Qu ... ZXso",    <-- REQUIRED RPT or blank value if absent (not send by RP)
        "path":"<path of resource>",                                   <-- REQUIRED Path of resource (e.g. http://rs.com/phones), /phones should be passed
        "http_method":"<http method of RP request>",                   <-- REQUIRED Http method of RP request (GET, POST, PUT, DELETE)
        "protection_access_token":"<access token of the client>"       <-- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Sample of RP request:
```
GET /users/alice/album/photo HTTP/1.1
Authorization: Bearer vF9dft4qmT
Host: photoz.example.com
```

Params:
```
rpt: 'vF9dft4qmT'
path: /users/alice/album/photo
http_method: GET
```

Access Granted response:

```json
{
    "status":"ok",
    "data":{
        "access":"granted"
    }
}
```

Access Denied with ticket response:

```json
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

Access Denied without ticket response:

```json
{
    "status":"ok",
    "data":{
        "access":"denied"
    }
}
```


Errors:

Resource is not protected
```json
{
    "status":"error",
    "data":{
        "error":"invalid_request",
        "error_description":"Resource is not protected. Please protect your resource first with uma_rs_protect command."
    }
}
```

### UMA 2 Client API's

If your application is calling UMA 2 protected resources, use these API's to obtain an RPT token.

#### UMA RP - Get RPT

Request:

```json
{
    "command":"uma_rp_get_rpt",
    "params": {
         "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",   <- REQUIRED
         "ticket": "016f84e8-f9b9-11e0-bd6f-0021cc6004de",  <- REQUIRED
         "claim_token": "eyj0f9b9...",                      <- OPTIONAL
         "claim_token_format": "http://openid.net/specs/openid-connect-core-1_0.html#IDToken",
         "pct": "c2F2ZWRjb25zZW50",                         <- OPTIONAL                                                      
         "rpt": "SSJHBSUSSJHVhjsgvhsgvshgsv",               <- OPTIONAL
         "scope":["read"],                                  <- OPTIONAL,
         "state": "af0ifjsldkj",                            <- OPTIONAL state that is returned from uma_rp_get_claims_gathering_url command
         "protection_access_token": "ejt3425"               <- OPTIONAL, required if oxd-https-extension is used          
    }
}
```

Success Response:

```json
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

Needs Info Error Response
```json
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


Invalid ticket error:
```json
{
    "status":"error",
    "data":{
        "error":"invalid_ticket",
        "error_description":"Ticket is not valid (outdated or not present on Authorization Server)."
    }
}
```

Internal oxd server error:
```json
{
    "status":"error",
    "data":{
        "error":"internal_error",
        "error_description":"oxd server failed to handle command. Please check logs for details."
    }
}
```

#### UMA RP - Get Claims-Gathering URL

`ticket` parameter for this command MUST be newest, in 90% cases it is from `need_info` error.

Request:

```json
{
    "command":"uma_rp_get_claims_gathering_url",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",         <- REQUIRED
        "ticket": "016f84e8-f9b9-11e0-bd6f-0021cc6004de",        <- REQUIRED
        "claims_redirect_uri":"https://client.example.com/cb",   <- REQUIRED
        "protection_access_token": "ejt3425"                     <- OPTIONAL, required if oxd-https-extension is used
    }
}
```

Success Response:

```json
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

After redirect to claims-gathering url user pass Claims-Gathering Flow and if it is success user is redirected back to `claims_redirect_uri` with new ticket which should be provided with next `uma_rp_get_rpt` call.

Example of response

```
https://client.example.com/cb?ticket=e8e7bc0b-75de-4939-a9b1-2425dab3d5ec
```

## References

- [UMA 2.0 Grant for OAuth 2.0 Authorization Specification](https://docs.kantarainitiative.org/uma/ed/oauth-uma-grant-2.0-06.html)
- [Federated Authorization for UMA 2.0 Specification](https://docs.kantarainitiative.org/uma/ed/oauth-uma-federated-authz-2.0-07.html)
- [Java Resteasy HTTP interceptor of uma-rs](https://github.com/GluuFederation/uma-rs/blob/master/uma-rs-resteasy/src/main/java/org/xdi/oxd/rs/protect/resteasy/RptPreProcessInterceptor.java)
