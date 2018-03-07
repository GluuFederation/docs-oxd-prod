# oxd-ruby

## Overview

Use oxd's Ruby library to send users from a Ruby application to your Gluu Server OpenID Connect Provider (OP) for dynamic enrollment, single sign-on (SSO), strong authentication, and access management policy enforcement.

!!!
  See the [API docs](http://www.rubydoc.info/gems/oxd-ruby) for in-depth information about the various functions and their parameters

## Sample Code

### OpenID Connect

The plugin requires editing the `application_controller.rb` file to include the following snippet.

```
require 'oxd-ruby'
before_filter :set_oxd_commands_instance
  
  protected
    def set_oxd_commands_instance
      @oxd_command = Oxd::ClientOxdCommands.new
      @uma_command = Oxd::UMACommands.new
      @oxdConfig = @oxd_command.oxdConfig
    end
```

#### Setup Client

In order to use an OpenID Connect Provider (OP) for login, you need to setup your client application at the OP. During setup, oxd will dynamically register the OpenID Connect client and save its configuration. Upon successful setup, the `oxd-server` will assign a unique oxd ID, return a Client ID and Client Secret. This Client ID and Client Secret can be used for `get_client_token` method. 

If your OP does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be passed to the `setup_client` method as a parameter. The Setup Client method is a one time task to configure a client in the oxd-server and OpenID Connect Provider (OP).

**Parameters:**

- authorization_redirect_uri: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- op_host: URL of the OpenID Connect Provider (OP)
- post_logout_uri: (Optional) URL to which the user is redirected to after successful logout
- application_type: (Optional) Application type, the default values are native or web. The default, if omitted, is web.
- response_types: (Optional) Determines the authorization processing flow to be used
- grant_types: (Optional) Grant types that the client is declaring that it will restrict itself to using
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- acr_values: (Optional) Custom authentication script from the Gluu server.  Required for extended authentication.
- client_name: (Optional) Client application name
- client_jwks_uri: (Optional) URL for the client's JSON Web Key Set (JWKS) document
- client_token_endpoint_auth_method: (Optional) Requested client authentication method for the Token Endpoint
- client_request_uris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OpenID Connect Provider (OP)
- client_frontchannel_logout_uris: (Optional) Client application Logout URL
- client_sector_identifier_uri: (Optional) URL using the HTTPS scheme to be used in calculating pseudonymous identifiers by the OpenID Connect Provider (OP)
- contacts: (Optional) Array of e-mail addresses of people responsible for this client
- ui_locales: (Optional) End user's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- claims_locales: (Optional) End user's preferred languages and scripts for claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- client_id: (Optional) Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: (Optional) Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- claims_redirect_uri: (Optional) The URI to which the client wishes the authorization server to direct the requesting party’s user agent after completing its interaction

**Request:**

```ruby
def setup_client
  @oxd_command.setup_client
end
```

**Response:**

```javascript
{
  "status":"ok",
  "data":{
    "oxd_id":"f484c218-72a9-44a6-8a73-98b5814ea12c",
    "op_host":"https://as.com",
    "setup_client_oxd_id":"05a739ca-82dd-42f1-9d60-dc29b4f57de6",
    "client_id":"@!FA47.9507.79D2.1B3B!0001!B14B.4094!0008!5F9D.EBEA.2DF5.A010",
    "client_secret":"27677367-d4e8-4e76-9d48-48531479ca2b",
    "client_registration_access_token":"1dfc58f7-8bd6-41e6-b127-f35626ebad2e",
    "client_registration_client_uri":"https://as.com/oxauth/restv1/register?client_id=@!FA47.9507.79D2.1B3B!0001!B14B.4094!0008!5F9D.EBEA.2DF5.A010",
    "client_id_issued_at":1520417340,
    "client_secret_expires_at":1520503740
  }
}
```

#### Get Client Token

The `get_client_token` method is used to get a token which is sent as `protection_access_token` for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Parameters:**

- client_id: Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- op_host: URL of the OpenID Connect Provider (OP)
- op_discovery_path: (Optional) Path to discovery document. For example if it's https://client.example.com/.well-known/openid-configuration then path is blank. But if it is https://client.example.com/oxauth/.well-known/openid-configuration then path is oxauth
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)

**Request:**

```ruby
def get_client_token
    @oxd_command.get_client_token
end
```

**Response:**

```javascript
{
  "status":"ok",
  "data":{
    "scope":"openid uma_protection",
    "access_token":"3b50dd26-f17f-4fbb-bbce-3c2f65cfe87c",
    "expires_in":299,
    "refresh_token":null
  }
}
```

#### Introspect Access Token

The `introspect_access_token` method is used to gain information about the `access_token` generated from `get_client_token` method.

**Parameters:**

- oxd_id: oxd ID from client registration
- access_token: Generated from get_client_token method

**Request:**

```ruby
def introspect_access_token
  @oxd_command.introspect_access_token
end
```

**Response:**
{
  "status":"ok",
  "data":{
    "active":true,
    "scopes":["openid","uma_protection"],
    "client_id":"@!FA47.9507.79D2.1B3B!0001!B14B.4094!0008!5F9D.EBEA.2DF5.A010",
    "username":null,
    "token_type":"bearer",
    "exp":1520417687,
    "iat":1520417387,
    "sub":null,
    "aud":"@!FA47.9507.79D2.1B3B!0001!B14B.4094!0008!5F9D.EBEA.2DF5.A010",
    "iss":"https://as.com",
    "jti":null,
    "acr_values":null
  }
}
```

#### Register Site

In order to use an OpenID Connect Provider (OP) for login, you need to register your client application at the OpenID Connect Provider (OP). 
During registration oxd will dynamically register the OpenID Connect client and save its configuration. Upon successful registration, a unique 
identifier will be issued by the oxd-server. If your OpenID Connect Provider does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be set in `oxd_config.rb` initializer file. The Register Site method is a one time task to configure a client in the oxd-server and OpenID Connect Provider (OP).

!!! Note: 
    The `register_site` endpoint is not required if client is registered using `setup_client`

**Parameters:**

- authorization_redirect_uri: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- op_host: URL of the OpenID Connect Provider (OP)
- post_logout_uri: (Optional) URL to which the user is redirected to after successful logout
- application_type: (Optional) Application type, the default values are native or web. The default, if omitted, is web.
- response_types: (Optional) Determines the authorization processing flow to be used
- grant_types: (Optional) Grant types the client is restricting itself to using
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- acr_values: (Optional) Required for extended authentication. Custom authentication script from Gluu server.
- client_name: (Optional) Client application name
- client_jwks_uri: (Optional) URL for the client's JSON Web Key Set (JWKS) document
- client_token_endpoint_auth_method: (Optional) Requested client authentication method for the Token Endpoint
- client_request_uris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OP
- client_frontchannel_logout_uris: (Optional) Client application Logout URL
- client_sector_identifier_uri: (Optional) URL using the HTTPS scheme to be used in calculating pseudonymous identifiers by the OpenID Connect Provider (OP)
- contacts: (Optional) Array of e-mail addresses of people responsible for this client
- ui_locales: (Optional) End user's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- claims_locales: (Optional) End user's preferred languages and scripts for claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- client_id: (Optional) Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: (Optional) Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- claims_redirect_uri: (Optional) The URI to which the client wishes the authorization server to direct the requesting party’s user agent after completing its interaction
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```ruby
def register_site
  @oxd_command.register_site 
end
```

**Response:**

```javascript
{
    "status":"ok",
    "data":{
        "oxd_id":"f484c218-72a9-44a6-8a73-98b5814ea12c"
    }
}
```

#### Update Site Registration

The `update_site_registration` method can be used to update an existing client in the OpenID Connect Provider (OP). 
Fields like Authorization Redirect URL, Post Logout URL, Scope, Client Secret and other fields can be updated using this method.

**Parameters:**

- oxd_id: oxd ID from client registration
- authorization_redirect_uri: (Optional) URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- post_logout_uri: (Optional) URL to which the RP is requesting the end user's user agent be redirected to after a logout has been performed
- response_types: (Optional) Determines the authorization processing flow to be used
- grant_types: (Optional) Grant types the client is restricting itself to using
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- acr_values: (Optional) Custom authentication script from the Gluu server. Required for extended authentication.
- client_name: (Optional) Client application name
- client_secret_expires_at: (Optional) Used to extend client lifetime (milliseconds since 1970)
- client_jwks_uri: (Optional) URL for the client's JSON Web Key Set (JWKS) document
- client_token_endpoint_auth_method: (Optional) Requested client authentication method for the Token Endpoint
- client_request_uris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OpenID Connect Provider (OP)
- client_frontchannel_logout_uris: (Optional) Client application Logout URL
- client_sector_identifier_uri: (Optional) URL using the HTTPS scheme to be used in calculating pseudonymous identifiers by the OpenID Connect Provider (OP)
- contacts: (Optional) Array of e-mail addresses of people responsible for this client
- ui_locales: (Optional) End user's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- claims_locales: (Optional) End user's preferred languages and scripts for claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```ruby
def update_site_registration
  status = @oxd_command.update_site()
end
```

**Response:**

```javascript
{
  "status":"ok",
  "data":{
    "oxd_id":"f484c218-72a9-44a6-8a73-98b5814ea12c"
  }
}
```

#### Remove Site

`remove_site` method is used to clean up the website's information from OpenID Provider.

**Parameters:**

- oxd_id: oxd ID from client registration
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```ruby
def delete_registration
  @oxd_command.remove_site
end
```

**Response:**

```javascript
{
    "status":"ok",
    "data": {
        "oxd_id": "f484c218-72a9-44a6-8a73-98b5814ea12c"
    }
}
```

#### Get Authorization URL

The `get_authorization_url` method returns the OpenID Connect Provider (OP) Authentication URL to which the client application must redirect the user to authorize the release of personal data. The Response URL includes state value, which can be used to obtain tokens required for authentication. This state value is used to maintain state between the request and the callback.

**Parameters:**

- oxd_id: oxd ID from client registration
- scope: (Optional) A scope is an indication by the client that it wants to access some resource
- acr_values: (Optional) Custom authentication script from the Gluu server.  Required for extended authentication. 
- prompt: (Optional) Values that specifies whether the Authorization Server prompts the end user for reauthentication and consent
- custom_params: (Optional) Custom parameters
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```ruby
def get_authorization_url
  authorization_url = @oxd_command.get_authorization_url(custom_params: {"param1" => "value1","param2" => "value2"})
  redirect_to authorization_url
end
```

**Response:**

```javascript
{
  "status":"ok",
  "data":{
    "authorization_url":"https://as.com/oxauth/restv1/authorize?response_type=code&client_id=@!FA47.9507.79D2.1B3B!0001!B14B.4094!0008!5395.506E.5472.54C9&redirect_uri=https://client.example.com/open_id/login&scope=openid+profile+email+uma_protection+uma_authorization&state=nh3h52nmik2es4bb25utok5mj8&nonce=qfk24r0rev2a135n4kbaja2f9t&acr_values=basic&prompt=login&custom_response_headers=%5B%7B%22param1%22%3A%22value1%22%7D%2C%7B%22param2%22%3A%22value2%22%7D%5D"
  }
}
```

#### Get Tokens by Code

Upon successful login, the login result will return code and state. `get_tokens_by_code` method uses code and state to retrieve token which can be used to access user claims.

**Parameters:**

- oxd_id: oxd ID from client registration
- code: The code from OpenID Connect Provider (OP) Authorization Redirect URL
- state: The state from OpenID Connect Provider (OP) Authorization Redirect URL
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```ruby
def get_tokens_by_code
  @access_token = @oxd_command.get_tokens_by_code(code, state)
end
```

**Response:**

```javascript
{
  "status":"ok",
  "data":{
    "access_token":"18e6eea4-a1fd-4356-b97b-db8b29e7b8c3",
    "expires_in":299,
    "id_token":"eyJra ... NiJ9.eyJpc3 ... pyVV9zIn0.VC1vyTQ ... RI5A",
    "refresh_token":"519fcd96-bae2-4e8f-96b2-c0e64e9a96bf",
    "id_token_claims":{
      "at_hash":["mTGnc4hBvqhFbjrOwcVw2Q"],
      "aud":["@!FA47.9507.79D2.1B3B!0001!B14B.4094!0008!5395.506E.5472.54C9"],
      "acr":["basic"],
      "sub":["JYk7aFjvuOQZg4zEjNHQpc21U9e4pi4Xmnn312zrU_s"],
      "amr":["[100]"],
      "auth_time":["1520417818"],
      "iss":["https://as.com"],
      "exp":["1520421420"],
      "iat":["1520417820"],
      "nonce":["qfk24r0rev2a135n4kbaja2f9t"],
      "oxOpenIDConnectVersion":["openidconnect-1.0"]
    }
  }
}
```

#### Get Access Token by Refresh Token

The `get_access_token_by_refresh_token` method is used to get a new access token and a new refresh token by using the refresh token which is obtained from `get_tokens_by_code` method.

**Parameters:**

- oxd_id: oxd ID from client registration
- refresh_token: Obtained from the get_tokens_by_code method
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```ruby
def get_access_token_by_refresh_token
  @access_token = @oxd_command.get_access_token_by_refresh_token if(@oxdConfig.dynamic_registration == true)
end
```

**Response:**

```javascript
{
  "status":"ok",
  "data":{
    "scope":"openid uma_protection",
    "access_token":"c263acf7-d712-4025-b782-e24827215353",
    "expires_in":299,
    "refresh_token":"7232ae84-736d-4ed9-97e0-c313f0b326a2"
  }
}
```

#### Get User Info

Once the user has been authenticated by the OpenID Connect Provider (OP), 
the `get_user_info` method returns the claims (First Name, Last Name, E-Mail ID, etc.) about the authenticated end user.

**Parameters:**

- oxd_id: oxd ID from client registration
- access_token: access_token from get_tokens_by_code or get_access_token_by_refresh_token
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```ruby
def get_user_info
  @user = @oxd_command.get_user_info(@access_token)
end
```

**Response:**
```javascript
{"status":"ok",
  data":{
    "claims":{
      "sub":["JYk7aFjvuOQZg4zEjNHQpc21U9e4pi4Xmnn312zrU_s"],
      "name": ["Jane Doe"],
      "given_name": ["Jane"],
      "family_name": ["Doe"],
      "preferred_username": ["j.doe"],
      "email": ["janedoe@example.com"],
      "picture": ["http://example.com/janedoe/me.jpg"]
    },
    refresh_token":null,
    "access_token":null
  }
}
```

#### Logout

`get_logout_uri` method returns the OpenID Connect Provider (OP) Logout URL.
Client application uses this Logout URL to end the user session.

**Parameters:**

- oxd_id: oxd ID from client registration
- id_token_hint: (Optional) ID Token previously issued by the Authorization Server being passed as a hint about the end user's current or past authenticated session with the client
- post_logout_redirect_uri: (Optional) URL to which user is redirected to after successful logout
- state: (Optional) Value used to maintain state between the request and the callback
- session_state: (Optional) JSON string that represents the end user’s login state at the OpenID Connect Provider (OP)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```ruby
def logout
  logout_url = @oxd_command.get_logout_uri(state, session_state)
end
```

**Response:**

```javascript
{
  "status":"ok",
  "data":
  {
    "uri":"https://as.com/oxauth/restv1/end_session?id_token_hint=eyJra ... NiJ9.eyJpc3 ... pyVV9zIn0.VC1vyTQ ... RI5A&post_logout_redirect_uri=https%3A%2F%2Fclient.example.com&state=4oq7aic2ljq0sjfdtrtmicqa1g&session_state=4c46b9e8-0a63-493c-a21f-fac64369cb84"
  }
}

```

### UMA (User Managed Access)

#### RS Protect

`uma_rs_protect` method is used for protecting resources by the Resource Server. The Resource Server is needed to construct the command which will protect the resource.
The command will contain an API path, HTTP methods (POST, GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy is mapped, uma_rs_check_access method will always return access as granted. For more information about uma_rpt_policies you can reference this [document](https://gluu.org/docs/oxd/3.1.2/api/#uma-2-client-apis).

**Parameters:**

- oxd_id: oxd ID from client registration
- resources: One or more protected resources that a resource server manages, abstractly, as a set. In authorization policy terminology, a resource set is the "object" being protected. 
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

Request with scopes:

```ruby
def protect_resources
    condition1 = {
      :httpMethods => ["GET"],
      :scopes => ["http://photoz.example.com/dev/actions/view"]
    }
    condition2 = {
      :httpMethods => ["PUT", "POST"],
      :scopes => [
        "http://photoz.example.com/dev/actions/all",
        "http://photoz.example.com/dev/actions/add"
      ],
      :ticketScopes => ["http://photoz.example.com/dev/actions/add"]
    }
    @uma_command.uma_add_resource("/photoz", condition1, condition2)
    response = @uma_command.uma_rs_protect # Register above resources with UMA RS
end
```

Request with `scope_expression`. `scope_expression` is Gluu invented extension which allows to put JsonLogic expression instead of single list of scopes.

```ruby
def protect_resources
    condition = {
      :httpMethods => ["GET"],
      :scope_expression => {
        :rule => { 
          :and => [
            {
              :or => [{:var => 0}, {:var => 1}]
            },
            {:var => 2}
          ]
        },
        :data => [
          "http://photoz.example.com/dev/actions/all",
          "http://photoz.example.com/dev/actions/add",
          "http://photoz.example.com/dev/actions/internalClient"
        ]
      }
    }
    @uma_command.uma_add_resource("/photoz", condition)
    response = @uma_command.uma_rs_protect # Register above resources with UMA RS
end
```

**Response:**

```javascript
{
    "status":"ok"
    "data": {
        "oxd_id": "f484c218-72a9-44a6-8a73-98b5814ea12c"
    }
}
```

#### RS Check Access 

`uma_rs_check_access` method is used in the UMA Resource Server to check the access to the resource.

**Parameters:**

- oxd_id: oxd ID from client registration
- rpt: Requesting Party Token (blank value if absent)
- path: Path of the resource to be checked 
- http_method: HTTP methods (POST, GET and PUT)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```ruby
def check_access
  @uma_command.uma_rs_check_access('/photoz', 'GET')
end
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
    "access":"denied",
    "www-authenticate_header":"UMA realm=\"rs\",
                               as_uri=\"https://as.com\",
                               error=\"insufficient_scope\",
                               ticket=\"289bc54c-72a1-41e2-946a-818cead78fa0\"",
    "ticket":"289bc54c-72a1-41e2-946a-818cead78fa0"
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

#### Introspect RPT

`introspect_rpt` method is used to gain information about obtained RPT

**Parameters:**

- oxd_id: oxd ID from client registration
- rpt: Requesting Party Token
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```ruby
def introspect_rpt
  @uma_command.introspect_rpt
end
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

#### RP Get RPT 

The method uma_rp_get_rpt is called in order to obtain the RPT (Requesting Party Token).

**Parameters:**

- oxd_id: oxd ID from client registration
- ticket: Client Access Ticket generated by uma_rs_check_access method
- claim_token: (Optional) A package of claims provided by the client to the authorization server through claims pushing
- claim_token_format: (Optional) A string containing directly pushed claim information in the indicated format. Must be base64url encoded unless otherwise specified.
- pct: (Optional) Persisted Claims Token
- rpt: (Optional) Requesting Party Token
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- state: (Optional) State that is returned from uma_rp_get_claims_gathering_url method
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)


**Request:**

```ruby
def get_rpt
  @uma_command.uma_rp_get_rpt( claim_token: claim_token, claim_token_format: claim_token_format, pct: pct, rpt: rpt, scope: scope, state: state )
end
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

#### RP Get Claims Gathering URL 

**Parameters:**

- oxd_id: oxd ID from client registration
- ticket: Client Access Ticket generated by uma_rs_check_access method
- claims_redirect_uri: The URI to which the client wishes the authorization server to direct the requesting party’s user agent after completing its interaction
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```ruby
def get_claims_gathering_url
  @uma_command.uma_rp_get_claims_gathering_url('/photo')
end
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

## Sample Project

Download a [Sample Project](https://github.com/GluuFederation/oxd-ruby-demo-app) specific to this oxd-ruby library.

### Software Requirements

System Requirements:

- Ubuntu / Debian / CentOS / RHEL / Windows Server 2008 or higher
- Ruby 2.4.0
- Rails
- Passenger
- Apache 2.4.4 or higher

To use the oxd-ruby library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google.
- An active installation of the [oxd-server](../../../install/index.md) running on the same server as the client application.
- If you want to make RESTful (https) calls from your app to your `oxd-server`, you will need an active installation of the [oxd-https-extension](../../../oxd-https/start/index.md)).
- A Windows server or Windows installed machine / Linux server or Linux installed machine.

### Install oxd-ruby from RubyGems

To install gem, add this line to your application's Gemfile:

```ruby
gem 'oxd-ruby', '~> 1.0.0'
```

Run bundle command to install it:

```bash
$ bundle install
```

### Configure the Client Application

After you installed oxd-ruby, you need to run the generator command to generate the configuration file:

```bash
$ rails generate oxd:config
```

The generator will install `oxd_config.rb` initializer file in `config/initializers` directory which conatins all the global configuration options for oxd-ruby plguin. The generated configuration file looks like this:

```ruby
Oxd.configure do |config|
  config.oxd_host_ip                       = '127.0.0.1'
  config.oxd_host_port                     = 8099
  config.op_host                           = "https://your.openid.provider.com"
  config.client_id                         = ""
  config.client_secret                     = ""
  config.client_name                       = "Gluu oxd Sample Client"
  config.op_discovery_path                 = ""
  config.authorization_redirect_uri        = "https://domain.example.com/callback"
  config.post_logout_redirect_uri          = "https://domain.example.com/logout"
  config.claims_redirect_uri               = ["https://domain.example.com/claims"]
  config.scope                             = ["openid","profile", "email", "uma_protection","uma_authorization"]
  config.grant_types                       = ["authorization_code","client_credenitals"]
  config.application_type                  = "web"
  config.response_types                    = ["code"]
  config.acr_values                        = ["basic"]
  config.client_jwks_uri                   = ""
  config.client_token_endpoint_auth_method = ""
  config.client_request_uris               = []
  config.contacts                          = ["example-email@gmail.com"]
  config.client_frontchannel_logout_uris   = ['https://domain.example.com/logout']
  config.connection_type                   = "local"
  config.dynamic_registration              = true
end
```

- Your client application must have a valid SSL certificate, so the URL includes: `https://`
- The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP address. You can configure the hostname by adding the following entry in the host file:

    **Linux**

    ```bash
    $ sudo nano /etc/hosts
    ```

    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host`

    Add these lines in virtual host file:

    `127.0.0.1  www.client.example.com`
    `127.0.0.1  client.example.com`

- Enable the virtual host by adding the following lines on virtual host file of Apache in the location:

    **Linux**
    
    `/etc/apache2/sites-available/000-default.conf`
    
    **Windows**
    
    `C:/apache/conf/extra/httpd-vhosts.conf`

```
<VirtualHost *:80>
    ServerName client.example.com
    ServerAlias client.example.com
    #ServerAdmin webmaster@localhost
    DocumentRoot /var/www/oxd-ruby-demo-app/public
    PassengerRuby "<path to installed ruby-2.4.0>"
    RailsEnv development
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    <Directory "/var/www/oxd-ruby-demo-app/public">
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>
<IfModule mod_ssl.c>
    <VirtualHost *:443>
        ServerName client.example.com
        ServerAlias client.example.com
        DocumentRoot /var/www/oxd-ruby-demo-app/public
        PassengerRuby "<path to installed ruby-2.4.0>"
        RailsEnv development

        LogLevel info ssl:warn
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        #   SSL Engine Switch:
        #   Enable/Disable SSL for this virtual host.
        SSLEngine on
        SSLCertificateFile  "<Path to your ssl certificate file>"
        SSLCertificateKeyFile "<Path to your ssl certificate key file>"
        <Directory "/var/www/oxd-ruby-demo-app/public">
                Options FollowSymLinks
                Require all granted
        </Directory>
    </VirtualHost>
</IfModule>
```

Then enable client.example.com virtual host by running:

  **Linux**

  ```bash
    $ sudo a2ensite 000-default.conf 
  ```
Reload the apache server

**Linux**

```bash
$ sudo service apache2 restart
```

For Windows, just start the XAMPP/WAMP server you are using
 
## Support

Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.