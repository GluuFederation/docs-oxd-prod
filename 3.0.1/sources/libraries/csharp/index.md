# oxd-csharp
##Overview
The following documentation demonstrates 
how to use Gluu's commercial OAuth 2.0 client software, [oxd](http://oxd.gluu.org), 
to send users from a C# app to an OpenID Connect Provider (OP) for login. 
You can securely send users to any standard OP for login, including Google 
and the [free open source Gluu Server](http://gluu.org/gluu-server).



## Sample Project
Download a sample project specific to this tutorial configured with your Auth0 API Keys.
[download](https://github.com/GluuFederation/oxd-csharp/archive/3.0.1.zip)

## oxd Server APIs

The Gluu's oxd Server provides basic six API's for OpenID Connect authentication. The all APIs are listed below.

* [Register Site](https://gluu.org/docs/oxd/protocol/#register-site)
* [Update Site Registration](https://gluu.org/docs/oxd/protocol/#update-site-registration)
* [Get Authorization URL](https://gluu.org/docs/oxd/protocol/#get-authorization-url)
* [Get Tokens by Code](https://gluu.org/docs/oxd/protocol/#get-user-info)
* [Get User Info](https://gluu.org/docs/oxd/protocol/#get-user-info)
* [Get Logout URI](https://gluu.org/docs/oxd/protocol/#log-out-uri)

The Gluu's oxd Server provides two UMA Resource Server API's. The APIs are listed below
* [UMA RS Protect resources](https://gluu.org/docs/oxd/protocol/#uma-rs-protect-resources)
* [UMA RS Check Access](https://gluu.org/docs/oxd/protocol/#uma-rs-check-access)
The Gluu's oxd Server provides two UMA Client API's. The APIs are listed below
* [UMA RP - Get RPT](https://gluu.org/docs/oxd/protocol/#uma-rp-get-rpt)
* [UMA RP - Authorize RPT](https://gluu.org/docs/oxd/protocol/#uma-rp-authorize-rpt)
The oxd Server also provides one Gluu OAuth2 Access Management API's. The API is
* [UMA RP Get GAT](https://gluu.org/docs/oxd/protocol/#gluu-oauth2-access-management-apis)




## Installation
* [Github sources](https://github.com/GluuFederation/oxd-csharp)
* [Gluu Server](https://gluu.org/docs/ce/3.0.1/installation-guide/install/)
* [oxd server](../../install/index.md)
* [Tests in Github](https://github.com/GluuFederation/oxd-csharp/tree/master/CSharp/Clients)
* [CSharp API Documentation](https://github.com/GluuFederation/oxd-csharp)


###**Prerequisite**

1) You have to install [gluu server](https://gluu.org/docs/ce/3.0.1/installation-guide/install/) in Ubuntu 14 VM and oxd-server in your hosting server to use oxd-csharp
   library with your application.
2) [oxd server](../../install/index.md) must be running in the same server where client application is hosted
3) Client application url must be https:// . 


###**Install oxd NuGet Package**

```PM> Install-Package Gluu.Oxd.OxdCSharp```


!!! **Note:** Install Gluu server in Ubuntu 14 VM in your windows machine. 
VM will need at least 4GB or RAM and 2 CPU units. 
So you can communicate with gluu server from your c# library.
 you can start oxd server in your windows machine it self. 

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

#### UMA RS Resource protection
**Required parameters:**
* OpHost   - OpenId Provider Url
* oxdport  - the port of the oxd server
* oxdId    - the port of the oxd server

```csharp
   private UmaRsProtectResponse ProtectResources(string opHost, int oxdPort, string oxdId)
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
                }
            };

            var protectClient = new UmaRsProtectClient();

            //Protect Resources
            var protectResponse = protectClient.ProtectResources(opHost, oxdPort, protectParams);

            //Response
            return protectResponse;
        }
```

#### UMA RS Check access
**Request:**
```csharp
	 private UmaRsCheckAccessResponse CheckAccess(string rpt, string path, string httpMethod, string opHost, int oxdPort, string oxdId)
        {
            //prepare input params for Check Access
            var checkAccessParams = new UmaRsCheckAccessParams()
            {
                OxdId = oxdId,
                RPT = rpt,
                Path = path,
                HttpMethod = httpMethod
            };

            var checkAccessClient = new UmaRsCheckAccessClient();

            //Check Access
            var checkAccessResponse = checkAccessClient.CheckAccess(opHost, oxdPort, checkAccessParams);

            //Response
            return checkAccessResponse;
        }
```

**Response:**
```
Access Denied with valid Ticket	
```
 

#### UMA Get RPT
**Request:**
```csharp
   private GetRPTResponse ObtainRpt(string opHost, int oxdPort, string oxdId)
        {
            //prepare input params for Protect Resource
            var getRptParams = new UmaRpGetRptParams()
            {
                OxdId = oxdId,
                ForceNew = false
            };

            var getRptClient = new UmaRpGetRptClient();

            //Get RPT
            var getRptResponse = getRptClient.GetRPT(opHost, oxdPort, getRptParams);

            //Response
            return getRptResponse;
        }
```

**Response:**
```
Expect a valid RPT is returned
```
!!!Note: 
    Once you get valid RPT you need to follow this steps again:

##### UMA RS Check access  with valid RPT 
**Request:**
``` 
UMA RS Check access again with valid RPT
```

**Response:**
```
Access Denied with valid data
```

##### Authorize RPT  
**Request:**
```csharp
	 private UmaRpAuthorizeRptResponse AuthorizeRpt(string rpt, string ticket, string opHost, int oxdPort, string oxdId)
        {
            //prepare input params for Check Access
            var authorizeRptParams = new UmaRpAuthorizeRptParams()
            {
                OxdId = oxdId,
                RPT = rpt,
                Ticket = ticket
            };

            var authorizeRptClient = new UmaRpAuthorizeRptClient();

            //Authorize RPT
            var authorizeRptResponse = authorizeRptClient.AuthorizeRpt(opHost, oxdPort, authorizeRptParams);

            //Response
            return authorizeRptResponse;
        }
```

**Response:**
```
Status should be ok
```

##### UMA RS Check access (Check Access again after authorizing RPT)
**Request:**
``` 
UMA RS Check access again after authorizing RPT 
```

**Response:**
```
Access Granted
```


#### UMA Get GAT
**Request:**
```csharp
 public ActionResult GetGat(string opHost, int oxdPort, string oxdId)
        {
            //prepare input params for Getting GAT
            var getGatInputParams = new GetGATParams()
            {
                OxdId = oxdId,
                Scopes = new List<string> {
                                            "https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1",
                                            "https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas2" }
            };

            var getGatClient = new GetGATClient();
            
            //Get GAT
            var getGatResponse = getGatClient.GetGat(opHost, oxdPort, getGatInputParams);

            //Response
            return Json(new { getGatResponse = getGatResponse.Data.Rpt });
        }

```

## Support
Any technical support you need please checkout our [support page](https://support.gluu.org/)
Bugs found in oxd CSharp libary can be reported at [github](https://github.com/GluuFederation/oxd-csharp/issues)

