# oxd-https-extension API

## Swagger UI
Peruse the API documentation on [Swagger](http://petstore.swagger.io/?url=https://raw.githubusercontent.com/GluuFederation/oxd/version_3.1.3/oxd-https-extension/src/main/resources/swagger.yaml). 

## Setup Client

*Non-normative example request*
```language-json
POST /setup-client
{
    "authorization_redirect_uri": "https://client.example.org/cb", <- REQUIRED
    "op_host":"https://<ophostname>"                               <- OPTIONAL (If missing, must be present in defaults)
    "post_logout_redirect_uri": "https://client.example.org/cb",   <- OPTIONAL 
    "application_type": "web",                                     <- OPTIONAL
    "response_types": ["code"],                                    <- OPTIONAL
    "grant_types": ["authorization_code", "client_credentials"],   <- OPTIONAL 
    "scope": ["openid"],                                           <- OPTIONAL
    "acr_values": ["basic"],                                       <- OPTIONAL
    "client_name": "",                                             <- OPTIONAL (If missing, oxd will generate its own non-human readable name)
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
    "client_id": "<client id of existing client>",                 <- OPTIONAL - ignores all other parameters and skips new client registration forcing to use existing client (client_secret is required if this parameter is set)
    "client_secret": "<client secret of existing client>"          <- OPTIONAL - must be used together with client_secret
}
```

*Non-normative example response*
```language-json
{
    "status": "ok",
    "data": {
        "oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6",        <-- DEPRECATED - additional registered client oxdId which can be used for normal operations (same as returned by register_site command). It is going to be removed in future releases.
        "client_id_of_oxd_id": "@!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!A2BB.9AE6.AAA4", <-- DEPRECATED - additional registered client oxdId which can be used for normal operations (same as returned by register_site command). It is going to be removed in future releases.
        "op_host": "https://<op-hostname>",
        "setup_client_oxd_id": "<setup client oxd_id>",          <-- oxdId of the setup client used to obtain access token
        "client_id": "@!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!A2BB.9AE6.5F14.B387",
        "client_secret": "f436b936-03fc-433f-9772-53c2bc9e1c74",
        "client_registration_access_token": "d836df94-44b0-445a-848a-d43189839b17",
        "client_registration_client_uri": "https://<op-hostname>/oxauth/restv1/register?client_id=@!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!A2BB.9AE6.5F14.B387",
        "client_id_issued_at": 1501854943,
        "client_secret_expires_at": 1501941343
    }
}
```

## Get Client Token

*Non-normative example request*
```language-json
POST /get-client-token
{
	"op_host" : "https://<op-hostname>",                                          <- REQUIRED
	"op_discovery_path":""                                                        <- OPTIONAL
	"scope" : ["openid","profile","email","uma_protection"],                      <- OPTIONAL 
	"client_id": "@!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!A2BB.9AE6.5F14.B387", <- REQUIRED
	"client_secret": "f436b936-03fc-433f-9772-53c2bc9e1c74",                      <- REQUIRED
	"authentication_method":"",            <- OPTIONAL, if value is missed then basic authentication is used. Otherwise it's possible to set `private_key_jwt` value for Private Key authentication.
    "algorithm":"",                        <- OPTIONAL but is required if authentication_method=private_key_jwt. Valid values are none, HS256, HS384, HS512, RS256, RS384, RS512, ES256, ES384, ES512
    "key_id":""                            <- OPTIONAL but is required if authentication_method=private_key_jwt. It has to be valid key id from key store.

}
```

*Non-normative example response*
```language-json
{
    "status": "ok",
    "data": {
        "scope": "openid profile uma_protection uma_authorization email",
        "access_token": "b75434ff-f465-4b70-92e4-b7ba6b6c58f2",
        "expires_in": 299,
        "refresh_token": null
    }
}
```

## Introspect Access Token

Request:

```language-json
{
    "command":"introspect-access-token",
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

## Register Site

*Non-normative example request*
```language-json
POST /register-site
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
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
    "client_id": "<client id of existing client>",                 <- OPTIONAL - Ignores all other parameters and skips new client registration forcing to use existing client (client_secret is required if this parameter is set)
    "client_secret": "<client secret of existing client>",         <- OPTIONAL - must be used together with client_secret.
    "client_registration_access_token":"<access token of existing client>", <- OPTIONAL - must be used together with client_id/client_secret
    "client_registration_client_uri":"<uri of existing client>"    <- OPTIONAL - must be used together with client_id/client_secret
}
```

*Non-normative example response*
```language-json
{
    "status": "ok",
    "data": {
        "oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6",
        "op_host": "https://<op-hostname>"
    }
}
```

## Update Site

*Non-normative example request*
```language-json
POST /update-site
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
    "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",                <- REQUIRED
    "authorization_redirect_uri": "https://client.example.org/cb",  <- OPTIONAL 
    "post_logout_redirect_uri": "https://client.example.org/cb",    <- OPTIONAL 
    "client_frontchannel_logout_uris":["https://client.example.org/logout"],   <- OPTIONAL
    "response_type":["code"],                                       <- OPTIONAL
    "grant_types":[],                                               <- OPTIONAL
    "scope": ["opeind", "profile"],                                 <- OPTIONAL
    "acr_values": ["duo"],                                          <- OPTIONAL
    "client_name": "",                                              <- OPTIONAL
    "client_secret_expires_at":1335205592410,                       <- OPTIONAL - can be used to extends client lifetime (milliseconds since 1970)
    "client_jwks_uri": "",                                          <- OPTIONAL
    "client_token_endpoint_auth_method": "",                        <- OPTIONAL
    "client_request_uris":[],                                       <- OPTIONAL
    "client_sector_identifier_uri":"",                              <- OPTIONAL
    "contacts":["foo_bar@spam.org"],                                <- OPTIONAL
    "ui_locales":[],                                                <- OPTIONAL
    "claims_locales":[]                                             <- OPTIONAL
}
```

*Non-normative example response*
```language-json
{
    "status": "ok",
    "data": {
        "oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6"
    }
}
```

## Remove Site

*Non-normative example request*
```language-json
POST /remove-site
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
	"oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6"     <- REQUIRED   
}
```

*Non-normative example response*
```language-json
{
    "status": "ok",
    "data": {
        "oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6"
    }
}
```

## Get Authorization URL

*Non-normative example request*
```language-json
POST /get-authorization-url
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
    "oxd_id": "6F9619FF-8B86-D011-B42D-00CF4FC964FF", <- REQUIRED - obtained after registration
    "scope": ["openid"],                              <- OPTIONAL - by default takes scopes that was registered during register_site command
    "acr_values": ["duo"],                            <- OPTIONAL - default is basic
    "prompt": "login",                                <- OPTIONAL - skipped if no value specified or missed. prompt=login is required if you want to force alter current user session (in case user is already logged in from site1 and site2 construsts authorization request and want to force alter current user session)
    "custom_parameters": {                            <- OPTIONAL
        "param1":"value1",
        "param2":"value2"
    }
}
```

*Non-normative example response*
```language-json
{
    "status": "ok",
    "data": {
        "authorization_url": "https://<op-hostname>/oxauth/restv1/authorize?response_type=code&client_id=@!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!8A36.24E1.97DE.F4EF&redirect_uri=https://192.168.200.95/&scope=openid+profile+email+uma_protection+uma_authorization&state=473ot4nuqb4ubeokc139raur13&nonce=lbrdgorr974q66q6q9g454iccm"
    }
}
```

## Get Tokens By Code

*Non-normative example request*
Use the code and state obtained in the previous step to call this API to retrieve tokens.
```language-json
POST /get-tokens-by-code
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
	"oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6",  <- REQUIRED
	"code" : "0b9f1518-15aa-47b2-9477-d4c607447e18",   <- REQUIRED
	"state" :"6q1ec90hn6ui4ipigv91hrbodj"              <- REQUIRED
}
```

*Non-normative example response*
```language-json
{
    "status": "ok",
    "data": {
        "access_token": "88bba7f5-961c-4b71-8053-9ab35f1ad395",
        "expires_in": 299,
        "id_token": "eyJraWQiOiI5MTUyNTU1Ni04YmIwLTQ2MzYtYTFhYy05ZGVlNjlhMDBmYWUiLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NlLWRldjMuZ2x1dS5vcmciLCJhdWQiOiJAITE3MzYuMTc5RS5BQTYwLjE2QjIhMDAwMSE4RjdDLkI5QUIhMDAwOCE5Njk5LkFFQzcuOTM3MS4yODA3IiwiZXhwIjoxNTAxODYwMzMwLCJpYXQiOjE1MDE4NTY3MzAsIm5vbmNlIjoiOGFkbzJyMGMzYzdyZG03OHU1OTUzbTc5MXAiLCJhdXRoX3RpbWUiOjE1MDE4NTY2NzIsImF0X2hhc2giOiItQ3gyZHo1V3Z3X2tCWEFjVHMzbUZBIiwib3hPcGVuSURDb25uZWN0VmVyc2lvbiI6Im9wZW5pZGNvbm5lY3QtMS4wIiwic3ViIjoialNadE9rOUlGTmdLRTZUVVNGMHlUbHlzLVhCYkpic0dSckY5eG9JV2c4dyJ9.gi5tvt-duNygoDGjCqQqdKH6D6jJnpW5p6zYzxYiHtYecxkp8ks6AUJ4bmvkVHBd7a3vNbbFDY9Z3wsHGIMRXZRUXFVSQL1-JG0ye9zFH6Pp--Ky3Hexrl7V8PJ-AAFJwX3s854svIXugKNJMwPMmOvKcdzhhPgMBjh8GfVCpTW415iIBg2XcCmoq40zMIdya2WFeBy7IndcaoKcyUKQwqvtGfA53K3qe6RnKS_ps116n24RyBGypovLlThnoGdh20SZfaGVzoumRwW5-wBR6Iff97jgjx_SEOhhJK7Dr4dxliePd6H5ZtgUmFFoxm6Jyln9LKx-WrrUZRYNuFkh-w",
        "refresh_token": "33d7988e-6ffb-4fe5-8c2a-0e158691d446",
        "id_token_claims": {
            "at_hash": [
                "-Cx2dz5Wvw_kBXAcTs3mFA"
            ],
            "aud": [
                "@!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!9699.AEC7.9371.2807"
            ],
            "sub": [
                "jSZtOk9IFNgKE6TUSF0yTlys-XBbJbsGRrF9xoIWg8w"
            ],
            "auth_time": [
                "1501856672"
            ],
            "iss": [
                "https://<op-hostname>"
            ],
            "exp": [
                "1501860330"
            ],
            "iat": [
                "1501856730"
            ],
            "nonce": [
                "8ado2r0c3c7rdm78u5953m791p"
            ],
            "oxOpenIDConnectVersion": [
                "openidconnect-1.0"
            ]
        }
    }
}
```

## Get User Info

*Non-normative example request*
```language-json
POST /get-user-info
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
    "oxd_id" : "bcad760f-91ba-46e1-a020-05e4281d91b6",       <- REQUIRED
    "access_token" :"88bba7f5-961c-4b71-8053-9ab35f1ad395"   <- REQUIRED
}
```

*Non-normative example response*
```language-json
{
  "claims": {
    "sub": [
      "N4tKFw2-ZCY5V7AaBgi2sGEgCGKtNX6--53aPnfEbNs"
    ],
    "zoneinfo": [
      "America/Chicago"
    ],
    "website": [
      "http://www.example.com"
    ],
    "birthdate": [
      "1983-1-6"
    ],
    "gender": [
      "Male"
    ],
    "profile": [
      "http://www.mywebsite.com/profile"
    ],
    "preferred_username": [
      "user"
    ],
    "middle_name": [
      "User"
    ],
    "locale": [
      "en-US"
    ],
    "given_name": [
      "Test"
    ],
    "picture": [
      "http://www.example.come/uploads/2012/04/mike.png"
    ],
    "updated_at": [
      "20170224125915.538Z"
    ],
    "nickname": [
      "user"
    ],
    "name": [
      "oxAuth Test User"
    ],
    "family_name": [
      "User"
    ]
  }
}
```

## Logout URL

*Non-normative example request*
```language-json
POST /get-logout-uri
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
    "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",                       <-- REQUIRED
    "id_token_hint": "eyJ0 ... NiJ9.eyJ1c ... I6IjIifX0.DeWt4Qu ... ZXso", <- OPTIONAL - oxd server will use last used ID Token
    "post_logout_redirect_uri": "<post logout redirect uri here>",         <- OPTIONAL
    "state": "<site state>",                                               <- OPTIONAL
    "session_state": "<session state>",                                    <- OPTIONAL
}
```

*Non-normative example response*
```language-json
{
  "uri": "https://<op-hostname>/oxauth/seam/resource/restv1/oxauth/end_session?id_token_hint=eyJraWQiOiI1YmM2ZGM3MS0xYjA1LTQ5YzMtYWU3MC0zYTg4Y2ZiMjQwN2QiLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NlLWRldi5nbHV1Lm9yZyIsImF1ZCI6IkAhNUE1OC5BRTBELkQzODMuMUU0NiEwMDAxIUUzOEIuN0RCRSEwMDA4IUE3MTkuOTU4QS41QjdGLkVBQkMiLCJleHAiOjE0OTAwMTk5MjEsImlhdCI6MTQ5MDAxNjMyMSwibm9uY2UiOiJkNGdsbmtndHAxYWlqZ3JnY3V2cGp1N2k3cCIsImF1dGhfdGltZSI6MTQ5MDAxNjI3MiwiYXRfaGFzaCI6Im1Xa2NXQzZ6NC1qN0ZNX0ctX0tYMWciLCJveFZhbGlkYXRpb25VUkkiOiJodHRwczovL2NlLWRldi5nbHV1Lm9yZy9veGF1dGgvb3BpZnJhbWUiLCJveE9wZW5JRENvbm5lY3RWZXJzaW9uIjoib3BlbmlkY29ubmVjdC0xLjAiLCJzdWIiOiJONHRLRncyLVpDWTVWN0FhQmdpMnNHRWdDR0t0Tlg2LS01M2FQbmZFYk5zIn0.PvCdzPnMwqPNUw1bzd8tvzpJqYu-P2iCTnELr85ZaJTG8_Fdj3EruLgUBa-emeum3j29cFgdjFPx6WplfCV1GnehOieXjDiAAE85fy-stxXwII3xrva5ZjG0FnTYnJLoRmy0BWMjFC2IdCoISJI9imcfvmQmlvNmU0EjLS02cJf3JAaqEaM-FJWdQv8end9-Sq2bcp6ME3voRjV30ps_7jcDdlM_hW3M_e3RdrXYCDifbl_1jaNip5tb6_bLpgTADDoLT3fTvACRN057e2GCkSYdxvVhIjfDsjnOhk5n3TDcWedriu99H8-sNXyI_aBr3HAXd37CsgmdfIJcgUNJJw"
}
```

## Get Access Token By Refresh Token

*Non-normative example request*
```language-json
POST /get-access-token-by-refresh-token
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
    "oxd_id" : "bcad760f-91ba-46e1-a020-05e4281d91b6",       <-- REQUIRED
    "refresh_token":"33d7988e-6ffb-4fe5-8c2a-0e158691d446",  <-- REQUIRED - refresh_token from get_tokens_by_code command
    "scope" : ["openid","profile","email","uma_protection"]  <-- OPTIONAL - If not specified. should grant access with scope provided in previous request
}
```

*Non-normative example response*
```language-json
{
    "status": "ok",
    "data": {
        "scope": "openid profile uma_protection email",
        "access_token": "14f95caa-1f5a-46f8-ae8c-069873591f67",
        "expires_in": 299,
        "refresh_token": "c6cbb8ec-1d36-4d06-bc4f-58c40214133e"
    }
}
```

## UMA RS Protect Resources

It's important to have a single HTTP method, mentioned only once within a given path in JSON, otherwise, the operation will fail.

*Non-normative example request*
```language-json
POST /uma-rs-protect
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
	"oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6",  <- REQUIRED
    "overwrite":false,                                 <- OPTIONAL oxd_id registers resource, if send uma_rs_protect second time with same oxd_id and overwrite=false then it will fail with error uma_protection_exists. overwrite=true means remove existing UMA Resource and register new based on JSON Document.
	"resources": [{                                    <- REQUIRED
		"path": "/scim",
		"conditions": [{
			"httpMethods": ["GET"],
			"scopes": ["https://example.com/identity/seam/resource/restv1/scim/vas1"],
			"ticketScopes": ["https://example.com/identity/seam/resource/restv1/scim/vas1"]
		}]
	}]
}
```


Request with `scope_expression`. `scope_expression` is a Gluu-invented extension which allows a JsonLogic expression instead of a single list of scopes. Please read more about `scope_expression` [here](https://gluu.org/docs/ce/3.1.2/admin-guide/uma.md).
```language-json
{
    "oxd_id": "6F9619FF-8B86-D011-B42D-00CF4FC964FF",  <- REQUIRED
    "overwrite":false,                                 <- OPTIONAL oxd_id registers resource, if send uma_rs_protect second time with same oxd_id and overwrite=false then it will fail with error uma_protection_exists. overwrite=true means remove existing UMA Resource and register new based on JSON Document.
    "resources": [                                     <- REQUIRED
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
    ]
}
```
```

*Non-normative example response*
```language-json
{
    "status": "ok",
    "data": {
        "oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6"
    }
}
```


## UMA RS Check Access

*Non-normative example request*
```language-json
POST /uma-rs-check-access
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
	"oxd_id": "bcad760f-91ba-46e1-a020-05e4281d91b6", <- REQUIRED
	"rpt":"",                                         <- REQUIRED - RPT or blank value if not sent by RP
	"path":"/scim",                                   <- REQUIRED - Path of resource (e.g. http://rs.com/phones), /phones should be passed
	"http_method" : "GET"                             <- REQUIRED - HTTP method of RP request (GET, POST, PUT, DELETE)
}
```

*Non-normative Access Granted example response*
```language-json
{
    "status":"ok",
    "data":{
        "access":"granted"
    }
}
```

*Non-normative Access Denied example response*
```language-json
{
    "status": "ok",
    "data": {
        "access": "denied",
        "ticket": "e986fd2b-de83-4947-a889-8f63c7c409c0",
        "www-authenticate_header": "UMA realm=\"rs\",as_uri=\"https://<op-hostname>\",error=\"insufficient_scope\",ticket=\"e986fd2b-de83-4947-a889-8f63c7c409c0\""
    }
}
```

## UMA Introspect RPT

Request:

```language-json
POST /introspect-rpt
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
   "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",         <- REQUIRED
   "rpt": "016f84e8-f9b9-11e0-bd6f-0021cc6004de"            <- REQUIRED
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

## UMA RP - Get RPT

*Non-normative example request*
```language-json
POST /uma-rp-get-rpt
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
    "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF",   <- REQUIRED
    "ticket": "016f84e8-f9b9-11e0-bd6f-0021cc6004de",  <- REQUIRED
    "claim_token": "eyj0f9b9...",                      <- OPTIONAL
    "claim_token_format": "http://openid.net/specs/openid-connect-core-1_0.html#IDToken", <- OPTIONAL but required if claims_token is specified
    "pct": "c2F2ZWRjb25zZW50",                         <- OPTIONAL
    "rpt": "SSJHBSUSSJHVhjsgvhsgvshgsv",               <- OPTIONAL
    "scope":["read"],                                  <- OPTIONAL,
    "state": "af0ifjsldkj"                             <- OPTIONAL state that is returned from uma_rp_get_claims_gathering_url command
}
```

*Non-normative example response*
```language-json
{
    "status": "ok",
    "data": {
        "pct": "4f44136f-797d-4b70-aa4a-a4d5f96dad7c_86BA.DB48.64EE.52E2.1E48.828A.C4E6.7C82",
        "updated": false,
        "access_token": "656b0f54-bf05-4ec8-aa95-b81b7c9bfb7a_1649.62A5.396A.3D67.B24F.74E9.2254.E4EF",
        "token_type": "Bearer"
    }
}
```

## UMA RP Get Claims Gathering URL

*Non-normative example request*
```language-json
POST /uma-rp-get-claims-gathering-url
Authorization: Bearer b75434ff-f465-4b70-92e4-b7ba6b6c58f2
{
	"oxd_id":"bcad760f-91ba-46e1-a020-05e4281d91b6",       <- REQUIRED
	"ticket": "fba00191-59ab-4ed6-ac99-a786a88a9f40",      <- REQUIRED
	"claims_redirect_uri":"https://client.example.com/cb"  <- REQUIRED
}
```

*Non-normative Success example response*
```language-json
{
    "status": "ok",
    "data": {
        "url": "https://<op-hostname>/oxauth/restv1/uma/gather_claims?client_id@!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!4508.BF20.9B81.E904&ticket=fba00191-59ab-4ed6-ac99-a786a88a9f40&claims_redirect_uri=https://client.example.com/cb&state=d871gpie16np0f5kfv936sc33k",
        "state": "d871gpie16np0f5kfv936sc33k"
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
