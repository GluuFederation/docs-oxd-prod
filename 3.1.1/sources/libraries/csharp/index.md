# oxd-csharp
## Overview
The following documentation demonstrates how to use 
the [oxd Client Software](http://oxd.gluu.org) C# library to send users from a 
C# web application to an OpenID Connect Provider (OP), 
like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 

## Sample Project
Download the [Sample Project](https://github.com/GluuFederation/oxd-csharp/archive/3.1.1.zip) specific to this oxd C# library.

### System Requirements

- Microsoft Visual Studio 2008 or higher
- Windows 7 or higher / Windows Server 2008 or higher
- Gluu.Oxd.OxdCSharp  3.1.1


## Prerequisites

### Required Software
To use the oxd C# library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/) or Google.   
- An active installation of the [oxd-server](../../install/index.md)
running in the same server as the client application.
- An active installation of the [oxd-https-extension](../../install/index.md) if oxd-https-extension connection is used. In this case, client applications can be on different servers but will be able to access oxd-https-extension.
- A Windows server or Windows installed machine meeting system requirements.

### Install the Gluu.Oxd.OxdCSharp NuGet Package

Use the NuGet Package Manager Console of Visual Studio
`Tools` > `NuGet Package Manager` > `Package Manager Console` to 
install the Gluu.Oxd.OxdCSharp package, by running the following command:

`PM> Install-Package Gluu.Oxd.OxdCSharp`


### Configure the Client Application

- Your client application must have a valid SSL certification, so the URL includes: `https://`    
- The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP Address. 
You can configure the `hostname` by adding the following 
entry in `C:\Windows\System32\drivers\etc\host` file:

    `127.0.0.1  client.example.com`

- Download the [Sample Project](https://github.com/GluuFederation/oxd-csharp/archive/3.1.1.zip) specific to this oxd-csharp library.
- Open the Sample Project in Visual Studio

- Enable SSL using the following instructions:

    - Open the Client application in Visual Studio.
    - Go to Client Application Properties.
    - Navigate to `Development Server` and set `SSL Enabled` to `True`.

- Change the `hostname` in the project using the following instructions:

     - Make hidden folders visible in the windows explorer.(If already done then ignore it).
     - Navigate to `vs/config` folder in the root of the project in the windows explorer.
     - Open the `applicationhost.config` file.
     - Add the following lines to `bindings` section of the project:

     ```xml
     <binding protocol="https" bindingInformation="*:44383:client.example.com" />
     <binding protocol="http" bindingInformation="*:1329:client.example.com" />
     ```
     - After adding the aforementioned lines the binding section will look like this:
     
    ```xml
     <site name="GluuDemoWebsite" id="2">
                <application path="/" applicationPool="Clr4IntegratedAppPool">
                    <virtualDirectory path="/" physicalPath="<path of the project>" />
                </application>
                <bindings>
    <binding protocol="https" bindingInformation="*:44383:localhost" />
                    <binding protocol="http" bindingInformation="*:1329:localhost" />
                  <binding protocol="https" bindingInformation="*:44383:client.example.com" />
<binding protocol="http" bindingInformation="*:1329:client.example.com" /></bindings>
            </site>
      
      
- With the oxd-server running, navigate to the URL's below to run the sample client application. To register a client in the oxd-server use the Setup Client URL. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, navigate to the Login URL for authentication.
    - Setup Client URL: https://client.example.com:44383/setupClient
    - Login URL: https://client.example.com:44383
    - UMA URL: https://client.example.com:44383/uma

- The input values used during Setup Client are stored in a configuration file (oxd_config.json). Therefore, the configuration file needs to be writable by the Client application.


## Endpoints (oxd-server and oxd-https-extension)

The oxd server provides the following methods for authenticating users with an OpenID Connect Provider (OP):

 Available OpenId Connect Endpoints 
  - [Setup Client](../../protocol/#setup-client)  
  - [Get Client Token](../../protocol/#get-client-token)
  - [Register Site](../../protocol/#register-site) 
  - [Update Site Registration](../../protocol/#update-site-registration)    
  - [Get Authorization URL](../../protocol/#get-authorization-url)   
  - [Get Tokens by Code](../../protocol/#get-tokens-id-access-by-code)
  - [Get Access Token by Refresh Token](../../protocol/#get-access-token-by-refresh-token)    
  - [Get User Info](../../protocol/#get-user-info)   
  - [Get Logout URI](../../protocol/#log-out-uri)

 Available UMA (User Managed Access) Endpoints 

  - [UMA RS Protect](../../protocol/#uma-rs-protect) 
  - [UMA RS Check Access](../../protocol/#uma-rs-check-access) 
  - [UMA RP Get RPT](../../protocol/#uma-rp-get-rpt) 
  - [UMA RP Get Claims Gathering URL](../../protocol/#uma-rp-get-claims-gathering-url) 


## Sample Code

### OpenID Connect

#### Setup Client

In order to use an OpenID Connect Provider (OP) for login, 
you need to setup your client application at the OP. 
During setup oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful setup a unique 
identifier will be issued by the oxd server by assigning a specific oxd id. Along with oxd Id oxd server will also return client Id and client secret. This client Id and client secret can be used for `GetClientToken` method. If your OpenID Connect Provider 
does not support dynamic registration (like Google), you will need to obtain a 
ClientID and Client Secret which can be passed to the `SetupClient` method as a 
parameter. The Setup Client method is a one time task to configure a client in the 
oxd server and OP.


**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional, required if oxd-https-extension is used).
- OpHost: URL of the OpenID Connect Provider (OP)
- redirectUrl: URL to which the OpenID Connect Provider (OP)is authorized to redirect the user to after authorization.
- clientId: Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- clientSecret: Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.

**Request:**

 **Setup Client using oxd-server**


```csharp


public ActionResult SetupClient(string oxdHost, int oxdPort,  string OpHost, string redirectUrl, string ClientId, string clientSecret)
        {
            //prepare input params for Setup client
            var setupClientInputParams = new SetupClientParams()
            {
                AuthorizationRedirectUri = redirectUrl,
                OpHost = OpHost,
                ClientName = "<Your Client Name>",
                Scope = new List<string> { "openid", "profile", "email", "uma_protection", "uma_authorization" },
                GrantTypes = new List<string> { "authorization_code", "client_credentials", "uma_ticket" },
                ClientId = clientId,
                ClientSecret = clientSecret
            };

            var setupClientClient = new SetupClientClient();

            var setupClientResponse = new SetupClientResponse();

            
            setupClientResponse = setupClientClient.SetupClient(oxdHost, oxdPort, setupClientInputParams);


            return Json(new { oxdId = oxd.OxdId, clientId = clientid, clientSecret = clientsecret });
        }
```

**Setup Client using oxd-https-extension**
```csharp


public ActionResult SetupClient( string oxdhttpsurl, string OpHost, string redirectUrl, string ClientId, string clientSecret)
        {
            //prepare input params for Setup client
            var setupClientInputParams = new SetupClientParams()
            {
                AuthorizationRedirectUri = redirectUrl,
                OpHost = OpHost,
                ClientName = "<Your Client Name>",
                Scope = new List<string> { "openid", "profile", "email", "uma_protection", "uma_authorization" },
                GrantTypes = new List<string> { "authorization_code", "client_credentials", "uma_ticket" },
                ClientId = clientId,
                ClientSecret = clientSecret
            };

            var setupClientClient = new SetupClientClient();

            var setupClientResponse = new SetupClientResponse();
            
            setupClientResponse = setupClientClient.SetupClient(oxdhttpsurl, setupClientInputParams);

            return Json(new { oxdId = oxd.OxdId, clientId = clientid, clientSecret = clientsecret });
        }
```

**Response:**

```javascript
{
  "status": "ok",
  "data": {
    "oxd_id": "6F9619FF-8B86-D011-B42D-00CF4FC964FF",
    "op_host": "https://ce-dev3-gluu.org",
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

The `GetClientToken` method is used to get a token which is sent as input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.


**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional, required if oxd-https-extension is used).
- OpHost: OpenID Connect Provider (OP)
- clientId: Client ID from OpenID Connect Provider (OP).  Should be passed with the Client Secret.
- clientSecret: Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.

**Request:**

**GetClientToken using oxd-server**

```csharp
public string GetProtectionAccessToken(string oxdHost, int oxdPort, string OpHost, string ClientId, string clientSecret)
        {
            //prepare input params for Client Registration
var getClientAccessTokenParams = new GetClientTokenParams()
            {
                clientId = clientid,
                clientSecret = clientsecret,
                opHost = OpHost
            };

            var getClientAccessToken = new GetClientTokenClient();

            string protectionAccessToken;
          
            protectionAccessToken = getClientAccessToken.GetClientToken(oxdHost, oxdPort, getClientAccessTokenParams()).Data.accessToken;
            return protectionAccessToken;
        }
```

**GetClientToken using oxd-https-extension**

```csharp
public string GetProtectionAccessToken( string oxdhttpsurl, string OpHost, string ClientId, string clientSecret)
        {
            //prepare input params for Client Registration
var getClientAccessTokenParams = new GetClientTokenParams()
            {
                clientId = clientid,
                clientSecret = clientsecret,
                opHost = OpHost
            };

            var getClientAccessToken = new GetClientTokenClient();

            string protectionAccessToken;
            
            protectionAccessToken = getClientAccessToken.GetClientToken(oxdhttpsurl, getClientAccessTokenParams()).Data.accessToken;

            return protectionAccessToken;
        }
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

In order to use an OpenID Connect Provider (OP) for login, you need to register your client application at the OpenID Connect Provider (OP). During registration oxd will dynamically register the OpenID Connect client and save its configuration. Upon successful registration, a unique identifier will be issued by the oxd server. If your OpenID Connect Provider (OP) does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be passed to the `RegisterSite` method as a parameter. The `RegisterSite` method is a one time task to configure a client in the oxd server and OpenID Connect Provider (OP).

**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional, required if oxd-https-extension is used).
- OpHost: URL of the OpenID Connect Provider (OP)
- redirectUrl: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization.
- protectionAccessToken: Generated from GetClientToken method (Optional, required if oxd-https-extension is used)

**Request:**

**Register Client using oxd-server**

```csharp
 public ActionResult RegisterClient(string oxdHost, int oxdPort, string OpHost, string redirectUrl, string protectionAccessToken)
        {
            //prepare input params for Client Registration
var registerSiteInputParams = new RegisterSiteParams()
            {
                AuthorizationRedirectUri = redirectUrl,
                OpHost = OpHost,
                ClientName = "<Your Client Name>",
                Scope = new List<string> { "openid", "profile", "email" }
                ProtectionAccessToken = protectionAccessToken
            };

             var registerSiteClient = new RegisterSiteClient();

            var registerSiteResponse = new RegisterSiteResponse();


registerSiteResponse = registerSiteClient.RegisterSite(oxdHost, oxdPort, registerSiteInputParams);



//Response
return Json(new { oxdId = registerSiteResponse.Data.OxdId });
        }
```
**Register Client using oxd-https-extension**

```csharp
 public ActionResult RegisterClient(string oxdhttpsurl, string OpHost, string redirectUrl, string protectionAccessToken)
        {
            //prepare input params for Client Registration
var registerSiteInputParams = new RegisterSiteParams()
            {
                AuthorizationRedirectUri = redirectUrl,
                OpHost = OpHost,
                ClientName = "<Your Client Name>",
                Scope = new List<string> { "openid", "profile", "email" }
                ProtectionAccessToken = protectionAccessToken
            };

             var registerSiteClient = new RegisterSiteClient();

            var registerSiteResponse = new RegisterSiteResponse();
            registerSiteResponse = registerSiteClient.RegisterSite(oxdhttpsurl, registerSiteInputParams);

//Response
return Json(new { oxdId = registerSiteResponse.Data.OxdId });
        }
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

The `UpdateSiteRegistration` method can be used to update an existing client in the OP. Fields like Authorization redirect url, post logout url, scope, client secret etc. can be updated using this method.

**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional, required if oxd-https-extension is used).
- oxdId: oxd ID from client registration
- postLogoutRedirectUrl: URL to which the RP is requesting that the End-User's User Agent be redirected to after a logout has been performed.
- protectionAccessToken: Generated from GetClientToken method.

**Request:**

**Update Client using  oxd-server**

```csharp
     public ActionResult Update(string oxdHost, int oxdPort,  string oxdId, string postLogoutRedirectUrl, string protectionAccessToken)
        {
            //prepare input params for Update Site Registration
            var updateSiteInputParams = new UpdateSiteParams()
            {
                OxdId = oxdId,
                Contacts = new List<string> { "support@email.com" },
                PostLogoutRedirectUri = postLogoutRedirectUrl,
                ProtectionAccessToken = protectionAccessToken
            };

            var updateSiteClient = new UpdateSiteRegistrationClient();
            var updateSiteResponse = new UpdateSiteResponse();

           
            updateSiteResponse = updateSiteClient.UpdateSiteRegistration(oxdHost, oxdPort, updateSiteInputParams);

          
            //Response
            return Json(new { status = updateSiteResponse.Status });
        }
```

**Update Client using oxd-https-extension**

```csharp
     public ActionResult Update(string oxdhttpsurl, string oxdId, string postLogoutRedirectUrl, string protectionAccessToken)
        {
            //prepare input params for Update Site Registration
            var updateSiteInputParams = new UpdateSiteParams()
            {
                OxdId = oxdId,
                Contacts = new List<string> { "support@email.com" },
                PostLogoutRedirectUri = postLogoutRedirectUrl,
                ProtectionAccessToken = protectionAccessToken
            };

            var updateSiteClient = new UpdateSiteRegistrationClient();
            var updateSiteResponse = new UpdateSiteResponse();

           
            updateSiteResponse = updateSiteClient.UpdateSiteRegistration(oxdhttpsurl, updateSiteInputParams);

            //Response
            return Json(new { status = updateSiteResponse.Status });
        }
```

**Response:**

```javascript
{
    "status":"ok"
}
```

#### Get Authorization URL

The `GetAuthorizationURL` method returns the OpenID Connect Provider (OP) Authentication URL to which the client application must redirect the user to authorize the release of personal data. The Response URL includes state value, which can be used to obtain tokens required for authentication. This state value is used to maintain the state between the request and the callback.

**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional, required if oxd-https-extension is used).
- oxdId: oxd ID from client registration
- customParams: custom parameters
- protectionAccessToken:  Generated from GetClientToken method (Optional, required if oxd-https-extension is used)

**Request:**

**Get Auth URL using  oxd-server**

```csharp
    public ActionResult GetAuthorizationURL(string oxdHost, int oxdPort, string oxdId, dictionary<string, string> customParams, string protectionAccessToken)
        {
            //prepare input params for Getting Auth URL from a site
            var getAuthUrlInputParams = new GetAuthorizationUrlParams()
            {
                OxdId = oxdId,
                CustomParams = customParams,
                ProtectionAccessToken = protectionAccessToken
            };

            var getAuthUrlClient = new GetAuthorizationUrlClient();
            var getAuthUrlResponse = new GetAuthorizationUrlResponse();

            
            getAuthUrlResponse = getAuthUrlClient.GetAuthorizationURL(oxdHost, oxdPort, getAuthUrlInputParams);

          

            //Response
            return Json(new { authUrl = getAuthUrlResponse.Data.AuthorizationUrl });
        }
```

**Get Auth URL using  oxd-https-extension**

```csharp
    public ActionResult GetAuthorizationURL(string oxdhttpsurl, string oxdId, dictionary<string, string> customParams, string protectionAccessToken)
        {
            //prepare input params for Getting Auth URL from a site
            var getAuthUrlInputParams = new GetAuthorizationUrlParams()
            {
                OxdId = oxdId,
                CustomParams = customParams,
                ProtectionAccessToken = protectionAccessToken
            };

            var getAuthUrlClient = new GetAuthorizationUrlClient();
            var getAuthUrlResponse = new GetAuthorizationUrlResponse();

           
            getAuthUrlResponse = getAuthUrlClient.GetAuthorizationURL(oxdhttpsurl, getAuthUrlInputParams);

            //Response
            return Json(new { authUrl = getAuthUrlResponse.Data.AuthorizationUrl });
        }
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

Upon successful login, the login result will return code and state. `GetTokensByCode` uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional, required if oxd-https-extension is used).
- oxdId: oxd ID from client registration
- authCode: The code from OpenID Connect Provider (OP) authorization Redirect URL 
- authState: The state from OpenID Connect Provider (OP) authorization Redirect URL
- protectionAccessToken:  Generated from GetClientToken method (Optional, required if oxd-https-extension is used)

**Request:**

**Get Tokens by Code using oxd-server**

```csharp
     public ActionResult GetTokenByCode(string oxdHost, int oxdPort, string oxdId, string authCode, string authState, string protectionAccessToken)
        {
            //prepare input params for Getting Tokens from a site
            var getTokenByCodeInputParams = new GetTokensByCodeParams()
            {
                OxdId = oxdId,
                Code = authCode,
                State = authState,
                ProtectionAccessToken = protectionAccessToken
            };

            var getTokenByCodeClient = new GetTokensByCodeClient();
            var getTokensByCodeResponse = new GetTokensByCodeResponse();
            getTokensByCodeResponse = getTokenByCodeClient.GetTokensByCode(oxdHost, oxdPort, getTokenByCodeInputParams);
            //Response
            return Json(new { accessToken = getTokensByCodeResponse.Data.AccessToken, refreshToken = getTokensByCodeResponse.Data.RefreshToken });
        }
```

**Get Tokens by Code using oxd-https-extension**

```csharp
     public ActionResult GetTokenByCode( string oxdhttpsurl, string oxdId, string authCode, string authState, string protectionAccessToken)
        {
            //prepare input params for Getting Tokens from a site
            var getTokenByCodeInputParams = new GetTokensByCodeParams()
            {
                OxdId = oxdId,
                Code = authCode,
                State = authState,
                ProtectionAccessToken = protectionAccessToken
            };

            var getTokenByCodeClient = new GetTokensByCodeClient();
            var getTokensByCodeResponse = new GetTokensByCodeResponse();
            getTokensByCodeResponse = getTokenByCodeClient.GetTokensByCode(oxdhttpsurl, getTokenByCodeInputParams);

            //Response
            return Json(new { accessToken = getTokensByCodeResponse.Data.AccessToken, refreshToken = getTokensByCodeResponse.Data.RefreshToken });
        }
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

The `GetAccessTokenByRefreshToken` method is used to get a new access token and a new refresh token by using the refresh token which is obtained from `GetTokensByCode` method.

**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional,required if oxd-https-extension is used).
- oxdId: oxd ID from client registration
- refreshToken: Generated from the GetTokensByCode method
- protectionAccessToken:  Generated from GetClientToken method (Optional, required if oxd-https-extension is used)

**Request:**

**Get Access Token by Refresh Token using oxd-server**

```csharp
     public ActionResult GetAccessTokenByRefreshToken(string oxdHost, int oxdPort, string oxdId, string refreshToken, string protectionAccessToken)
        {
            //prepare input params for Getting Tokens from a site
            var getAccessTokenByRefreshTokenInputParams = new GetAccessTokenByRefreshTokenParams()
            {
                OxdId = oxdId,
                RefreshToken = refreshToken,
                ProtectionAccessToken = protectionAccessToken
            };

            var getTokenByCodeClient = new GetTokensByCodeClient();
            var getAccessTokenByRefreshTokenResponse = new GetAccessTokenByRefreshTokenResponse();
            getAccessTokenByRefreshTokenResponse = getAccessTokenByRefreshTokenClient.GetAccessTokenByRefreshToken(oxdHost, oxdPort, getAccessTokenByRefreshTokenInputParams);

            //Response
            return Json(new { accessToken = getAccessTokenByRefreshTokenResponse.Data.AccessToken, refreshToken = getAccessTokenByRefreshTokenResponse.Data.RefreshToken });
        }
```

**Get Access Token by Refresh Token using oxd-https-extension**

```csharp
     public ActionResult GetAccessTokenByRefreshToken(string oxdhttpsurl, string oxdId, string refreshToken, string protectionAccessToken)
        {
            //prepare input params for Getting Tokens from a site
            var getAccessTokenByRefreshTokenInputParams = new GetAccessTokenByRefreshTokenParams()
            {
                OxdId = oxdId,
                RefreshToken = refreshToken,
                ProtectionAccessToken = protectionAccessToken
            };

            var getTokenByCodeClient = new GetTokensByCodeClient();
            var getAccessTokenByRefreshTokenResponse = new GetAccessTokenByRefreshTokenResponse();
            getAccessTokenByRefreshTokenResponse = getAccessTokenByRefreshTokenClient.GetAccessTokenByRefreshToken(oxdhttpsurl, getAccessTokenByRefreshTokenInputParams);

            //Response
            return Json(new { accessToken = getAccessTokenByRefreshTokenResponse.Data.AccessToken, refreshToken = getAccessTokenByRefreshTokenResponse.Data.RefreshToken });
        }
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

Once the user has been authenticated by the OpenID Connect Provider (OP), the `GetUserInfo` method returns the claims (First Name, Last Name, E-Mail ID, etc.) about the authenticated end user.

**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional,required if oxd-https-extension is used).
- OpHost: URL of the OpenID Connect Provider (OP)
- oxdId: oxd ID from client registration
- accessToken: accessToken from GetTokenByCode
- protectionAccessToken:  Generated from GetClientToken method (Optional, required if oxd-https-extension is used)

**Request:**

**Get User Info using oxd-server**

```csharp
    public ActionResult GetUserInfo(string oxdHost, int oxdPort,  string oxdId, string accessToken, string protectionAccessToken)
        {
            //prepare input params for Getting User Info from a site
            var getUserInfoInputParams = new GetUserInfoParams()
            {
                OxdId = oxdId,
                AccessToken = accessToken,
                ProtectionAccessToken = protectionAccessToken
            };

            var getUserInfoClient = new GetUserInfoClient();
            var getUserInfoResponse = new GetUserInfoResponse();

            getUserInfoResponse = getUserInfoClient.GetUserInfo(oxdHost, oxdPort, getUserInfoInputParams);

            //Response
            var userName = getUserInfoResponse.Data.UserClaims.Name.First();
            var userEmail = getUserInfoResponse.Data.UserClaims.Email == null ? string.Empty : getUserInfoResponse.Data.UserClaims.Email.FirstOrDefault();

            return Json(new { userName = userName, userEmail = userEmail });
        }
```

**Get User Info using oxd-https-extension**

```csharp
    public ActionResult GetUserInfo(string oxdhttpsurl, string oxdId, string accessToken, string protectionAccessToken)
        {
            //prepare input params for Getting User Info from a site
            var getUserInfoInputParams = new GetUserInfoParams()
            {
                OxdId = oxdId,
                AccessToken = accessToken,
                ProtectionAccessToken = protectionAccessToken
            };

            var getUserInfoClient = new GetUserInfoClient();
            var getUserInfoResponse = new GetUserInfoResponse();
           
            getUserInfoResponse = getUserInfoClient.GetUserInfo(oxdhttpsurl, getUserInfoInputParams);

            //Response
            var userName = getUserInfoResponse.Data.UserClaims.Name.First();
            var userEmail = getUserInfoResponse.Data.UserClaims.Email == null ? string.Empty : getUserInfoResponse.Data.UserClaims.Email.FirstOrDefault();

            return Json(new { userName = userName, userEmail = userEmail });
        }
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

`GetLogoutURL` method returns the OpenID Connect Provider (OP) Logout URL. Client application  uses this Logout URL to end the user session.


**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional, required if oxd-https-extension is used).
- OpHost: URL of the OpenID Connect Provider (OP)
- oxdId: oxd ID from client registration
- protectionAccessToken:  Generated from GetClientToken method (Optional, required if oxd-https-extension is used)

**Request:**

**Get Logout URI using oxd-server**

```csharp
     public ActionResult GetLogoutUrl(string oxdHost, int oxdPort, string oxdId, string protectionAccessToken)
        {
            //prepare input params for Getting Logout URI from a site
            var getLogoutUriInputParams = new GetLogoutUrlParams()
            {
                OxdId = oxdId,
                ProtectionAccessToken = protectionAccessToken
            };

            var getLogoutUriClient = new GetLogoutUriClient();
            var getLogoutUriResponse = new GetLogoutUriResponse();
            
            getLogoutUriResponse = getLogoutUriClient.GetLogoutURL(oxdHost, oxdPort, getLogoutUriInputParams);
           
            //Response
            return Json(new { logoutUri = getLogoutUriResponse.Data.LogoutUri });
        }
```


**Get Logout URI using oxd-https-extension**

```csharp
     public ActionResult GetLogoutUrl(string oxdhttpsurl, string oxdId, string protectionAccessToken)
        {
            //prepare input params for Getting Logout URI from a site
            var getLogoutUriInputParams = new GetLogoutUrlParams()
            {
                OxdId = oxdId,
                ProtectionAccessToken = protectionAccessToken
            };

            var getLogoutUriClient = new GetLogoutUriClient();
            var getLogoutUriResponse = new GetLogoutUriResponse();

            getLogoutUriResponse = getLogoutUriClient.GetLogoutURL(oxdhttpsurl, getLogoutUriInputParams);

            //Response
            return Json(new { logoutUri = getLogoutUriResponse.Data.LogoutUri });
        }
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

### UMA (User Managed Access)

#### RS Protect

`ProtectResources` method is used for protecting resource by the Resource Server. The Resource Server is needed to construct the command which will protect the resource.
The command will contain an API path, HTTP methods (POST,GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy is mapped, uma_rs_check_access method will always return access as granted. For more information about uma_rpt_policies you can reference this [document] (https://gluu.org/docs/oxd/3.1.1/api/#uma-2-client-apis).

**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional, required if oxd-https-extension is used).
- oxdId: oxd ID from client registration
- protectionAccessToken: Generated from GetClientToken method (Optional, required if oxd-https-extension is used)

**Request:**

**UMA RS Protect using oxd-server**

```csharp
public ActionResult ProtectResources(string oxdHost, int oxdPort, string oxdId, string protectionAccessToken)
        {
            //prepare input params for Protect Resource
            var protectParams = new UmaRsProtectParams()
            {
                OxdId = oxdId,
                ProtectResources = new List<ProtectResource>
                {
                    new ProtectResource
                    {
                        Path = "/scim",
                        ProtectConditions = new List<ProtectCondition>
                        {
                            new ProtectCondition
                            {
                                HttpMethods = new List<string> { "GET" },
                                Scopes = new List<string> { "https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1" },
                                TicketScopes = new List<string> { "https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1" }
                            }
                        }
                    }
                },
                ProtectionAccessToken = protectionAccessToken
            };
            var protectClient = new UmaRsProtectClient();
            var protectResponse = new UmaRsProtectResponse();

           
            protectResponse = protectClient.ProtectResources(oxdHost, oxdPort, protectParams);
            
                       
            //process response
            if (protectResponse.Status.ToLower().Equals("ok"))
            {
                return Json(new { Response = protectResponse.Status });
            }
        }
```

**UMA RS Protect using oxd-https-extension**

```csharp
public ActionResult ProtectResources(string oxdhttpsurl, string oxdId, string protectionAccessToken)
        {
            //prepare input params for Protect Resource
            var protectParams = new UmaRsProtectParams()
            {
                OxdId = oxdId,
                ProtectResources = new List<ProtectResource>
                {
                    new ProtectResource
                    {
                        Path = "/scim",
                        ProtectConditions = new List<ProtectCondition>
                        {
                            new ProtectCondition
                            {
                                HttpMethods = new List<string> { "GET" },
                                Scopes = new List<string> { "https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1" },
                                TicketScopes = new List<string> { "https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1" }
                            }
                        }
                    }
                },
                ProtectionAccessToken = protectionAccessToken
            };
            var protectClient = new UmaRsProtectClient();
            var protectResponse = new UmaRsProtectResponse();
                      
            protectResponse = protectClient.ProtectResources(oxdhttpsurl, protectParams);

            //process response
            if (protectResponse.Status.ToLower().Equals("ok"))
            {
                return Json(new { Response = protectResponse.Status });
            }
        }
```

**Response:**

```javascript
{
    "status":"ok"
}
```

#### RS Check Access 

`CheckAccess` method is used in the UMA Resource Server to check the access to the resource.

**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional, required if using oxd-https-extension).
- oxdId: oxd ID from client registration
- rpt: Requesting Party Token
- protectionAccessToken:  Generated from GetClientToken method (Optional, required if oxd-https-extension is used)

**Request:**

**Check Access using oxd-server**


```csharp
public ActionResult CheckAccess(string oxdHost, int oxdPort,  string oxdId, string rpt, string protectionAccessToken)
        {
            //prepare input params for Check Access
            var checkAccessParams = new UmaRsCheckAccessParams()
            {
                OxdId = oxdId,
                RPT = rpt,
                Path = "/scim",
                HttpMethod = "GET",
                ProtectionAccessToken = protectionAccessToken
            };

            var checkAccessClient = new UmaRsCheckAccessClient();
            var checkAccessResponse = new UmaRsCheckAccessResponse();

            checkAccessResponse = checkAccessClient.CheckAccess(oxdHost, oxdPort, checkAccessParams);
                       
            if (checkAccessResponse.Status.ToLower().Equals("ok"))
            {
                return Json(new { Response = JsonConvert.SerializeObject(checkAccessResponse.Data) });
            }
        }
```

**Check Access using oxd-https-extension**

```csharp
public ActionResult CheckAccess( string oxdhttpsurl, string oxdId, string rpt, string protectionAccessToken)
        {
            //prepare input params for Check Access
            var checkAccessParams = new UmaRsCheckAccessParams()
            {
                OxdId = oxdId,
                RPT = rpt,
                Path = "/scim",
                HttpMethod = "GET",
                ProtectionAccessToken = protectionAccessToken
            };

            var checkAccessClient = new UmaRsCheckAccessClient();
            var checkAccessResponse = new UmaRsCheckAccessResponse();

            checkAccessResponse = checkAccessClient.CheckAccess(oxdhttpsurl, checkAccessParams);
            
            if (checkAccessResponse.Status.ToLower().Equals("ok"))
            {
                return Json(new { Response = JsonConvert.SerializeObject(checkAccessResponse.Data) });
            }
        }
```

**Response:**

***Access Granted Response***

```javascript
{
    "status":"ok",
    "data":{
        "access":"granted"
    }
}
```

***Access Denied with Ticket Response***

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

***Access Denied without Ticket Response***

```javascript
{
    "status":"ok",
    "data":{
        "access":"denied"
    }
}
```

***Resource is not Protected***

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

The method `GetRPT` is called in order to obtain the RPT (Requesting Party Token) 

**Required Parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: URL of the oxd-https-extension (Optional, required if oxd-https-extension is used).
- oxdId: oxd ID from client registration
- ticket: Client Access Ticket generated by uma_rs_check_access method
- protectionAccessToken:  Generated from GetClientToken method (Optional, required if oxd-https-extension is used).
- pct: (Optional) Persisted Claims Token
- rpt: (Optional) Requesting Party Token

**Request:**

**UMA RP Get RPT using oxd-server**

```csharp
public ActionResult ObtainRpt(string oxdHost, int oxdPort,  string oxdId, string ticket, string protectionAccessToken, string pct, string rpt )
        {
            //prepare input params for Protect Resource
            var getRptParams = new UmaRpGetRptParams()
            {
                getRptParams.OxdId = oxdId,
                getRptParams.ticket = ticket,
                ProtectionAccessToken = protectionAccessToken
            };
            
            var getRptClient = new UmaRpGetRptClient();
            var getRptResponse = new GetRPTResponse();
            
            getRptResponse = getRptClient.GetRPT(oxdHost, oxdPort, getRptParams);
           
            //process response
            if (getRptResponse.Status.ToLower().Equals("ok"))
            {
                return Json(new { Response = JsonConvert.SerializeObject(getRptResponse.Data) });
            }
        }
```

**UMA RP Get RPT using oxd-https-extension**

```csharp
public ActionResult ObtainRpt(string oxdhttpsurl, string oxdId, string ticket, string protectionAccessToken, , string pct, string rpt)
        {
            //prepare input params for Protect Resource
            var getRptParams = new UmaRpGetRptParams()
            {
                getRptParams.OxdId = oxdId,
                getRptParams.ticket = ticket,
                ProtectionAccessToken = protectionAccessToken
            };
            
            var getRptClient = new UmaRpGetRptClient();
            var getRptResponse = new GetRPTResponse();
            
            getRptResponse = getRptClient.GetRPT(oxdhttpsurl, getRptParams);

            //process response
            if (getRptResponse.Status.ToLower().Equals("ok"))
            {
                return Json(new { Response = JsonConvert.SerializeObject(getRptResponse.Data) });
            }
        }
```




**Response:**


***Success Response***

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

***Needs Info Error Response***

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

***Invalid Ticket Error Response***

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

**Required parameters:**

- oxdHost: The IP address of the oxd-server
- oxdport: The port of the oxd-server
- oxdhttpsurl: The URL of the oxd-https-extension (Optional, required if oxd-https-extension is used).
- oxdId: oxd ID from client registration
- ticket: Client Access Ticket generated by uma_rs_check_access method
- protectionAccessToken:  Generated from GetClientToken method (Optional, required if oxd-https-extension is used)

**Request:**

**UMA RP Get Claims Gathering URL using oxd-server**

```csharp
public ActionResult GetClaimsGatheringUrl(string oxdHost, int oxdPort, string oxdId, string ticket, string protectionAccessToken)
        {
            //prepare input params for Check Access
            var getClaimsGatheringUrlParams = new UmaRpGetClaimsGatheringUrlParams()
            {
                OxdId = oxdId,
                Ticket = ticket,
                ClaimsRedirectURI = "https://client.example.com",
                ProtectionAccessToken = protectionAccessToken
            };

            var getClaimsGatheringUrlClient = new UmaRpGetClaimsGatheringUrlClient();
            var getClaimsGatheringUrlResponse = new UmaRpGetClaimsGatheringUrlResponse();

            
            getClaimsGatheringUrlResponse = getClaimsGatheringUrlClient.GetClaimsGatheringUrl(oxdHost, oxdPort, getClaimsGatheringUrlParams);
            
      
            
            //process response
            return Json(new { Response = JsonConvert.SerializeObject(getClaimsGatheringUrlResponse.Data) });
        }
```

**UMA RP Get Claims Gathering URL using oxd-https-extension**

```csharp
public ActionResult GetClaimsGatheringUrl( string oxdhttpsurl, string oxdId, string ticket, string protectionAccessToken)
        {
            //prepare input params for Check Access
            var getClaimsGatheringUrlParams = new UmaRpGetClaimsGatheringUrlParams()
            {
                OxdId = oxdId,
                Ticket = ticket,
                ClaimsRedirectURI = "https://client.example.com",
                ProtectionAccessToken = protectionAccessToken
            };

            var getClaimsGatheringUrlClient = new UmaRpGetClaimsGatheringUrlClient();
            var getClaimsGatheringUrlResponse = new UmaRpGetClaimsGatheringUrlResponse();
                     
            getClaimsGatheringUrlResponse = getClaimsGatheringUrlClient.GetClaimsGatheringUrl(oxdhttpsurl, getClaimsGatheringUrlParams);
            
            //process response
            return Json(new { Response = JsonConvert.SerializeObject(getClaimsGatheringUrlResponse.Data) });
        }
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


## Support
Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.
