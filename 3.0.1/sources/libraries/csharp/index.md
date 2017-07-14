# oxd-csharp
## Overview
The following documentation demonstrates how to use the [oxd client software](http://oxd.gluu.org) C# library to send users from a C# web application to an OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 

## Sample Project
[Download a sample project](https://github.com/GluuFederation/oxd-csharp/archive/3.0.1.zip) specific to this oxd library.

### System Requirements
* Microsoft Visual Studio 2008 +

* Windows 7 + or Windows Server 2008 +

* Gluu.Oxd.OxdCSharp  3.1.0


## Prerequisites
### Required Software
To use the oxd C# library, you will need:

- A valid OpenID Connect Provider (OP), like Google or the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/);    
- An active installation of the [oxd server](../../install/index.md) running in the same server as the client application;      
- A Windows server or Windows installed machine.

### Install the Gluu.Oxd.OxdCSharp NuGet Package
Use the NuGet Package Manager Console (Tools -> NuGet Package Manager -> Package Manager Console) to install the Gluu.Oxd.OxdCSharp package, running the following command:

```PM> Install-Package Gluu.Oxd.OxdCSharp```

### Configure the Client Application
- Your client application must have a valid ssl cert, so the url includes: `https://`    

- The client hostname should be a valid hostname, not localhost or an IP Address. You can configure the hostname by adding the following entry in your `C:\Windows\System32\drivers\etc\host` file:

    `127.0.0.1  client.example.com`

- Enable SSL using the following instructions:

    - Open the Client application in Visual Studio;
    - Select Client Application Property;
    - Navigate to `Development Server` and set `SSL Enabled` to `True`.

## oxd Server Methods
The oxd server provides the following six methods for authenticating users with an OpenID Connect Provider (OP):

- [Register Site](../protocol/#register-site)    
- [Update Site Registration](../protocol/#update-site-registration)    
- [Get Authorization URL](../protocol/#get-authorization-url)   
- [Get Tokens by Code](../protocol/#get-user-info)    
- [Get User Info](../protocol/#get-user-info)   
- [Get Logout URI](../protocol/#log-out-uri)   

## Sample Code

### Register Site
In order to use an OpenID Connect Provider (OP) for login, you need to register your client application at the OP. During registration oxd will dynamically register the OpenID Connect client and save its configuration. Upon successful registration a unique identifier will be issued by the oxd server. If your OpenID Connect Provider does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be passed to the `RegisterSite` method as a parameter. The `RegisterSite` method is a one time task to configure a client in the oxd server and OP.

**Required parameters:**

- OpHost: The URL of the OpenID Connect Provider (OP)

- oxdport: the port of the oxd server 

- redirectURI: A URL which the OP is authorized to redirect the user after authorization.

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
                Scope = new List<string> { "openid", "profile", "email" }
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

### Update Site Registration
The `UpdateSiteRegistration` method can be used to update an existing client in the OP. Fields like Authorization redirect url, post logout url, scope, client secret etc. can be updated using this method.

**Required parameters:**

- OpHost: The URL of the OpenID Connect Provider (OP)

- oxdport: the port of the oxd server 

- redirectURI: A URL which the OP is authorized to redirect the user after authorization.

- postLogoutRedirectUrl: URL to which the RP is requesting that the End-User's User Agent be redirected after a logout has been performed.

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

### Get Authorization URL
The `GetAuthorizationURL` method returns the OpenID Connect Provider authentication URL to which the client application must redirect the user to authorize the release of personal data. The response URL includes state value, which can be used to obtain tokens required for authentication. This state value used to maintain state between the request and the callback.

**Required parameters:**

- OpHost: The URL of the OpenID Connect Provider (OP)

- oxdport: the port of the oxd server 

- oxdId: oxd Id from client registration

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

### Get Tokens by Code

Upon successful login, the login result will return code and state. `GetTokensByCode` uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**

- OpHost: The URL of the OpenID Connect Provider (OP)

- oxdport: the port of the oxd server 

- oxdId: oxd Id from client registration

- authCode: The Code from OP authorization redirect url 

- authState: The State from OP authorization redirect url

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


### Get User Info
Once the user has been authenticated by the OpenID Connect Provider, the `GetUserInfo` method returns Claims (Like First Name, Last Name, emailId, etc.) about the authenticated end user.

**Required parameters:**

- OpHost: The URL of the OpenID Connect Provider (OP)

- oxdport: the port of the oxd server 

- oxdId: oxd Id from client registration

- accessToken: accessToken from GetTokenByCode

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

### Logout

`GetLogoutURL` method returns the OpenID Connect Provider logout url. Client application  uses this logout url to end the user session.


**Required parameters:**

- OpHost: The URL of the OpenID Connect Provider (OP)

- oxdport: the port of the oxd server 

- oxdId: oxd Id from client registration

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
