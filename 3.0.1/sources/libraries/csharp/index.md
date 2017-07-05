# oxd-csharp
## Overview
The following documentation demonstrates how to use the [oxd client software](http://oxd.gluu.org) to send users from a C# app to an OpenID Connect Provider (OP) for login. You can use oxd to securely send users to any standard OP for login, including Google 
and the [free open source Gluu Server](http://gluu.org/gluu-server).

## Sample Project
[Download a sample project](https://github.com/GluuFederation/oxd-csharp/archive/3.0.1.zip) specific to this oxd library.

## oxd Server Methods

The oxd server provides the following six methods for authenticating users with OpenID Connect:

* [Register Site](../protocol/#register-site)
* [Update Site Registration](../protocol/#update-site-registration)
* [Get Authorization URL](../protocol/#get-authorization-url)
* [Get Tokens by Code](../protocol/#get-user-info)
* [Get User Info](../protocol/#get-user-info)
* [Get Logout URI](../protocol/#log-out-uri)

## Installation

### Prerequisites

To install oxd-csharp, you need the following:
* A valid OpenID Connect Provider (OP), like Google or the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/);
* The [oxd server](../../install/index.md) running in the same server where the client is configured;
* Client application url must be `https://` . 

In addition, the following documents will be of assistance as well:
* [Github sources](https://github.com/GluuFederation/oxd-csharp/tree/3.0.1).
* [Tests in Github](https://github.com/GluuFederation/oxd-csharp/tree/master/CSharp/Clients)
* [CSharp API Documentation](https://github.com/GluuFederation/oxd-csharp)

!!! Note:
    CSharp requires windows server or windows installed machine to work.

### **Install oxd NuGet Package**

```PM> Install-Package Gluu.Oxd.OxdCSharp```

## Sample Code

#### Register Site
**Required parameters:**
* OpHost     - OpenId Provider Url
* oxdport        - the port of the oxd server
* redirectURI - A URL which the OP is authorized to redirect the user after authorization.

**Request:**
```csharp
 public ActionResult RegisterClient(string OpHost, int oxdport, string redirectUrl)
        {
            //prepare input params for Client Registration
            var registerSiteInputParams = new RegisterSiteParams()
            {
                AuthorizationRedirectUri = redirectUrl,
                OpHost = OpHost,
                ClientName = "<Your Client Name>",
                Scope = new List<string> { "openid", "profile", "email", "uma_protection", "uma_authorization" }
            };

            var registerSiteClient = new RegisterSiteClient();

            //Register Client
            var registerSiteResponse = registerSiteClient.RegisterSite(OpHost, oxdport, registerSiteInputParams);

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
**Required parameters:**
* OpHost     - OpenId Provider Url
* oxdport        - the port of the oxd server
* oxdId       - the port of the oxd server
* redirectURI - A URL which the OP is authorized to redirect the user after authorization.

**Request:**
```csharp
     public ActionResult UpdateClient(string opHost, int oxdPort, string oxdId, string postLogoutRedirectUrl)
        {
            //prepare input params for Update Site Registration
            var updateSiteInputParams = new UpdateSiteParams()
            {
                OxdId = oxdId,
                Contacts = new List<string> { "support@email.com" },
                PostLogoutRedirectUri = postLogoutRedirectUrl
            };

            var updateSiteClient = new UpdateSiteRegistrationClient();

            //Update Site Registration
            var updateSiteResponse = updateSiteClient.UpdateSiteRegistration(opHost, oxdPort, updateSiteInputParams);

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
**Required parameters:**
* OpHost   - OpenId Provider Url
* oxdport  - the port of the oxd server
* oxdId    - the port of the oxd server

**Request:**
```csharp
    public ActionResult GetAuthorizationURL(string opHost, int oxdPort, string oxdId)
        {
            //prepare input params for Getting Auth URL from a site
            var getAuthUrlInputParams = new GetAuthorizationUrlParams()
            {
                OxdId = oxdId
            };

            var getAuthUrlClient = new GetAuthorizationUrlClient();

            //Get Auth URL
            var getAuthUrlResponse = getAuthUrlClient.GetAuthorizationURL(opHost, oxdPort, getAuthUrlInputParams);

            //Response
            return Json(new { authUrl = getAuthUrlResponse.Data.AuthorizationUrl });
        }
```

**Response:**
```javascript
{
    "status":"ok",
    "data":{
        "authorization_url":"https://client.example.com/authorize?response_type=code&client_id=s6BhdRkqt3&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb&scope=openid%20profile&acr_values=duo&state=af0ifjsldkj&nonce=n-0S6_WzA2Mj"
    }
}
```

#### Get Tokens by Code
**Required parameters:**
* OpHost   - OpenId Provider Url
* oxdport  - the port of the oxd server
* oxdId    - the port of the oxd server
* authCode    - The Code from OP redirect url
* authState    - The State from OP redirect url

**Request:**
```csharp
     public ActionResult GetTokenByCode(string opHost, int oxdPort, string oxdId, string authCode, string authState)
        {
            //prepare input params for Getting Tokens from a site
            var getTokenByCodeInputParams = new GetTokensByCodeParams()
            {
                OxdId = oxdId,
                Code = authCode,
                State = authState
            };

            var getTokenByCodeClient = new GetTokensByCodeClient();

            //Get Tokens by Code
            var getTokensByCodeResponse = getTokenByCodeClient.GetTokensByCode(opHost, oxdPort, getTokenByCodeInputParams);

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


#### Get User Info
**Required parameters:**
* OpHost   - OpenId Provider Url
* oxdport  - the port of the oxd server
* oxdId    - the port of the oxd server
* accessToken - accessToken from GetTokenByCode

**Request:**
```csharp
    public ActionResult GetUserInfo(string opHost, int oxdPort, string oxdId, string accessToken)
        {
            //prepare input params for Getting User Info from a site
            var getUserInfoInputParams = new GetUserInfoParams()
            {
                OxdId = oxdId,
                AccessToken = accessToken
            };

            var getUserInfoClient = new GetUserInfoClient();

            //Get User Info
            var getUserInfoResponse = getUserInfoClient.GetUserInfo(opHost, oxdPort, getUserInfoInputParams);

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
**Required parameters:**
* OpHost   - OpenId Provider Url
* oxdport  - the port of the oxd server
* oxdId    - the port of the oxd server

**Request:**
```csharp
     public ActionResult GetLogoutUrl(string opHost, int oxdPort, string oxdId)
        {
            //prepare input params for Getting Logout URI from a site
            var getLogoutUriInputParams = new GetLogoutUrlParams()
            {
                OxdId = oxdId
            };

            var getLogoutUriClient = new GetLogoutUriClient();

            //Get Logout URI
            var getLogoutUriResponse = getLogoutUriClient.GetLogoutURL(opHost, oxdPort, getLogoutUriInputParams);

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

## Support
Please report technical issues and suspected bugs on our [support page](https://support.gluu.org/). You can use the same credentials you created to register for your oxd license to sign in on Gluu support.
