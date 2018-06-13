# OpenID Connect & UMA Protocol Overview

oxd implements the [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) and [UMA 2.0](https://docs.kantarainitiative.org/uma/wg/oauth-uma-grant-2.0-05.html) profiles of OAuth 2.0. 

- The [oxd OpenID Connect APIs](#openid-connect-authentication) can be used to send a user to an OpenID Connect Provider (OP) for authentication and to gather identity information ("claims") about the user 

- The [oxd UMA APIs](#uma-2-authorization) can be used to send a user to an UMA Authorization Server (AS) for access policy enforcement, for example to centrally manage which people (or software clients) can access which web pages and APIs      

## OpenID Connect Authentication

OpenID Connect is a simple identity layer on top of OAuth 2.0. 

Technically OpenID Connect is not an authentication protocol--it enables a person to authorize the release of personal information from an "identity provider" to a separate application. In the process of authorizing the release of information, the person is authenticated (if no previous session exists).  

### Authentication Flow
oxd supports the OpenID Connect [Hybrid Flow](http://openid.net/specs/openid-connect-core-1_0.html#HybridFlowAuth) and [Authorization Code Flow](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) for authentication. 

Learn more about authentication flows in the [OpenID Connect spec](http://openid.net/specs/openid-connect-core-1_0.html). 

### oxd OpenID Connect APIs
`oxd-server` provides seven APIs for OpenID Connect authentication. In general,
you can think of the Authorization Code Flow as a three-step process: 

 1. Redirect person to the authorization URL and obtain a code
 1. Use code to obtain tokens (access_token, id_token and refresh_token)
 1. Use access token to obtain user claims

The other four oxd APIs are:
 
 - Register site (called once--the first time your application uses oxd)
 - Update site registration (not used often)
 - Logout
 - Get access token by refresh token
 
**IMPORTANT** : 

If you are using the `oxd-https-extension`, before using the above workflow you will need to obtain an access token to secure the interaction between your client and the `oxd-https-extension`. You can follow the two steps below. 

 - [Setup client](#setup-client) (returns `client_id` and `client_secret`. Make sure `uma_protection` scope is present in request)
 - [Get client token](#get-client-token) (pass `client_id` and `client_secret` to obtain `access_token`)
 
 Pass the obtained access token as `protection_access_token` in all future calls to the `oxd-https-extension`.

#### Set Up Client

If you are using the `oxd-https-extension`, you must setup the client. 

The parameters for Setup Client are the same as for Register the Site command. 
The command registers two clients:

 - client 1 -  client for communication protection (called setup client). This will be used to obtain an access token via the Get Client Token command. The access token will be passed as a `protection_access_token` parameter to other commands. `uma_protection` scope has to be present in request to `setup_client` command. In response:
    - setup_client_oxd_id - oxd_id of setup client
    - client_id - client id of the setup client
    - client_secret - client secret of the setup client   
 - client 2 - it is regular client which can be used for all oxd operations. In response `oxd_id`. We are going to remove this second client registration since we have `register_site` command for this purpose.  

Request:

```language-json
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
        "client_token_endpoint_auth_signing_alg":"",                   <- OPTIONAL possible values: none, HS256, HS384, HS512, RS256, RS384, RS512, ES256, ES384, ES512
        "client_request_uris": [],                                     <- OPTIONAL
        "client_frontchannel_logout_uris": [],                         <- OPTIONAL
        "client_sector_identifier_uri": [],                            <- OPTIONAL
        "contacts": ["foo_bar@spam.org"],                              <- OPTIONAL
        "ui_locales": [],                                              <- OPTIONAL
        "claims_locales": [],                                          <- OPTIONAL
        "claims_redirect_uri": [],                                     <- OPTIONAL
        "client_id": "<client id of existing client>",                 <- OPTIONAL ignores all other parameters and skips new client registration forcing to use existing client (client_secret is required if this parameter is set)
        "client_secret": "<client secret of existing client>"         <- OPTIONAL must be used together with client_secret.
             
    }
}
```

Response:

```language-json
{
    "status":"ok",
    "data":{
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",         <-- DEPRECATED : additional registered client oxdId which can be used for normal operations (same as returned by register_site command). It is going to be removed in future releases.
        "client_id_of_oxd_id": "@!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!A2BB.9AE6.AAA4", <-- DEPRECATED : additional registered client oxdId which can be used for normal operations (same as returned by register_site command). It is going to be removed in future releases.
        "op_host": "<op host>",
        "setup_client_oxd_id": "<setup client oxd_id>",          <-- oxdId of the setup client used to obtain access token
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

```language-json
{
    "command":"get_client_token",
    "params": {
        "client_id": "<client id>",            <- REQUIRED
        "client_secret": "<client secret>",    <- REQUIRED
        "op_host":"https://<ophostname>"       <- REQUIRED
        "op_discovery_path":""                 <- OPTIONAL 
        "scope":[],                            <- OPTIONAL 
        "authentication_method":"",            <- OPTIONAL, if value is missed then basic authentication is used. Otherwise it's possible to set `private_key_jwt` value for Private Key authentication.
        "algorithm":"",                        <- OPTIONAL but is required if authentication_method=private_key_jwt. Valid values are none, HS256, HS384, HS512, RS256, RS384, RS512, ES256, ES384, ES512
        "key_id":""                            <- OPTIONAL but is required if authentication_method=private_key_jwt. It has to be valid key id from key store.
    }
}
```

Response:

```language-json
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

#### Introspect Access Token

Request:

```language-json
{
    "command":"introspect_access_token",
    "params": {
        "oxd_id": "<oxd id>",                <- REQUIRED
        "access_token": "<access_token>"     <- REQUIRED
    }
}
```

Response:

```language-json
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
        "extension_field": "twenty-seven",
        ...
    }
}
```

#### Register Site

The client must first register itself with the `oxd-server`. 

If registration is successful, oxd will dynamically register an OpenID Connect client and return an identifier for the application which must be presented in subsequent API calls. This is the `oxd_id` (not to be confused with the OpenID Connect Client ID). 

`register_site` has many optional parameters. 

The only required parameter is the  `authorization_redirect_uri`. This is where the user will be redirected after successful authorization at the OpenID Connect Provider (OP).

The `op_host` parameter is optional, but it must be specified in either the [default configuration file](../configuration/#oxd-confjson) or the API call. This is the URL at the OP where users will be sent for authentication. 

!!! Note
    `op_host` must point to a valid OpenID Connect Provider (OP) that supports [Client Registration](http://openid.net/specs/openid-connect-registration-1_0.html#ClientRegistration).
    
Request:

```language-json
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
        "client_token_endpoint_auth_signing_alg":"",                   <- OPTIONAL possible values: none, HS256, HS384, HS512, RS256, RS384, RS512, ES256, ES384, ES512
        "client_request_uris": [],                                     <- OPTIONAL
        "client_frontchannel_logout_uris": [],                                      <- OPTIONAL
        "client_sector_identifier_uri": [],                            <- OPTIONAL
        "contacts": ["foo_bar@spam.org"],                              <- OPTIONAL
        "ui_locales": [],                                              <- OPTIONAL
        "claims_locales": [],                                          <- OPTIONAL
        "claims_redirect_uri": [],                                     <- OPTIONAL
        "client_id": "<client id of existing client>",                 <- OPTIONAL ignores all other parameters and skips new client registration forcing to use existing client (client_secret is required if this parameter is set)
        "client_secret": "<client secret of existing client>",         <- OPTIONAL must be used together with client_secret.
        "client_registration_access_token":"<access token of existing client>", <- OPTIONAL must be used together with client_id/client_secret
        "client_registration_client_uri":"<uri of existing client>",   <- OPTIONAL must be used together with client_id/client_secret
        "protection_access_token":"<access token of the client>"       <- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter        
    }
}
```

Response:

```language-json
{
    "status":"ok",
    "data":{
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF"
    }
}
```

#### Update Site 

If something changes in a pre-registered client, you can use this API to update your client in the OP.

Request:

```language-json
{
    "command":"update_site",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",              <- REQUIRED
        "authorization_redirect_uri": "https://client.example.org/cb",<- OPTIONAL 
        "post_logout_redirect_uri": "https://client.example.org/cb",  <- OPTIONAL 
        "client_frontchannel_logout_uris":["https://client.example.org/logout"],   <- OPTIONAL
        "response_type":["code"],                                     <- OPTIONAL
        "grant_types":[],                                             <- OPTIONAL
        "scope": ["opeind", "profile"],                               <- OPTIONAL
        "acr_values": ["duo"],                                        <- OPTIONAL
        "client_name": "",                                            <- OPTIONAL
        "client_secret_expires_at":1335205592410,                     <- OPTIONAL can be used to extends client lifetime (milliseconds since 1970)
        "client_jwks_uri": "",                                        <- OPTIONAL
        "client_token_endpoint_auth_method": "",                      <- OPTIONAL
        "client_request_uris":[],                                     <- OPTIONAL
        "client_sector_identifier_uri":"",                            <- OPTIONAL
        "contacts":["foo_bar@spam.org"],                              <- OPTIONAL
        "ui_locales":[],                                              <- OPTIONAL
        "claims_locales":[],                                          <- OPTIONAL
        "protection_access_token":"<access token of the client>"      <- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Response:

```language-json
{
    "status":"ok"
    "data": {
        "oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6"
    }
}
```

#### Remove Site 

Request:

```language-json
{
    "command":"remove_site",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF"              <- REQUIRED       
    }
}
```

Response:

```language-json
{
    "status":"ok",
    "data": {
        "oxd_id": "6F9619FF-8B86-D011-B42D-00CF4FC964FF"
    }
}
```

#### Get Authorization URL

Returns the URL at the OpenID Connect Provider (OP) to which your application must redirect the person to authorize the release of personal data (and perhaps be authenticated in the process if no previous session exists).

The response from the OpenID Connect Provider (OP) will include the code and state values, which should be used to subsequently obtain tokens.

Request:

```language-json
{
    "command":"get_authorization_url",
    "params": {
        "oxd_id": "6F9619FF-8B86-D011-B42D-00CF4FC964FF", <- REQUIRED - obtained after registration
        "scope": ["openid"],                              <- OPTIONAL - default takes scopes that was registered during register_site command)
        "acr_values": ["duo"],                            <- OPTIONAL - default is basic
        "prompt": "login",                                <- OPTIONAL - skipped if no value specified or missed. prompt=login is required if you want to force alter current user session (in case user is already logged in from site1 and site2 construsts authorization request and want to force alter current user session)
        "custom_parameters": {                            <- OPTIONAL
            "param1":"value1",
            "param2":"value2"
        },
        "protection_access_token":"<access token of the client>" <- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off / on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Response:

```language-json
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

After redirecting to the above URL, the OpenID Connect Provider (OP) will return a response to the URL your application registered as the redirect URI (parse out the code and state). It will look like this:

```language-http
HTTP/1.1 302 Found
Location: https://client.example.org/cb?code=SplxlOBeZQQYbYS6WxSbIA&state=af0ifjsldkj&scopes=openid%20profile
```

#### Get Tokens (ID & Access) by Code

Use the code and state obtained in the previous step to call this API to retrieve tokens.

Request:

```language-json
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

```language-json
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

#### Get Access Token by Refresh Token

A Refresh Token can be used to obtain a renewed Access Token. 

Request:

```language-json
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

```language-json
{
    "status":"ok",
    "data":{
        "access_token":"SlAV32hkKG",
        "expires_in":3600,
        "refresh_token":"aaAV32hkKG1"
    }
}
```


#### Get User Info

Use the access token from the step above to retrieve a JSON object with the user claims.

Request:

```language-json
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

```language-json
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

#### Get Logout URI

The `get_logout_uri` command uses front-channel logout. A page is returned with iFrames, each of which contains the logout URL of the applications that have a session in that browser. 

These iFrames should be loaded automatically, enabling each application to get a notification of logout, and to hopefully clean up any cookies in the person's browser. If the person blocks [third-party cookies](https://en.wikipedia.org/wiki/HTTP_cookie#Third-party_cookie) in their browser, front-channel logout will not work.

Request:

```language-json
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

```language-json
{
    "status":"ok",
    "data":{
        "uri":"https://<server>/end_session?id_token_hint=<id token>&state=<state>&post_logout_redirect_uri=<...>"
    }
}
```


## UMA 2 Authorization 
UMA 2 is a profile of OAuth 2.0 that defines RESTful, JSON-based, standardized flows and constructs for coordinating the protection of APIs and web resources. 

Using oxd, your application can delegate access management decisions, like who can access which resources, from what devices, to a central UMA Authorization Server (AS) like the [Gluu AS](https://gluu.org/docs/ce/admin-guide/uma/). 

### UMA 2 Resource Server API's

Your client, acting as an [OAuth2 Resource Server](https://tools.ietf.org/html/rfc6749#section-1.1), MUST:

- Register a protected resource (with the `uma_rs_protect` command)
- Intercept the HTTP call (before the actual REST resource call) and check the `uma_rs_check_access` command response to determine whether the requester is allowed to proceed or should be rejected:
    - Allow access: if the response from `uma_rs_check_access` is `allowed` or `not_protected`, an error is returned.
    - If `uma_rs_check_access` returns `denied` then return back HTTP response.
- client must have `client_credenitals` grant type. It's required for correct PAT obtaining.    
        
The `uma_rs_check_access` operation checks access using the "or" rule when evaluating scopes.

For example, a resource like `/photo` protected with scopes `read`, `all` (by `uma_rs_protect` command) assumes that if either `read` or `all` is present access is granted.

If the "and" rule is preferred it can be achieved by adding an additional scope, for example:

`Resource1` scopes: `read`, `write` (follors the "or" rule).

`Resource2` scopes: `read_write` (and associate `read` *and* `write` policies with the `read_write` scope)

If access is not granted then an unauthorized HTTP code and registered ticket are returned, for example: 

```language-http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: UMA realm="example",
      as_uri="https://as.example.com",
      ticket="016f84e8-f9b9-11e0-bd6f-0021cc6004de"
```

The `uma_rs_check_access` returns `denied` then returns back an HTTP response:

```language-http
HTTP/1.1 403 Forbidden
Warning: 199 - "UMA Authorization Server Unreachable"
```

#### UMA RS Protect Resources

It's important to have a single HTTP method mentioned only one time within a given path in JSON, otherwise the operation will fail.

Request:

```language-json
{
    "command":"uma_rs_protect",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",   <- REQUIRED
        "overwrite":false,                                 <- OPTIONAL oxd_id registers resource, if send uma_rs_protect second time with same oxd_id and overwrite=false then it will fail with error uma_protection_exists. overwrite=true means remove existing UMA Resource and register new based on JSON Document.
        "resources":[        <-  REQUIRED
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
        "protection_access_token":"<access token of the client>"      <- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off / on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Request with `scope_expression`. `scope_expression` is a Gluu-invented extension that allows a JsonLogic expression instead of a single list of scopes. Read more about `scope_expression` [here](https://gluu.org/docs/ce/3.1.3/admin-guide/uma.md).
```language-json
{
  "command": "uma_rs_protect",
  "params": {
    "oxd_id": "6F9619FF-8B86-D011-B42D-00CF4FC964FF",  <-REQUIRED
    "overwrite":false,                                 <- OPTIONAL oxd_id registers resource, if send uma_rs_protect second time with same oxd_id and overwrite=false then it will fail with error uma_protection_exists. overwrite=true means remove existing UMA Resource and register new based on JSON Document.
    "resources": [  <-  REQUIRED
      {
        "path": "/photo",
        "conditions": [
          {
            "httpMethods": [
              "GET"
            ],
            "scope_expression": {
              "rule": {
                "and": [
                  {
                    "or": [
                      {
                        "var": 0
                      },
                      {
                        "var": 1
                      }
                    ]
                  },
                  {
                    "var": 2
                  }
                ]
              },
              "data": [
                "http://photoz.example.com/dev/actions/all",
                "http://photoz.example.com/dev/actions/add",
                "http://photoz.example.com/dev/actions/internalClient"
              ]
            }
          },
          {
            "httpMethods": [
              "PUT",
              "POST"
            ],
            "scope_expression": {
              "rule": {
                "and": [
                  {
                    "or": [
                      {
                        "var": 0
                      },
                      {
                        "var": 1
                      }
                    ]
                  },
                  {
                    "var": 2
                  }
                ]
              },
              "data": [
                "http://photoz.example.com/dev/actions/all",
                "http://photoz.example.com/dev/actions/add",
                "http://photoz.example.com/dev/actions/internalClient"
              ]
            }
          }
        ]
      }
    ],
    "protection_access_token": "<access token of the client>"   <-OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off / on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter   
  }
}
```

Response:

```language-json
{
    "status":"ok"
    "data": {
        "oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6"
    }
}
```

#### UMA RS Check Access

Request:

```language-json
{
    "command":"uma_rs_check_access",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",
        "rpt":"eyJ0 ... NiJ9.eyJ1c ... I6IjIifX0.DeWt4Qu ... ZXso",    <-- REQUIRED - RPT or blank value if not sent by RP
        "path":"<path of resource>",                                   <-- REQUIRED - Resource Path (e.g. http://rs.com/phones), /phones should be passed
        "http_method":"<http method of RP request>",                   <-- REQUIRED - HTTP method of RP request (GET, POST, PUT, DELETE)
        "protection_access_token":"<access token of the client>"       <-- OPTIONAL for `oxd-server` but REQUIRED for `oxd-https-extension`. You can switch off/on protection by `oxd-server`'s `protect_commands_with_access_token` configuration parameter
    }
}
```

Sample of RP Request:
```language-http
GET /users/alice/album/photo HTTP/1.1
Authorization: Bearer vF9dft4qmT
Host: photoz.example.com
```

Parameters:
```language-yaml
rpt: 'vF9dft4qmT'
path: /users/alice/album/photo
http_method: GET
```

Access Granted Response:

```language-json
{
    "status":"ok",
    "data":{
        "access":"granted"
    }
}
```

Access Denied with Ticket Response:

```language-json
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

Access Denied without Ticket Response:

```language-json
{
    "status":"ok",
    "data":{
        "access":"denied"
    }
}
```

Resource is not Protected Error Response:
```language-json
{
    "status":"error",
    "data":{
        "error":"invalid_request",
        "error_description":"Resource is not protected. Please protect your resource first with uma_rs_protect command."
    }
}
```

#### UMA 2 Introspect RPT

Request:

```language-json
{
    "command":"introspect_rpt",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",         <- REQUIRED
        "rpt": "016f84e8-f9b9-11e0-bd6f-0021cc6004de"            <- REQUIRED
    }
}
```

Success Response:

```language-json
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


### UMA 2 Client APIs

If your application is calling UMA 2 protected resources, use these APIs to obtain an RPT token.

#### UMA RP - Get RPT

Request:

```language-json
{
    "command":"uma_rp_get_rpt",
    "params": {
         "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",   <- REQUIRED
         "ticket": "016f84e8-f9b9-11e0-bd6f-0021cc6004de",  <- REQUIRED
         "claim_token": "eyj0f9b9...",                      <- OPTIONAL
         "claim_token_format": "http://openid.net/specs/openid-connect-core-1_0.html#IDToken",  <- OPTIONAL, but REQUIRED if claims_token is specified
         "pct": "c2F2ZWRjb25zZW50",                         <- OPTIONAL
         "rpt": "SSJHBSUSSJHVhjsgvhsgvshgsv",               <- OPTIONAL
         "scope":["read"],                                  <- OPTIONAL
         "state": "af0ifjsldkj",                            <- OPTIONAL state that is returned from uma_rp_get_claims_gathering_url command
         "protection_access_token": "ejt3425"               <- OPTIONAL, but REQUIRED if oxd-https-extension is used
    }
}
```

Success Response:

```language-json
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

Needs Info Error Response:
```language-json
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

Invalid Ticket Error Response:
```language-json
{
    "status":"error",
    "data":{
        "error":"invalid_ticket",
        "error_description":"Ticket is not valid (outdated or not present on Authorization Server)."
    }
}
```

Internal oxd-server Error Response:
```language-json
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

```language-json
{
    "command":"uma_rp_get_claims_gathering_url",
    "params": {
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",         <- REQUIRED
        "ticket": "016f84e8-f9b9-11e0-bd6f-0021cc6004de",        <- REQUIRED
        "claims_redirect_uri":"https://client.example.com/cb",   <- REQUIRED
        "protection_access_token": "ejt3425"                     <- OPTIONAL, but REQUIRED if oxd-https-extension is used
    }
}
```

Success Response:

```language-json
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

After being redirected to the Claims Gathering URL, the user goes through the claims gathering flow. If successful, the user is redirected back to `claims_redirect_uri` with a new ticket which should be provided with the next `uma_rp_get_rpt` call.

Example of Response:

```
https://client.example.com/cb?ticket=e8e7bc0b-75de-4939-a9b1-2425dab3d5ec
```

## References

- [UMA 2.0 Grant for OAuth 2.0 Authorization Specification](https://docs.kantarainitiative.org/uma/ed/oauth-uma-grant-2.0-06.html)
- [Federated Authorization for UMA 2.0 Specification](https://docs.kantarainitiative.org/uma/ed/oauth-uma-federated-authz-2.0-07.html)
- [Java Resteasy HTTP interceptor of uma-rs](https://github.com/GluuFederation/uma-rs/blob/master/uma-rs-resteasy/src/main/java/org/xdi/oxd/rs/protect/resteasy/RptPreProcessInterceptor.java)
