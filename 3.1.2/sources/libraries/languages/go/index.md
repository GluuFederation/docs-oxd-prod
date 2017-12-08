# oxd-go

## Overview

Use oxd's Golang library to send users from a Golang application to your Gluu Server OpenID Connect Provider (OP) for dynamic enrollment, single sign-on (SSO), strong authentication, and access management policy enforcement. 

## Sample Code

### OpenID Connect

#### Setup Client

In order to use an OpenID Connect Provider (OP) for login, 
you need to setup your client application at the OpenID Connect Provider (OP). 
During setup, oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful setup, the oxd-server will assign a unique oxd ID, return a Client ID and Client Secret. This Client ID and Client Secret can be used for `GetClientToken` method. If your OpenID Connect Provider (OP) does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be passed to the `CallOxdServer` method as a parameter. The Setup Client method is a one time task to configure a client in the oxd-server and OpenID Connect Provider (OP).

**Parameters:**

- AuthorizationRedirectUri: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- OpHost: URL of the OpenID Connect Provider (OP)
- PostLogoutRedirectUri: (Optional) URL to which the user is redirected to after successful logout
- application_type: (Optional) Application type, the default values are native or web. The default, if omitted, is web.
- ResponseTypes: (Optional) Determines the authorization processing flow to be used
- GrantType: (Optional) Grant types that the client is declaring that it will restrict itself to using
- Scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- AcrValues: (Optional) Custom authentication script from the Gluu server.  Required for extended authentication.
- ClientName: (Optional) Client application name
- ClientJwksUri: (Optional) URL for the client's JSON Web Key Set (JWKS) document
- ClientTokenEndpointAuthMethod: (Optional) Requested client authentication method for the Token Endpoint
- ClientRequestUris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OpenID Connect Provider (OP)
- ClientFrontChannelLogoutUris: (Optional) Client application Logout URL
- ClientSectorIdentifierUri: (Optional) URL using the HTTPS scheme to be used in calculating pseudonymous identifiers by the OpenID Connect Provider (OP)
- Contacts: (Optional) Array of e-mail addresses of people responsible for this client
- UiLocales: (Optional) End user's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- ClaimsLocales: (Optional) End user's preferred languages and scripts for claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- ClientId: (Optional) Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- ClientSecret: (Optional) Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- ClaimsRedirecturi: (Optional) The URI to which the client wishes the authorization server to direct the requesting party user agent after completing its interaction
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension


**Request:**

```go
import (
    "fmt"
    "net/http"
    "oxd-client-demo/conf"
    "oxd-client/model/params/registration"
    "oxd-client/client"
    "oxd-client/constants"
    "oxd-client/model/transport"
    "oxd-client-demo/service"
    "oxd-client-demo/utils"
)

var oxdResponse transport.OxdResponse

var request model.SetupClientRequestParams

request.OpHost = opHost
request.AuthorizationRedirectUri = authorizationRedirectUrl
request.PostLogoutRedirectUri = postLogoutRedirectUrl
request.ClientName = client_name
request.Scope = scope
request.GrantType = grantType
OxdHost := fmt.Sprint(configuration.Host+":"+OxdPort)
request.ClientId = clientid
request.ClientSecret = clientsecret
page.CallOxdServer(
    client.BuildOxdRequest(constants.SETUP_CLIENT,request),
    &oxdResponse,
    OxdHost)

var response model.SetupClientResponseParams

oxdResponse.GetParams(&response)
var oxdId := response.OxdId
var clientId := response.ClientId
var clientSecret := response.ClientSecret
```

**Response:**

```javascript
{
  "status": "ok",
  "data": {
    "oxd_id": "6F9619FF-8B86-D011-B42D-00CF4FC964FF",
    "op_host": "https://<idp-hostname>",
    "client_id": "@!E64E.B7E6.3AC4.6CB9!0001!C05E.F402!0008!98F7.EB7B.6213.6527",
    "client_secret": "173d55ff-5a4f-429c-b50d-7899b616912a",
    "client_registration_access_token": "f8975472-240a-4395-b96d-6ef492f50b9e",
    "client_registration_client_uri": "https://iam310.centroxy.com/oxauth/restv1/register?client_id=@!E64E.B7E6.3AC4.6CB9!0001!C05E.F402!0008!98F7.EB7B.6213.6527",
    "client_id_issued_at": 1504353408,
    "client_secret_expires_at": 1504439808
  }
}
```


#### Get Client Token

The `CallOxdServer` method is used to get a token which is sent as an input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Parameters:**

- ClientID: Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- ClientSecret: Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- OpHost: URL of the OpenID Connect Provider (OP)
- OpDiscoveryPath: (Optional) Path to discovery document. For example if it's https://client.example.com/.well-known/openid-configuration then path is blank. But if it is https://client.example.com/oxauth/.well-known/openid-configuration then path is oxauth
- Scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- OxdId:oxd ID from client registration.
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension


**Request:**

```go
import (
	"net/http"
	"oxd-client-demo/conf"
	"oxd-client/client"
	"oxd-client/model/params/token"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client-demo/service"
	"github.com/juju/loggo"
)

var oxdResponse transport.OxdResponse
var request model.GetClientTokenRequestParams
request.OxdId = oxdId
request.ClientID = clientId
request.ClientSecret = clientSecret
request.OpHost = opHost 
page.CallOxdServer(
    client.BuildOxdRequest(constants.GET_CLIENT_TOKEN,request),
    &oxdResponse,
    configuration.Host)		 

var response model.GetClientTokenResponseParams
oxdResponse.GetParams(&response)
var response = &response

var accessToken := response.AccessToken
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


#### Register Site

In order to use an OpenID Connect Provider (OP) for login, 
you need to register your client application at the OpenID Connect Provider (OP). 
During registration oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful registration, a unique 
identifier will be issued by the oxd-server. If your OpenID Connect Provider (OP) does not support dynamic registration (like Google), you will need to obtain a 
ClientID and Client Secret which can be passed to the `CallOxdServer` method as a 
parameter. The Register Site method is a one time task to configure a client in the 
oxd-server and OpenID Connect Provider (OP).

!!! Note: 
    The `Register Site` endpoint is not required if client is registered using `Setup Client`

**Parameters:**

- AuthorizationRedirectUri: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- OpHost: URL of the OpenID Connect Provider (OP)
- PostLogoutRedirectUri: (Optional) URL to which the user is redirected to after successful logout
- application_type: (Optional) Application type, the default values are native or web. The default, if omitted, is web.
- ResponseTypes: (Optional) Determines the authorization processing flow to be used
- GrantType: (Optional) Grant types that the client is declaring that it will restrict itself to using
- Scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- AcrValues: (Optional) Custom authentication script from the Gluu server.  Required for extended authentication.
- ClientName: (Optional) Client application name
- ClientJwksUri: (Optional) URL for the client's JSON Web Key Set (JWKS) document
- ClientTokenEndpointAuthMethod: (Optional) Requested client authentication method for the Token Endpoint
- ClientRequestUris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OpenID Connect Provider (OP)
- ClientFrontChannelLogoutUris: (Optional) Client application Logout URL
- ClientSectorIdentifierUri: (Optional) URL using the HTTPS scheme to be used in calculating pseudonymous identifiers by the OpenID Connect Provider (OP)
- Contacts: (Optional) Array of e-mail addresses of people responsible for this client
- UiLocales: (Optional) End user's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- ClaimsLocales: (Optional) End user's preferred languages and scripts for claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- ClientId: (Optional) Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- ClientSecret: (Optional) Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- ClaimsRedirecturi: (Optional) The URI to which the client wishes the authorization server to direct the requesting party user agent after completing its interaction
- ProtectionAccessToken: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension


**Request:**

```go
import (
	"net/http"
	"oxd-client-demo/conf"
	"oxd-client/client"
	"oxd-client/model/params/registration"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client-demo/service"
	"oxd-client-demo/utils"
)

var oxdResponse transport.OxdResponse
	
var request model.RegisterSiteRequestParams

request.OpHost = opHost
request.AuthorizationRedirectUri = authorizationRedirectUrl
request.PostLogoutRedirectUri = postLogoutRedirectUrl
request.ClientName = client_name
request.Scope = scope
request.GrantType = grantType

page.CallOxdServer(
    client.BuildOxdRequest(constants.REGISTER_SITE,request),
    &oxdResponse,
    configuration.Host)

var response model.RegisterSiteResponseParams

oxdResponse.GetParams(&response)
var oxdId := response.OxdId
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


#### Update Site Registration

The `CallOxdServer` method can be used to update an existing client in the OpenID Connect Provider (OP). 
Fields like Authorization Redirect URL, Post Logout URL, Scope, Client Secret and other fields can be updated using this method.

**Parameters:**

- OxdId: oxd ID from client registration
- AuthorizationRedirectUri: (Optional) URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- PostLogoutRedirectUri: (Optional) URL to which the RP is requesting the end user's user agent be redirected to after a logout has been performed
- ResponseTypes: (Optional) Determines the authorization processing flow to be used
- GrantType: (Optional) Grant types the client is restricting itself to using
- Scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- AcrValues: (Optional) Custom authentication script from the Gluu server. Required for extended authentication.
- ClientName: (Optional) Client application name
- ClientSecretExpiresAt: (Optional) Used to extend client lifetime (milliseconds since 1970)
- ClientJwksUri: (Optional) URL for the client's JSON Web Key Set (JWKS) document
- ClientTokenEndpointAuthMethod: (Optional) Requested client authentication method for the Token Endpoint
- ClientRequestUris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OpenID Connect Provider (OP)
- ClientFrontChannelLogoutUris: (Optional) Client application Logout URL
- ClientSectorIdentifierUri: (Optional) URL using the HTTPS scheme to be used in calculating pseudonymous identifiers by the OpenID Connect Provider (OP)
- Contacts: (Optional) Array of e-mail addresses of people responsible for this client
- ui_locales: (Optional) End user's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- ClaimsLocales: (Optional) End user's preferred languages and scripts for claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference
- ProtectionAccessToken: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension


**Request:**

```go
import (
	"net/http"
	"oxd-client-demo/conf"
	"oxd-client-demo/service"
	"oxd-client/client"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client/model/params/registration"
	
)

var oxdResponse transport.OxdResponse

var request model.UpdateSiteRequestParams

request.OpHost = ophostId
request.AuthorizationRedirectUri = authorizationRedirectUrl
request.PostLogoutRedirectUri = postLogoutRedirectUrl
request.ClientId = clientId
request.ClientSecret = clientSecret
request.Scope = scope
request.GrantType = grantType
request.OxdId = oxdID
request.ProtectionAccessToken  = accesstoken


page.CallOxdServer(
	client.BuildOxdRequest(constants.UPDATE_SITE,request),
	&oxdResponse,
	Host)

oxdResponse.GetParams(&response)
var oxdId := response.OxdId
```

**Response:**

```javascript
{
    "status":"ok"
}
```


#### Get Authorization URL

The `CallOxdServer` method returns the OpenID Connect Provider (OP) 
Authentication URL to which the client application must redirect the user to 
authorize the release of personal data. The Response URL includes state value, 
which can be used to obtain tokens required for authentication. This state value is used 
to maintain state between the request and the callback.

**Parameters:**

- OxdId: oxd ID from client registration
- Scope: (Optional) A scope is an indication by the client that it wants to access some resource
- AcrValues: (Optional) Custom authentication script from the Gluu server.  Required for extended authentication. 
- Prompt: (Optional) Values that specifies whether the Authorization Server prompts the end user for reauthentication and consent
- CustomParameters: (Optional) Custom parameters
- ProtectionAccessToken: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension

**Request:**

```go

import (
	"net/http"
	"oxd-client-demo/conf"
	"oxd-client/client"
	"oxd-client/model/params/url"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client-demo/service"
	"github.com/juju/loggo"
)

var oxdResponse transport.OxdResponse
row := model.AuthorizationUrlRequestParams{
    CustomParameters: make(map[string]string),
}

row.CustomParameters["param1"] = "value1"
row.CustomParameters["param2"] = "value2"
row.OxdId = oxdId
row.ProtectionAccessToken = accesstoken

page.CallOxdServer(
    client.BuildOxdRequest(constants.GET_AUTHORIZATION_URL,
        row),
    &oxdResponse,
    Host)


var response model.AuthorizationUrlResponseParams
oxdResponse.GetParams(&response)

var authUrl := response.AuthorizationUrl
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


#### Get Tokens by Code

Upon successful login, the login result will return code and state. `CallOxdServer` method 
uses code and state to retrieve token which can be used to access user claims.

**Parameters:**

- OxdId: oxd ID from client registration
- Code: The code from OpenID Connect Provider (OP) Authorization Redirect URL
- State: The state from OpenID Connect Provider (OP) Authorization Redirect URL
- ProtectionAccessToken: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension

**Request:**

```go

import (
	"net/http"
	"oxd-client-demo/conf"
	"oxd-client/client"
	"oxd-client/model/params/token"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client-demo/service"
	"strings"	
)

var oxdResponse transport.OxdResponse
page.CallOxdServer(
    client.BuildOxdRequest(constants.GET_TOKENS_BY_CODE,
        model.TokensByCodeRequestParams{OxdId, protectionAccessToken, Code, State }),
    &oxdResponse,
    Host)

var response model.TokensByCodeResponseParams
oxdResponse.GetParams(&response)

var response := response.AccessToken
var idToken := response.IdToken
var refreshToken := response.RefreshToken
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


#### Get Access Token by Refresh Token

The `CallOxdServer` method is used to get a new access token and a new refresh token by using the refresh token which is obtained from `getTokensByCode` method.

**Parameters:**

- OxdId: oxd ID from client registration
- RefreshToken: Obtained from the get_tokens_by_code method
- Scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- ProtectionAccessToken: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension

**Request:**

```go
import (
	"net/http"
	"oxd-client-demo/conf"
	"oxd-client/client"
	"oxd-client/model/params/token"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client-demo/service"
	"strings"	
)

var oxdResponse transport.OxdResponse
page.CallOxdServer(
    client.BuildOxdRequest(constants.GET_ACCESS_TOKEN_BY_REFRESH_TOKEN,
        model.GetAccessTokenByRefreshTokenRequestParams{OxdId , RefreshToken , protectionAccessToken}),

    &oxdResponse,
    Host)


var response model.GetAccessTokenByRefreshTokenResponseParams
oxdResponse.GetParams(&response)

var accessToken := response.AccessToken
var refreshToken := response.RefreshToken
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

#### Get User Info

Once the user has been authenticated by the OpenID Connect Provider (OP), 
the `CallOxdServer` method returns the claims (First Name, Last Name, E-Mail ID, etc.) about the authenticated end user.

**Parameters:**

- OxdId: oxd ID from client registration
- AccessToken: access_token from GetTokenByCode or GetAccessTokenbyRefreshToken
- ProtectionAccessToken: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension

**Request:**

```go

import (
	"net/http"
	"oxd-client-demo/conf"
	"oxd-client/client"
	"oxd-client/model/params/token"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client-demo/service"
	"strings"
)

var oxdResponse transport.OxdResponse
		
page.CallOxdServer(
    client.BuildOxdRequest(constants.GET_USER_INFO,model.UserInfoRequestParams{OxdId, accesstoken, protectionAccessToken}),
    &oxdResponse,
    Host)

var response model.UserInfoResponseParams
oxdResponse.GetParams(&response)

username := response.Claims["name"]
email := response.Claims["email"]
usernameString := strings.Join(username," ")
emailString := strings.Join(email," ")

var name := usernameString
var email := emailString
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


#### Logout

`CallOxdServer` method returns the OpenID Connect Provider (OP) Logout URL. 
Client application uses this Logout URL to end the user session.

**Parameters:**

- OxdId: oxd ID from client registration
- IdTokenHint: (Optional) ID Token previously issued by the Authorization Server being passed as a hint about the end user's current or past authenticated session with the client
- PostLogoutRedirectUri: (Optional) URL to which user is redirected to after successful logout
- State: (Optional) Value used to maintain state between the request and the callback
- SessionState: (Optional) JSON string that represents the end users login state at the OpenID Connect Provider (OP)
- ProtectionAccessToken: Generated from GetClientTokenPageSite method (Optional, required if oxd-https-extension is used)
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension

**Request:**

```go

import (
	"net/http"
	"oxd-client-demo/conf"
	"oxd-client-demo/service"
	"oxd-client/client"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client/model/params/url"
)

var oxdResponse transport.OxdResponse
page.CallOxdServer(
    client.BuildOxdRequest(constants.GET_LOGOUT_URI,
        model.LogoutUrlRequestParams{OxdId, accesstoken, idToken, "", state, ""}),
    &oxdResponse,
    Host)
var response model.LogoutUrlResponseParams
oxdResponse.GetParams(&response)

var logoutUrl := response.Uri
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


### UMA

#### RS Protect

`CallOxdServer` method is used for protecting resources by the Resource Server. The Resource Server is needed to construct the command which will protect the resource.
The command will contain an API path, HTTP methods (POST, GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy is mapped, CheckAccessPage method will always return access as granted. For more information about uma_rpt_policies you can reference this [document](https://gluu.org/docs/oxd/3.1.1/api/#uma-2-client-apis).

**Parameters:**

- OxdId: oxd ID from client registration
- Resources: One or more protected resources that a resource server manages, abstractly, as a set. In authorization policy terminology, a resource set is the "object" being protected. 
- ProtectionAccessToken: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension


**Request:**

```go
import (
	"net/http"
	"oxd-client-demo/conf"
	"oxd-client/client"
	"oxd-client/model/params/uma"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client/model/params/uma/protect"
	"oxd-client-demo/service"
	"oxd-client-demo/utils"
)
var oxdResponse transport.OxdResponse
requestParams := uma.RsProtectRequestParams{OxdId, []protect.RsResource{ protect.RsResource{configuration.Path, configuration.Condition}}, accesstoken}

page.CallOxdServer(
	client.BuildOxdRequest(constants.RS_PROTECT,requestParams),
	&oxdResponse,
	Host)

var response uma.RsProtectResponseParams

oxdResponse.GetParams(&response)

var status := response.Status
```

**Response:**

```javascript
{
    "status":"ok"
}
```


#### RS Check Access 

`CallOxdServer` method is used in the UMA Resource Server to check the access to the resource.

**Parameters:**

- OxdId: oxd ID from client registration
- Rpt: Requesting Party Token
- Path: Path of the resource to be checked 
- HttpMethod: HTTP methods (POST, GET and PUT)
- ProtectionAccessToken: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension

**Request:**

```go

import (
	"net/http"
	"fmt"
	"oxd-client-demo/conf"
	"oxd-client/client"
	"oxd-client/model/params/uma"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client-demo/service"
	"oxd-client-demo/utils"
)

var oxdResponse transport.OxdResponse
var requestParams uma.RsCheckAccessRequestParams
requestParams.OxdId = OxdId
requestParams.Path = "/scim" 
requestParams.Rpt = " "
requestParams.HttpMethod = "GET"
requestParams.ProtectionAccessToken = protectionAccessToken
page.CallOxdServer(
	client.BuildOxdRequest(constants.RS_CHECK_ACCESS,requestParams),
	&oxdResponse,
	Host)

var response uma.RsCheckAccessResponseParams
oxdResponse.GetParams(&response)

var umaTicket := response.Ticket
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
        "access":"denied"
        "www-authenticate_header":"UMA realm=\"example\",
                                   as_uri=\"https://as.example.com\",
                                   error=\"insufficient_scope\",
                                   ticket=\"016f84e8-f9b9-11e0-bd6f-0021cc6004de\"",
        "ticket":"016f84e8-f9b9-11e0-bd6f-0021cc6004de"
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

***Resource is not Protected Response:***

```javascript
{
    "status":"error",
    "data":{
        "error":"invalid_request",
        "error_description":"Resource is not protected. Please protect your resource first with uma_rs_protect command."
    }
}
```


#### RP Get RPT 

The method `CallOxdServer` is called in order to obtain the RPT (Requesting Party Token). 

**Parameters:**

- OxdId: oxd ID from client registration
- Ticket: Client Access Ticket generated by CheckAccessPage method
- ClaimToken: (Optional) A package of claims provided by the client to the authorization server through claims pushing
- ClaimTokenFormat: (Optional) A string containing directly pushed claim information in the indicated format. Must be base64url encoded unless otherwise specified.  
- Pct: (Optional) Persisted Claims Token
- Rpt: (Optional) Requesting Party Token
- Scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- State: (Optional) State that is returned from RpGetClaimsGatheringUrlPage method
- ProtectionAccessToken: Generated from GetClientTokenPageSite method (Optional, required if oxd-https-extension is used)
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension

**Request:**

```go
import (
	"net/http"
	"oxd-client-demo/conf"
	"oxd-client/client"
	"oxd-client/model/params/uma"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client-demo/service"
	"oxd-client-demo/utils"
)

var oxdResponse transport.OxdResponse
var requestParams uma.RpGetRptRequestParams
requestParams.OxdId = OxdId
requestParams.Ticket = UMATicket 
requestParams.ProtectionAccessToken = accesstoken
page.CallOxdServer(
	client.BuildOxdRequest(constants.RP_GET_RPT,requestParams),
	&oxdResponse,
	Host)

var response uma.RpGetRptResponseParams
oxdResponse.GetParams(&response)

return response
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

- OxdId: oxd ID from client registration
- Ticket: Client Access Ticket generated by CheckAccessPage method
- ClaimsRedirectURI: The URI to which the client wishes the authorization server to direct the requesting partys user agent after completing its interaction
- ProtectionAccessToken: Generated from GetClientTokenPageSite (Optional, required if oxd-https-extension is used)
- OxdHost: The IP address of the oxd-server
- OxdPort: The port of the oxd-server
- HttpRestUrl: (Optional) URL of the oxd-https-extension

**Request:**

```go
import (
	"net/http"
	"oxd-client-demo/conf"
	"oxd-client/client"
	"oxd-client/model/params/uma"
	"oxd-client/constants"
	"oxd-client/model/transport"
	"oxd-client-demo/service"
	"oxd-client-demo/utils"
)

var oxdResponse transport.OxdResponse
var requestParams uma.RpGetClaimsGatheringUrlRequestParams
requestParams.OxdId = OxdId
requestParams.Ticket = umaTicket
requestParams.ClaimsRedirectURI = "https://client.example.com"
requestParams.ProtectionAccessToken = protectionAccessToken

page.CallOxdServer(
	client.BuildOxdRequest(constants.RP_GET_CLAIMS_GATHERING_URL,requestParams),
	&oxdResponse,
	Host)

var response uma.RpGetClaimsGatheringUrlResponseParams
oxdResponse.GetParams(&response)

return response
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

Download a [Sample Project](https://github.com/GluuFederation/oxd-go/archive/3.1.1.zip) specific to this oxd-go library.


### Software Requirements

System Requirements:

- Ubuntu / Debian / CentOS / RHEL / Windows Server 2008 or higher
- Go 1.9

To use the oxd-go library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google.    
- An active installation of the [oxd-server](../../../install/index.md) running on the same server as the client application.
- If you want to make RESTful (https) calls from your app to your `oxd-server`, you will need an active installation of the [oxd-https-extension](../../../oxd-https/start/index.md)).
- A Windows server or Windows installed machine / Linux server or Linux installed machine.



### Configure the Client Application

- Client application must have a valid SSL certificate, so the URL includes: `https://`    
    
- The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP address. You can configure the hostname by adding the following entry in the host file:

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`

- To start the demo, launch run.sh script, which is present in the oxd-client-demo folder of the src directory.
- To compile the demo app, run the following command in the command prompt:
```
 cd <path to the project directory>
 go install oxd-client-demo
```
- In order to run the demo app in the browser, run the following command in the command prompt:

```
 .\bin\oxd-client-demo.exe
```
- Open a browser and type https://client.example.com:8080/,  this will open the homepage of the demo app.

- Enable SSL using the following instructions:

    - Open command prompt and navigate to the GO Workspace directory like `C:\projects\Go`.
    - You have to generate public/private key pair (params: key and cert) in the project directory.
    - To generate public/private key pair, run the following command:
    
    **Windows**

  ```
  go run %GOROOT%/src/crypto/tls/generate_cert.go -host="127.0.0.1"
  ```
  
  **Linux**

```
  go run $GOROOT/src/crypto/tls/generate_cert.go -host="127.0.0.1"
```

- Enable SSL by	adding the following lines in the virtual host file of Apache in the location:

	**Linux**
    
    `/etc/apache2/sites-available/000-default.conf`
    
	**Windows**
    
    `C:/apache/conf/extra/httpd-vhosts.conf`

```
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

- With the oxd-server running, navigate to the URL's below to run the sample client application. To register a client in the oxd-server use the Setup Client URL. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, navigate to the Login URL for authentication.
    - Setup Client URL: https://client.example.com:8080/settings.html
    - Login URL: https://client.example.com:8080/
    - UMA URL: https://client.example.com:8080/UMA.html

- Plugin description:
    - src/oxd-client - client source code
    - src/oxd-client-demo - simple communication demo
    - src/oxd-client-test - unit tests for communication

- The input values used during Setup Client are stored in a configuration file (oxd_config.json). Therefore, the configuration file needs to be writable by the client application.   

## Support

Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.
