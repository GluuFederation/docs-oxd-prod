# CSharp

## Installation

### Prerequisites

- .Net Framework 4.5 or higher
- Gluu oxd server - [Installation docs](https://gluu.org/docs/oxd/install/)

### Library

- _Install from NuGet_ - Use the NuGet Package Manager Console of Visual Studio `Tools` > `NuGet Package Manager` > `Package Manager Console` to install the Gluu.Oxd.OxdCSharp package, by running the following command:

`PM> Install-Package Gluu.Oxd.OxdCSharp`

- _Source from Github_ -   [Download](https://github.com/GluuFederation/oxd-csharp/archive/3.1.3.zip) the zip of the oxd CSharp library

### Important Links

- [oxd docs](https://gluu.org/docs/oxd)
- oxd-Csharp [API docs](https://rawgit.com/GluuFederation/oxd-csharp/3.1.3/docs/html/index.html) for the auto-generated Csharp docs, which includes more in-depth information about the various functions and parameters
- See the code of a [sample .Net app](https://github.com/GluuFederation/oxd-csharp/tree/3.1.3/GluuDemoWebsite) built using oxd-sharp
- Browse the oxd-csharp [source code on Github](https://github.com/GluuFederation/oxd-csharp)

## Configuration

oxd-csharp uses a configuration file to specify information needed to configure the OpenID Connect client. If OpenID dynamic client registration is used, the config file needs to be _writable by the app_, because oxd will save the client id and client secret to this file.

oxd-csharp can communicate with the oxd server via sockets or HTTPS.

Below are minimal configuration examples for sockets and HTTPS transport. The [oxd_config.json](https://github.com/GluuFederation/oxd-csharp/blob/3.1.3/GluuDemoWebsite/oxd_config.json) file contains a full list of configuration parameters and sample values. 

!!! Note
    The client hostname should be a valid hostname (FQDN), not a localhost or an IP Address

**Configuration for oxd-server via sockets:**

```
"connection_type": "local",  
"oxd_host": "127.0.0.1",  
"oxd_host_port": 8099  
```  

**Configuration for oxd-https-extension:**

```
"connection_type": "web",  
"http_rest_url": "https://127.0.0.1:8443"  
```

## Sample Code 

oxdCsharp OpenID Connect Namespaces

```csharp  
using oxdCSharp.Clients;  
using oxdCSharp.CommandResponses;  
using oxdCSharp.CommandParameters;  
```  

oxdCsharp UMA Namespaces

```csharp  
using oxdCSharp.UMA.Clients;  
using oxdCSharp.UMA.CommandParameters;  
using oxdCSharp.UMA.CommandResponses;  
```  

### Set Up Client

**Setup Client using oxd-server**

```csharp  
public ActionResult SetupClient(string oxdHost, int oxdPort,  string OpHost, string redirectUrl)  
{  
    //prepare input params for Setup client  
    var setupClientInputParams = new SetupClientParams()  
    {  
        AuthorizationRedirectUri = redirectUrl,  
        OpHost = OpHost,  
        ClientName = "<Your Client Name>",  
        Scope = new List<string> { "openid", "profile", "email", "uma_protection", "uma_authorization" },  
        GrantTypes = new List<string> { "authorization_code", "client_credentials", "uma_ticket" }  
    };  
  
    var setupClientClient = new SetupClientClient();  
    var setupClientResponse = new SetupClientResponse();  
    setupClientResponse = setupClientClient.SetupClient(oxdHost, oxdPort, setupClientInputParams);  
  
    return Json(new { oxdId = oxd.OxdId, clientId = setupClientResponse.Data.clientId, clientSecret = setupClientResponse.Data.clientSecret });  
}  
```  

**Set Up Client using oxd-https-extension**

```csharp  
public ActionResult SetupClient( string oxdHttpsUrl, string OpHost, string redirectUrl)  
{  
    //prepare input params for Setup client  
    var setupClientInputParams = new SetupClientParams()  
    {  
        AuthorizationRedirectUri = redirectUrl,  
        OpHost = OpHost,  
        ClientName = "<Your Client Name>",  
        Scope = new List<string> { "openid", "profile", "email", "uma_protection", "uma_authorization" },  
        GrantTypes = new List<string> { "authorization_code", "client_credentials", "uma_ticket" }  
    };  
   
    var setupClientClient = new SetupClientClient();  
    var setupClientResponse = new SetupClientResponse();  
    setupClientResponse = setupClientClient.SetupClient(oxdHttpsUrl, setupClientInputParams);  
  
    return Json(new { oxdId = oxd.OxdId, clientId = setupClientResponse.Data.clientId, clientSecret = setupClientResponse.Data.clientSecret });  
}  
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

**Get Client Token using oxd-server**

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
    string protectionAccessToken = getClientAccessToken.GetClientToken(oxdHost, oxdPort, getClientAccessTokenParams()).Data.accessToken;  
 
    return protectionAccessToken;  
}  
```  

**Get Client Token using oxd-https-extension**

```csharp  
public string GetProtectionAccessToken( string oxdHttpsUrl, string OpHost, string ClientId, string clientSecret)  
{  
    //prepare input params for Client Registration  
    var getClientAccessTokenParams = new GetClientTokenParams()  
    {  
        clientId = clientid,  
        clientSecret = clientsecret,  
        opHost = OpHost  
    };  
  
    var getClientAccessToken = new GetClientTokenClient();  
    string protectionAccessToken = getClientAccessToken.GetClientToken(oxdHttpsUrl, getClientAccessTokenParams()).Data.accessToken;  
  
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

### Introspect Access Token

**Introspect Access Token using oxd-server**

```csharp  
public ActionResult IntrospectAccessToken(string oxdHost, int oxdPort, string oxd_id, string access_token)  
    {  
        var introspectAccessTokenParams = new IntrospectAccessTokenParams()  
        {  
            OxdId = oxd_id,  
            AccessToken = access_token  
        };  
  
        var introspectAccessTokenClient = new IntrospectAccessTokenClient();  
        var introspectAccessTokenResponse = new IntrospectAccessTokenResponse();  
  
        introspectAccessTokenResponse = introspectAccessTokenClient.IntrospectAccessToken(oxdHost, oxdPort, introspectAccessTokenParams);  
  
        return Json(new { status = introspectAccessTokenResponse.Status });  
  
    }  
```  

**Introspect Access Token using oxd-https-extension**

```csharp  
public ActionResult IntrospectAccessToken(string oxdHttpsUrl, string oxd_id, string access_token)  
    {  
        var introspectAccessTokenParams = new IntrospectAccessTokenParams()  
        {  
            OxdId = oxd_id,  
            AccessToken = access_token  
        };  
  
        var introspectAccessTokenClient = new IntrospectAccessTokenClient();  
        var introspectAccessTokenResponse = new IntrospectAccessTokenResponse();  
  
        introspectAccessTokenResponse = introspectAccessTokenClient.IntrospectAccessToken(oxdHttpsUrl, introspectAccessTokenParams);  
  
        return Json(new { status = introspectAccessTokenResponse.Status });  
  
    }  
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

**Register Site using oxd-server**

```csharp  
public ActionResult RegisterSite(string oxdHost, int oxdPort, string OpHost, string redirectUrl)  
{  
    //prepare input params for Client Registration  
    var registerSiteInputParams = new RegisterSiteParams()  
    {  
        AuthorizationRedirectUri = redirectUrl,  
        OpHost = OpHost,  
        ClientName = "<Your Client Name>",  
        Scope = new List<string> { "openid", "profile", "email" }  
    };  
  
    var registerSiteClient = new RegisterSiteClient();  
    var registerSiteResponse = new RegisterSiteResponse();  
    registerSiteResponse = registerSiteClient.RegisterSite(oxdHost, oxdPort, registerSiteInputParams);  
     
    //Response  
    return Json(new { oxdId = registerSiteResponse.Data.OxdId });  
}  
```  

**Register Site using oxd-https-extension**

```csharp  
public ActionResult RegisterSite(string oxdHttpsUrl, string OpHost, string redirectUrl, string protectionAccessToken)  
{  
    //prepare input params for Client Registration  
    var registerSiteInputParams = new RegisterSiteParams()  
    {  
        AuthorizationRedirectUri = redirectUrl,  
        OpHost = OpHost,  
        ClientName = "<Your Client Name>",  
        Scope = new List<string> { "openid", "profile", "email" },  
        ProtectionAccessToken = protectionAccessToken  
    };  
  
    var registerSiteClient = new RegisterSiteClient();  
    var registerSiteResponse = new RegisterSiteResponse();  
    registerSiteResponse = registerSiteClient.RegisterSite(oxdHttpsUrl, registerSiteInputParams);  
      
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

### Update Site

**Update Site using oxd-server**

```csharp  
public ActionResult Update(string oxdHost, int oxdPort,  string oxdId, string postLogoutRedirectUrl)  
{  
    //prepare input params for Update Site Registration  
    var updateSiteInputParams = new UpdateSiteParams()  
    {  
        OxdId = oxdId,  
        Contacts = new List<string> { "support@email.com" },  
        PostLogoutRedirectUri = postLogoutRedirectUrl  
    };  
  
    var updateSiteClient = new UpdateSiteRegistrationClient();  
    var updateSiteResponse = new UpdateSiteResponse();  
    updateSiteResponse = updateSiteClient.UpdateSiteRegistration(oxdHost, oxdPort, updateSiteInputParams);  
      
    //Response  
    return Json(new { status = updateSiteResponse.Status });  
}  
```  

**Update Site using oxd-https-extension**

```csharp  
public ActionResult Update(string oxdHttpsUrl, string oxdId, string postLogoutRedirectUrl, string protectionAccessToken)  
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
    updateSiteResponse = updateSiteClient.UpdateSiteRegistration(oxdHttpsUrl, updateSiteInputParams);  
  
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


### Remove Site

**Remove Site using oxd-server**

```csharp  
public ActionResult RemoveSite(string oxdHost, int oxdPort, string oxd_id)  
    {  
        var removeSiteInputParams = new RemoveSiteParams();  
        {  
            OxdId = oxd_id  
        };  

        var removeSiteClient = new RemoveSiteClient();  
        var removeSiteResponse = new RemoveSiteResponse();  
  
        removeSiteResponse = removeSiteClient.RemoveSite(oxdHost, oxdPort, removeSiteInputParams);  
  
        return Json(new { status = removeSiteResponse.Status });  
  
    }  
```  

**Remove Site using oxd-https-extension**

```csharp  
public ActionResult RemoveSite(string oxdHttpsUrl, string oxd_id, string protectionAccessToken)  
    {  
        var removeSiteInputParams = new RemoveSiteParams();  
        {  
            OxdId = oxd_id,  
            ProtectionAccessToken = protectionAccessToken  
        };  
        var removeSiteClient = new RemoveSiteClient();  
        var removeSiteResponse = new RemoveSiteResponse();  
  
        removeSiteResponse = removeSiteClient.RemoveSite(oxdHttpsUrl, removeSiteInputParams);  
  
        return Json(new { status = removeSiteResponse.Status });  
  
    }  
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

**Get Authorization URL using oxd-server**

```csharp  
public ActionResult GetAuthorizationURL(string oxdHost, int oxdPort, string oxdId, dictionary<string, string> customParams)  
{  
    //prepare input params for Getting Auth URL from a site  
    var getAuthUrlInputParams = new GetAuthorizationUrlParams()  
    {  
        OxdId = oxdId,  
        CustomParams = customParams  
    };  
  
    var getAuthUrlClient = new GetAuthorizationUrlClient();  
    var getAuthUrlResponse = new GetAuthorizationUrlResponse();  
    getAuthUrlResponse = getAuthUrlClient.GetAuthorizationURL(oxdHost, oxdPort, getAuthUrlInputParams);  
  
    //Response  
    return Json(new { authUrl = getAuthUrlResponse.Data.AuthorizationUrl });  
}  
```  

**Get Authorization URL using oxd-https-extension**

```csharp  
public ActionResult GetAuthorizationURL(string oxdHttpsUrl, string oxdId, dictionary<string, string> customParams, string protectionAccessToken)  
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
    getAuthUrlResponse = getAuthUrlClient.GetAuthorizationURL(oxdHttpsUrl, getAuthUrlInputParams);  
  
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

### Get Tokens by Code

**Get Tokens by Code using oxd-server**

```csharp  
public ActionResult GetTokenByCode(string oxdHost, int oxdPort, string oxdId, string authCode, string authState)  
{  
    //prepare input params for Getting Tokens from a site  
    var getTokenByCodeInputParams = new GetTokensByCodeParams()  
    {  
        OxdId = oxdId,  
        Code = authCode,  
        State = authState  
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
public ActionResult GetTokenByCode( string oxdHttpsUrl, string oxdId, string authCode, string authState, string protectionAccessToken)  
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
    getTokensByCodeResponse = getTokenByCodeClient.GetTokensByCode(oxdHttpsUrl, getTokenByCodeInputParams);  
  
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

### Get Access Token by Refresh Token

**Get Access Token by Refresh Token using oxd-server**

```csharp  
public ActionResult GetAccessTokenByRefreshToken(string oxdHost, int oxdPort, string oxdId, string refreshToken)  
{  
    //prepare input params for Getting Tokens from a site  
    var getAccessTokenByRefreshTokenInputParams = new GetAccessTokenByRefreshTokenParams()  
    {  
        OxdId = oxdId,  
        RefreshToken = refreshToken  
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
public ActionResult GetAccessTokenByRefreshToken(string oxdHttpsUrl, string oxdId, string refreshToken, string protectionAccessToken)  
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
    getAccessTokenByRefreshTokenResponse = getAccessTokenByRefreshTokenClient.GetAccessTokenByRefreshToken(oxdHttpsUrl, getAccessTokenByRefreshTokenInputParams);  
  
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
 

### Get User Info

**Get User Info using oxd-server**

```csharp  
public ActionResult GetUserInfo(string oxdHost, int oxdPort,  string oxdId, string accessToken)  
{  
    //prepare input params for Getting User Info from a site  
    var getUserInfoInputParams = new GetUserInfoParams()  
    {  
        OxdId = oxdId,  
        AccessToken = accessToken  
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
public ActionResult GetUserInfo(string oxdHttpsUrl, string oxdId, string accessToken, string protectionAccessToken)  
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
      
    getUserInfoResponse = getUserInfoClient.GetUserInfo(oxdHttpsUrl, getUserInfoInputParams);  
  
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

### Get Logout URI

**Get Logout URI using oxd-server**

```csharp  
public ActionResult GetLogoutUrl(string oxdHost, int oxdPort, string oxdId)  
{  
    //prepare input params for Getting Logout URI from a site  
    var getLogoutUriInputParams = new GetLogoutUrlParams()  
    {  
        OxdId = oxdId  
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
public ActionResult GetLogoutUrl(string oxdHttpsUrl, string oxdId, string protectionAccessToken)  
{  
    //prepare input params for Getting Logout URI from a site  
    var getLogoutUriInputParams = new GetLogoutUrlParams()  
    {  
        OxdId = oxdId,  
        ProtectionAccessToken = protectionAccessToken  
    };  
  
    var getLogoutUriClient = new GetLogoutUriClient();  
    var getLogoutUriResponse = new GetLogoutUriResponse();  
  
    getLogoutUriResponse = getLogoutUriClient.GetLogoutURL(oxdHttpsUrl, getLogoutUriInputParams);  
  
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

### UMA RS Protect

**RS Protect using oxd-server**

```csharp  
public ActionResult ProtectResources(string oxdHost, int oxdPort, string oxdId)  
{  
    //prepare input params for Protect Resource  
    var protectParams = new UmaRsProtectParams()  
    {  
        OxdId = oxdId,
        Overwrite = true,
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
        }  
    };  
    var protectClient = new UmaRsProtectClient();  
    var protectResponse = new UmaRsProtectResponse();  
    protectResponse = protectClient.ProtectResources(oxdHost, oxdPort, protectParams);  
      
    return Json(new { Response = protectResponse.Status });  
}  
```  

**RS Protect with scope_expression using oxd-server**

```csharp  
public ActionResult ProtectResources(string oxdHost, int oxdPort, string oxd_id)  
    {  
        var protectParams = new UmaRsProtectParams()  
        {  
            OxdId = oxd_id,
            Overwrite = true,
            ProtectResources = new List<ProtectResource>  
            {  
                new ProtectResource  
                {  
                    Path = "/photo",  
                    ProtectConditions = new List<ProtectCondition>  
                    {  
                        new ProtectCondition  
                        {  
                            HttpMethods = new List<string> { "GET" },  
                            ScopeExpressions = new ScopeExpression  
                            {  
                                Rule = JsonConvert.DeserializeObject("{'and':[{'or':[{'var':0},{'var':1}]},{'var':2}]}"),  
                                Data = new List<string>{"http://photoz.example.com/dev/actions/all","http://photoz.example.com/dev/actions/add","http://photoz.example.com/dev/actions/internalClient" }  
                            }  
                        }  
                    }  
                }  
            }  
        };  
  
        var protectClient = new UmaRsProtectClient();  
        var protectResponse = new UmaRsProtectResponse();  
  
        protectResponse = protectClient.ProtectResources(oxdHost, oxdPort, protectParams);  
  
        return Json(new { Response = protectResponse.Status });  
    }  
```  

**RS Protect using oxd-https-extension**

```csharp  
public ActionResult ProtectResources(string oxdHttpsUrl, string oxdId, string protectionAccessToken)  
{  
    //prepare input params for Protect Resource  
    var protectParams = new UmaRsProtectParams()  
    {  
        OxdId = oxdId,
        Overwrite = true,
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
    protectResponse = protectClient.ProtectResources(oxdHttpsUrl, protectParams);  
  
    return Json(new { Response = protectResponse.Status });  
}  
```  

**RS Protect with scope_expression using oxd-https-extension**

```csharp  
public ActionResult ProtectResources(string oxdHttpsUrl, string oxd_id, string protectionAccessToken)  
    {  
        var protectParams = new UmaRsProtectParams()  
        {  
            OxdId = oxd_id,
            Overwrite = true,
            ProtectResources = new List<ProtectResource>  
            {  
                new ProtectResource  
                {  
                    Path = "/photo",  
                    ProtectConditions = new List<ProtectCondition>  
                    {  
                        new ProtectCondition  
                        {  
                            HttpMethods = new List<string> { "GET" },  
                            ScopeExpressions = new ScopeExpression  
                            {  
                                Rule = JsonConvert.DeserializeObject("{'and':[{'or':[{'var':0},{'var':1}]},{'var':2}]}"),  
                                Data = new List<string>{"http://photoz.example.com/dev/actions/all","http://photoz.example.com/dev/actions/add","http://photoz.example.com/dev/actions/internalClient" }  
                            }  
                        }  
                    }  
                }  
            },   
            ProtectionAccessToken = protectionAccessToken  
        };  
  
        var protectClient = new UmaRsProtectClient();  
        var protectResponse = new UmaRsProtectResponse();  
  
        protectResponse = protectClient.ProtectResources(oxdHttpsUrl, protectParams);  
  
        return Json(new { Response = protectResponse.Status });  
    }  
```  

**Response:**

```javascript  
{  
    "status":"ok"  
}  
```  


### UMA RS Check Access 

**Check Access using oxd-server**

```csharp  
public ActionResult CheckAccess(string oxdHost, int oxdPort,  string oxdId, string rpt)  
{  
    //prepare input params for Check Access  
    var checkAccessParams = new UmaRsCheckAccessParams()  
    {  
        OxdId = oxdId,   
        RPT = rpt,  
        Path = "/scim",   
        HttpMethod = "GET"  
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
public ActionResult CheckAccess( string oxdHttpsUrl, string oxdId, string rpt, string protectionAccessToken)  
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
  
    checkAccessResponse = checkAccessClient.CheckAccess(oxdHttpsUrl, checkAccessParams);  
      
    if (checkAccessResponse.Status.ToLower().Equals("ok"))  
    {  
        return Json(new { Response = JsonConvert.SerializeObject(checkAccessResponse.Data) });  
    }  
}  
```  

**Response:**

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

**Introspect RPT using oxd-server**

```csharp  
public ActionResult IntrospectRPT(string oxdHost, int oxdPort, string oxd_id, string rpt)  
    {  
        var umaIntrospectRptParams = new UmaIntrospectRptParams()  
        {  
            OxdId = oxd_id,  
            RPT = rpt  
        };  
  
        var umaIntrospectRptClient = new UmaIntrospectRptClient();  
        var umaIntrospectRptResponse = new UmaIntrospectRptResponse();  
  
        umaIntrospectRptResponse = umaIntrospectRptClient.IntrospectRpt(oxdHost, oxdPort, umaIntrospectRptParams);  
  
        return Json(new { Response = umaIntrospectRptResponse.Data });  
    }  
```  

**Introspect RPT using oxd-https-extension**

```csharp  
public ActionResult IntrospectRPT(string oxdHttpsUrl, string oxd_id, string rpt)  
    {  
        var umaIntrospectRptParams = new UmaIntrospectRptParams()  
        {  
            OxdId = oxd_id,  
            RPT = rpt   
        };  
  
        var umaIntrospectRptClient = new UmaIntrospectRptClient();  
        var umaIntrospectRptResponse = new UmaIntrospectRptResponse();  
  
        umaIntrospectRptResponse = umaIntrospectRptClient.IntrospectRpt(oxdHttpsUrl, umaIntrospectRptParams);  
  
        return Json(new { Response = umaIntrospectRptResponse.Data });  
    }  
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

**RP Get RPT using oxd-server**

```csharp  
public ActionResult ObtainRpt(string oxdHost, int oxdPort,  string oxdId, string ticket, string pct, string rpt )  
{  
    //prepare input params for Protect Resource  
    var getRptParams = new UmaRpGetRptParams()  
    {  
        getRptParams.OxdId = oxdId,  
        getRptParams.ticket = ticket  
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

**RP Get RPT using oxd-https-extension**

```csharp  
public ActionResult ObtainRpt(string oxdHttpsUrl, string oxdId, string ticket, string protectionAccessToken, , string pct, string rpt)  
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
      
    getRptResponse = getRptClient.GetRPT(oxdHttpsUrl, getRptParams);  
  
    //process response  
    if (getRptResponse.Status.ToLower().Equals("ok"))  
    {  
        return Json(new { Response = JsonConvert.SerializeObject(getRptResponse.Data) });  
    }  
}  
```  

**Response:**

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

**RP Get Claims Gathering URL using oxd-server**

```csharp  
public ActionResult GetClaimsGatheringUrl(string oxdHost, int oxdPort, string oxdId, string ticket)  
{  
    //prepare input params for Check Access  
    var getClaimsGatheringUrlParams = new UmaRpGetClaimsGatheringUrlParams()  
    {  
        OxdId = oxdId,  
        Ticket = ticket,  
        ClaimsRedirectURI = "https://client.example.com"  
    };  
  
    var getClaimsGatheringUrlClient = new UmaRpGetClaimsGatheringUrlClient();  
    var getClaimsGatheringUrlResponse = new UmaRpGetClaimsGatheringUrlResponse();  
    getClaimsGatheringUrlResponse = getClaimsGatheringUrlClient.GetClaimsGatheringUrl(oxdHost, oxdPort, getClaimsGatheringUrlParams);  
      
    //process response  
    return Json(new { Response = JsonConvert.SerializeObject(getClaimsGatheringUrlResponse.Data) });  
}  
```  

**RP Get Claims Gathering URL using oxd-https-extension**

```csharp  
public ActionResult GetClaimsGatheringUrl( string oxdHttpsUrl, string oxdId, string ticket, string protectionAccessToken)  
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
                  
    getClaimsGatheringUrlResponse = getClaimsGatheringUrlClient.GetClaimsGatheringUrl(oxdHttpsUrl, getClaimsGatheringUrlParams);  
      
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

## Example

- The `GluuDemoWebsite` directory contains apps and scripts written using oxd-Csharp for OpenID Connect and UM RP Client
- The `UMAExample` directory contains apps and scripts for UMA RS server


## Support
Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.
