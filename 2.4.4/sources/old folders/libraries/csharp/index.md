# oxd-csharp

oxd-Csharp is a C# .Net library (DLL) to interact with Gluu's OXD Server. For information about oxD, visit [http://oxd.gluu.org](http://oxd.gluu.org)

## Installation

### Prerequisites

* Visual Studio 2015
* Gluu oxD Server - [Installation docs](https://oxd.gluu.org/docs/install/)

### Library

* *Official Gluu's Nuget Package* - Install using the Visual Studio Nuget package manager console from the Gluu's official Nuget Package. Click [here](https://www.nuget.org/packages/Gluu.Oxd.OxdCSharp/) and follow the installation steps.


## Configuration

The minimal configuration required to get oxd-csharp working:

```
[oxd]
host = 127.0.0.1 //IP represents localhost
port = 8099

[client]
authorization_redirect_uri=https://your.site.org/callback
```

All the required and optional configuration items are with application developer. The oxd-csharp library does not have any specific configuration items itself.

## Using the oxd-csharp Library in your website

#### Register Site

The following are the required information for registering a Site: 

- *OxdHost* - Oxd Server's Host address
- *OxdPort* - Oxd Server's Port number
- *AuthorizationRedirectUri* - A URL which the OP is authorized to redirect the user after authorization.

The following code snippet can be used to register a site.

```csharp
[HttpPost]
public ActionResult RegisterSite(OxdModel oxdModel)
{
	//prepare input params for Register Site
    var registerSiteInputParams = new RegisterSiteParams();
    registerSiteInputParams.AuthorizationRedirectUri = oxdModel.RedirectUrl;
    registerSiteInputParams.OpHost = "https://scim-test.gluu.org";
    registerSiteInputParams.ClientName = "OxdTestingClient";

    //Register Site
    var registerSiteClient = new RegisterSiteClient();
    var registerSiteResponse = registerSiteClient.RegisterSite(oxdModel.OxdHost, oxdModel.OxdPort, registerSiteInputParams);

    //Process the response
    return Json(new { oxdId = registerSiteResponse.Data.OxdId });
}
```

#### Get Authorization URL

The following are the required information for Getting Authorization URL: 

- *OxdHost* - Oxd Server's Host address
- *OxdPort* - Oxd Server's Port number
- *OxdId* - The _OXD ID_ of registered site

The following code snippet can be used to Get Authorization URL.

```csharp
[HttpPost]
public ActionResult GetAuthorizationUrl(OxdModel oxdModel)
{
	//prepare input params for Getting Auth URL from a site
    var getAuthUrlInputParams = new GetAuthorizationUrlParams();
    getAuthUrlInputParams.OxdId = oxdModel.OxdId;

    //Get Auth URL
    var getAuthUrlClient = new GetAuthorizationUrlClient();
    var getAuthUrlResponse = getAuthUrlClient.GetAuthorizationURL(oxdModel.OxdHost, oxdModel.OxdPort, getAuthUrlInputParams);

    //Process Response
    return Json(new { authUrl = getAuthUrlResponse.Data.AuthorizationUrl });
}
```

#### Get Tokens by Code

The following are the required information for Getting Tokens by Code: 

- *OxdHost* - Oxd Server's Host address
- *OxdPort* - Oxd Server's Port number
- *OxdId* - The _OXD ID_ of registered site
- *Code* - The Code from OP redirect url
- *State* - The State from OP redirect url

> **Note:** Before using this Get Tokens by Code API, you must obtain the Code and State values by authenticating the user using AuthorizationRedirectUri.

The following code snippet can be used to Get Tokens by Code.

```csharp
[HttpPost]
public ActionResult GetTokensByCode(OxdModel oxdModel)
{
	//prepare input params for Getting Tokens from a site
    var getTokenByCodeInputParams = new GetTokensByCodeParams();
    getTokenByCodeInputParams.OxdId = oxdModel.OxdId;
    getTokenByCodeInputParams.Code = oxdModel.AuthCode;
    getTokenByCodeInputParams.State = oxdModel.AuthState;

    //Get Tokens by Code
    var getTokenByCodeClient = new GetTokensByCodeClient();
    var getTokensByCodeResponse = getTokenByCodeClient.GetTokensByCode(oxdModel.OxdHost, oxdModel.OxdPort, getTokenByCodeInputParams);

    //Process response
    return Json(new { accessToken = getTokensByCodeResponse.Data.AccessToken, refreshToken = getTokensByCodeResponse.Data.RefreshToken });
}
```

#### Get User Info

The following are the required information for Getting User Info: 

- *OxdHost* - Oxd Server's Host address
- *OxdPort* - Oxd Server's Port number
- *OxdId* - The _OXD ID_ of registered site
- *AccessToken* - The _Access Token_ of the authenticated user

The following code snippet can be used to Get User Info.

```csharp
[HttpPost]
public ActionResult GetUserInfo(OxdModel oxdModel)
{
	//prepare input params for Getting User Info from a site
    var getUserInfoInputParams = new GetUserInfoParams();
    getUserInfoInputParams.OxdId = oxdModel.OxdId;
    getUserInfoInputParams.AccessToken = oxdModel.AccessToken;

    //Get User Info
    var getUserInfoClient = new GetUserInfoClient();
    var getUserInfoResponse = getUserInfoClient.GetUserInfo(oxdModel.OxdHost, oxdModel.OxdPort, getUserInfoInputParams);

    //Process response
    var userName = getUserInfoResponse.Data.UserClaims.Name.First();
    var userEmail = getUserInfoResponse.Data.UserClaims.Email == null ? string.Empty : getUserInfoResponse.Data.UserClaims.Email.FirstOrDefault();

    return Json(new { userName = userName });
}
```

#### Get Logout URI

The following are the required information for Getting Logout URI: 

- *OxdHost* - Oxd Server's Host address
- *OxdPort* - Oxd Server's Port number
- *OxdId* - The _OXD ID_ of registered site

The following code snippet can be used to Get Logout URI.

```csharp
[HttpPost]
public ActionResult GetLogoutUri(OxdModel oxdModel)
{
	//prepare input params for Getting Logout URI from a site
    var getLogoutUriInputParams = new GetLogoutUrlParams();
    getLogoutUriInputParams.OxdId = oxd.OxdId;

    //Get Logout URI
    var getLogoutUriClient = new GetLogoutUriClient();
    var getLogoutUriResponse = getLogoutUriClient.GetLogoutURL(oxd.OxdHost, oxd.OxdPort, getLogoutUriInputParams);

    //Process response
    return Json(new { logoutUri = getLogoutUriResponse.Data.LogoutUri });
}
```

#### Update Site Registration

The following are the required information for updating a registered Site: 

- *OxdHost* - Oxd Server's Host address
- *OxdPort* - Oxd Server's Port number
- *OxdId* - The _OXD ID_ of registered site

The following code snippet can be used to update a site.

```csharp
[HttpPost]
public ActionResult UpdateSiteRegistration(OxdModel oxdModel)
{
	//prepare input params for Update Site Registration
    var updateSiteInputParams = new UpdateSiteParams();
    updateSiteInputParams.OxdId = oxdModel.OxdId;
    updateSiteInputParams.Contacts = new List<string> { oxdModel.OxdEmail };
    updateSiteInputParams.PostLogoutRedirectUri = oxdModel.PostLogoutRedirectUrl;

    //Update Site Registration
    var updateSiteClient = new UpdateSiteRegistrationClient();
    var updateSiteResponse = updateSiteClient.UpdateSiteRegistration(oxdModel.OxdHost, oxdModel.OxdPort, updateSiteInputParams);

    //Process the response
    return Json(new { status = updateSiteResponse.Status });
}
```

#### UMA RS Protect Resources

The following are the required information for Protecting UMA resource in Resoruce Server: 

- *OxdId* - The _OXD ID_ of registered site

The following code snippet can be used to Protect UMA resources in Rssource Server.

```csharp
private UmaRsProtectResponse ProtectResources(OxdModel oxdModel)
{
	var protectParams = new UmaRsProtectParams();
    var protectClient = new UmaRsProtectClient();

    //prepare input params for Protect Resource
    protectParams.OxdId = oxdModel.OxdId;
    protectParams.ProtectResources = new List<ProtectResource>
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
	};
   	

    //Protect Resources
    var protectResponse = protectClient.ProtectResources(oxdModel.OxdHost, oxdModel.OxdPort, protectParams);

    //process response
    if(protectResponse.Status.ToLower().Equals("ok"))
    {
    	return protectResponse;
    }

    throw new Exception("Procteting Resource is not successful. Check OXD Server log for error details.");
}
```

#### UMA RS Check Access

The following are the required information for Checking Access of a UMA resource in Resource Server: 

- *OxdId* 		- The _OXD ID_ of registered site
- *RPT* 		- Requesting Party Token
- *Path* 		- Path of resource (e.g. http://rs.com/phones), /phones should be passed
- *HttpMethod* 	- Http method of RP request (GET, POST, PUT, DELETE)

The following code snippet can be used to Check Access of a UMA resource protected in Resource Server.

```csharp
private UmaRsCheckAccessResponse CheckAccess(string rpt, string path, string httpMethod, OxdModel oxdModel)
{
	var checkAccessParams = new UmaRsCheckAccessParams();
    var checkAccessClient = new UmaRsCheckAccessClient();

    //prepare input params for Check Access
    checkAccessParams.OxdId = oxdModel.OxdId;
    checkAccessParams.RPT = rpt;
    checkAccessParams.Path = path;
    checkAccessParams.HttpMethod = httpMethod;

    //Check Access
    var checkAccessResponse = checkAccessClient.CheckAccess(oxdModel.OxdHost, oxdModel.OxdPort, checkAccessParams);

    //process response
    return checkAccessResponse;
}
```

#### UMA RP - Get RPT

The following are the required information for Getting RPT from RP: 

- *OxdId* 		- The _OXD ID_ of registered site
- *ForceNew* 	- Indicates whether return new RPT, In General it should be false, so oxd server can cache/reuse same RPT

The following code snippet can be used to get RPT from RP.

```csharp
private GetRPTResponse GetRpt(OxdModel oxdModel)
{
	var getRptParams = new UmaRpGetRptParams();
    var getRptClient = new UmaRpGetRptClient();

    //prepare input params for Protect Resource
    getRptParams.OxdId = oxdModel.OxdId;
    getRptParams.ForceNew = false;

    //Get RPT
    var getRptResponse = getRptClient.GetRPT(oxdModel.OxdHost, oxdModel.OxdPort, getRptParams);

    //process response
    if (getRptResponse.Status.ToLower().Equals("ok"))
    {
    	return getRptResponse;
    }

    throw new Exception("Obtaining RPT is not successful. Check OXD Server log for error details.");
}
```

#### UMA RP - Authorize RPT

The following are the required information for Authorizing RPT from RP: 

- *OxdId*	- The _OXD ID_ of registered site
- *RPT* 	- Requesting Party Token
- *Ticket* 	- Ticket from Check Access command response if the resource is protected with Ticket Scope

The following code snippet can be used to authorize RPT from RP.

```csharp
private UmaRpAuthorizeRptResponse AuthorizeRpt(string rpt, string ticket, OxdModel oxdModel)
{
	var authorizeRptParams = new UmaRpAuthorizeRptParams();
    var authorizeRptClient = new UmaRpAuthorizeRptClient();

    //prepare input params for Check Access
    authorizeRptParams.OxdId = oxdModel.OxdId;
    authorizeRptParams.RPT = rpt;
    authorizeRptParams.Ticket = ticket;

    //Authorize RPT
    var authorizeRptResponse = authorizeRptClient.AuthorizeRpt(oxdModel.OxdHost, oxdModel.OxdPort, authorizeRptParams);

    //process response
    return authorizeRptResponse;
}
```

#### UMA RP Get GAT

The following are the required information for Getting GAT from RP: 

- *OxdId* 	- The _OXD ID_ of registered site
- *Scopes* 	- Required scopes. RP should know required scopes in advance

The following code snippet can be used to get GAT from RP.

```csharp
[HttpPost]
public ActionResult GetGat(OxdModel oxd)
{
	var getGatInputParams = new GetGATParams();
    var getGatClient = new GetGATClient();

    //prepare input params for Getting GAT
    getGatInputParams.OxdId = oxd.OxdId;
    getGatInputParams.Scopes = new List<string> {
    									"https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1",
                                        "https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas2" 
                                        };

	//Get GAT
    var getGatResponse = getGatClient.GetGat(oxd.OxdHost, oxd.OxdPort, getGatInputParams);

    //Process response
    return Json(new { getGatResponse = getGatResponse.Data.Rpt });
}
```