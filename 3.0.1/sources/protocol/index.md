# OpenID Connect & UMA Protocol Overview

The oxd server supports the OpenID Connect and UMA profiles of OAuth 2.0. OpenID Connect can be used to send a user for authentication and gather identity information about the user. UMA can be used to manage what digital resources the user should have access to.

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
oxd provides six API's for OpenID Connect authentication. In general,
you can think of the Authorization Code Flow as a three step process: 

 - Redirect person to the authorization URL and obtain a code
 - Use code to obtain tokens (access, id_token, refresh)
 - Use access token to obtain user claims

The other three oxd API's are:
 
 - Register site (called once--the first time your application uses oxd)
 - Update site registration (not used often)
 - Logout

#### Register site

First of all, the web site must register itself with oxd server. If 
registration is successful, ox will return an identifier for the 
application, which must be presented in subsequent API calls. This
is the `oxd-id`, not to be confused with the OpenID Connect client id.

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
        "op_host":"https://ce-dev.gluu.org"                            <- OPTIONAL (But if missing, must be present in defaults)
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
        "client_secret": "<client secret of existing client>"          <- OPTIONAL must be used together with client_secret.
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
        "prompt": "login"                                 <- optional, skipped if no value specified or missed. prompt=login is required if you want to force alter current user session (in case user is already logged in from site1 and site2 construsts authorization request and want to force alter current user session)
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
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF", <- Required
        "code":"I6IjIifX0",                              <- Required, code from OP redirect url (see example above)
        "state":"af0ifjsldkj"                            <- Required
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
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",
        "access_token":"SlAV32hkKG"
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
        "id_token_hint": "eyJ0 ... NiJ9.eyJ1c ... I6IjIifX0.DeWt4Qu ... ZXso",<-- OPTIONAL (oxd server will use last used ID Token)
        "post_logout_redirect_uri": "<post logout redirect uri here>",        <-- OPTIONAL
        "state": "<site state>",                                              <-- OPTIONAL
        "session_state": "<session state>"                                    <-- OPTIONAL
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

## UMA Authorization 
UMA is a profile of OAuth 2.0 that defines RESTful, JSON-based, standardized flows and constructs for coordinating the protection of any API or web resource. oxd makes it easy to secure applications with UMA so that access management decisions--like who should be able to access which resources, using which devices, from which networks, etc.--can be delegated to your Gluu Server. 

### UMA Resource Server API's

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
        ]
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
        "http_method":"<http method of RP request>"                    <-- REQUIRED Http method of RP request (GET, POST, PUT, DELETE)
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

### UMA Client API's

If your application is calling UMA protected resources, use these API's to obtain an RPT token.

#### UMA RP - Get RPT

For latest and most up to date parameters of command please check 
latest successful [jenkins build](https://ox.gluu.org/jenkins/job/oxd)

Request:

```json
{
    "command":"uma_rp_get_rpt",
    "params": {
         "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",  <- REQUIRED
         "force_new": false                                <- REQUIRED indicates whether return new RPT, in general should be false, so oxd server can cache/reuse same RPT
    }
}
```

Response:

```json
{
     "status":"ok",
     "data":{
         "rpt":"vF9dft4qmT"
     }
}
```

#### UMA RP - Authorize RPT

Request:

```json
{
    "command":"uma_rp_authorize_rpt",
    "params": {
         "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",  <- REQUIRED
         "rpt": "vF9dft4qmT",                              <- REQUIRED
         "ticket": "016f84e8-f9b9-11e0-bd6f-0021cc6004de"  <- REQUIRED
    }
}
```

Authorized Response (Success):

```json
{
     "status":"ok",
}
```

Not authorized error:
```json
{
    "status":"error",
    "data":{
        "code":"not_authorized",
        "description":"RPT is not authorized."
    }
}
```


Invalid ticket error:
```json
{
    "status":"error",
    "data":{
        "code":"invalid_ticket",
        "description":"Ticket is not valid (outdated or not present on Authorization Server)."
    }
}
```

Invalid rpt error:
```json
{
    "status":"error",
    "data":{
        "code":"invalid_rpt",
        "description":"RPT is not valid (outdated or not present on Authorization Server)."
    }
}
```

## Gluu OAuth2 Access Management API's

GAT stands for Gluu Access Token. It is invented by Gluu and is described here: 
[Gluu OAuth2 Access Management](https://ox.gluu.org/doku.php?id=uma:oauth2_access_management)

Request:

```json
{
    "command":"uma_rp_get_gat",
    "params": {
         "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",  <- REQUIRED
         "scopes": [                                       <- REQUIRED RP should know required scopes in advance
             "http://photoz.example.com/dev/actions/add",
             "http://photoz.example.com/dev/actions/view",
             "http://photoz.example.com/dev/actions/edit"
         ]
    }
}
```

Response:

```json
{
     "status":"ok",
     "data":{
         "rpt":"fg6vF9dft4qmT"
     }
}
```

## References

- [UMA 1.0.1 Specification](https://docs.kantarainitiative.org/uma/rec-uma-core.html#permission-failure-to-client)
- [Sample RS of Java Resteasy HTTP interceptor of uma-rs](https://github.com/GluuFederation/uma-rs/blob/master/uma-rs-resteasy/src/main/java/org/xdi/oxd/rs/protect/resteasy/RptPreProcessInterceptor.java)

