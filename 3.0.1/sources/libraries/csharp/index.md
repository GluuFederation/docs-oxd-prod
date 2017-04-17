# oxd-csharp
##Overview
The following documentation demonstrates 
how to use Gluu's commercial OAuth 2.0 client software, [oxd](http://oxd.gluu.org), 
to send users from a C# app to an OpenID Connect Provider (OP) for login. 
You can securely send users to any standard OP for login, including Google 
and the [free open source Gluu Server](http://gluu.org/gluu-server).

## Installation
* [Github sources](https://github.com/GluuFederation/oxd-csharp)
* [Gluu Server](https://gluu.org/docs/ce/3.0.1/installation-guide/install/)
* [oxd server](../../install/index.md)
* [Tests in Github](https://github.com/GluuFederation/oxd-csharp/tree/master/CSharp/Clients)
* [CSharp API Documentation](https://github.com/GluuFederation/oxd-csharp)

!!! **Note:** Install Gluu server in Ubuntu 14 VM in your windows machine. 
VM will need at least 4GB or RAM and 2 CPU units. 
So you can communicate with gluu server from your c# library.
 you can start oxd server in your windows machine it self. 


###**Prerequisite**
```
1) You have to install gluu server in Ubuntu 14 VM and oxd-server in your hosting server to use oxd-csharp
   library with your application.
2) Application will not be working if your host does not have https://. 
```

## Sample Code

#### Register Site
**Required parameters:**
* op_host
* port        - the port of the oxd server
* redirectURI - A URL which the OP is authorized to redirect the user after authorization.

**Request:**
```
public RegisterSiteResponse RegisterSite(string host, int port, string redirectUrl)
    {
        try
        {
            CommandClient client = new CommandClient(host, port);
            RegisterSiteParams param = new RegisterSiteParams();
            param.SetAuthorizationRedirectUri(redirectUrl);
            param.SetPostLogoutRedirectUri(redirectUrl);
            param.SetClientLogoutUri(Lists.newArrayList(new string[] { "" }));

            Command cmd = new Command(CommandType.register_site);
            cmd.setParamsObject(param);

            string commandresponse = client.send(cmd);
            RegisterSiteResponse response = new RegisterSiteResponse(JsonConvert.DeserializeObject<dynamic>(commandresponse).data);
            Assert.IsNotNull(response);
            Assert.IsTrue(!String.IsNullOrEmpty(response.getOxdId()));
            StoredValues._oxd_id = response.getOxdId();
            return response;
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
            Logger.Debug(ex.Message);
            return null;
        }
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
* op_host
* port

**Request:**
```
    public UpdateSiteResponse UpdateSiteRegisteration(string host, int port)
    {
        try
        {
            CommandClient client = new CommandClient(host, port);
            UpdateSiteParams param = new UpdateSiteParams();
            param.SetOxdId(StoredValues._oxd_id);
            param.SetAuthorizationRedirectUri("http://www.test.com/wp-login.php");
            param.SetPostLogoutRedirectUri("http://www.test.com/wp-login.php?action=logout&_wpnonce=a3c70643e9");
            param.SetApplicationType("web");
            param.SetRedirectUris(Lists.newArrayList(new string[] { "http://www.test.com/wp-login.php" }));
            param.SetAcrValues(new List<string>());
            param.SetClientJwksUri("");
            param.SetContacts(Lists.newArrayList(new string[] { "test@gmail.com" }));
            param.SetGrantType(Lists.newArrayList(new string[] { "authorization_code" }));
            param.SetClientTokenEndpointAuthMethod("");
            param.SetClientLogoutUri(Lists.newArrayList(new string[] { "http://www.test.com/wp-login.php?action=logout&_wpnonce=a3c70643e9" }));

            Command cmd = new Command(CommandType.update_site_registration);
            cmd.setParamsObject(param);

            string commandresponse = client.send(cmd);
            UpdateSiteResponse response = new UpdateSiteResponse(JsonConvert.DeserializeObject<dynamic>(commandresponse).data);
            Assert.IsNotNull(response);
            Assert.IsTrue(!String.IsNullOrEmpty(response.getOxdId()));
            return response;
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
            Logger.Debug(ex.Message);
            return null;
        }
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
* op_host
* port

**Request:**
```
    public string GetAuthorizationURL(string host, int port)
    {
        try
        {
            CommandClient client = new CommandClient(host, port);

            GetAuthorizationUrlParams param = new GetAuthorizationUrlParams();
            param.SetOxdId(StoredValues._oxd_id);
            param.SetAcrValues(new List<string>());

            Command cmd = new Command(CommandType.get_authorization_url);
            cmd.setParamsObject(param);

            string response = client.send(cmd);
            GetAuthorizationUrlResponse res = new GetAuthorizationUrlResponse(JsonConvert.DeserializeObject<dynamic>(response).data);

            Assert.IsNotNull(res);
            Assert.IsTrue(!String.IsNullOrEmpty(res.getAuthorizationUrl()));
            return res.getAuthorizationUrl();
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
            Logger.Debug(ex.Message);
            return ex.Message;
        }
    }
```

**Response:**
```javascript
{
    "status":"ok",
    "data":{
        "authorization_url":"https://server.example.com/authorize?response_type=code&client_id=s6BhdRkqt3&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb&scope=openid%20profile&acr_values=duo&state=af0ifjsldkj&nonce=n-0S6_WzA2Mj"
    }
}
```

#### Get Tokens by Code
**Required parameters:**
* op_host
* port
* userId
* userSecret

**Request:**
```
    public GetTokensByCodeResponse GetTokenByCode(string host, int port, string userId, string userSecret)
    {
        try
        {
            CommandClient client = new CommandClient(host, port);
            GetTokensByCodeParams param = new GetTokensByCodeParams();
            param.SetOxdId(StoredValues._oxd_id);
            param.SetCode(get_authorization_code.GetAuthorizationCode(host, port, userId, userSecret));
            param.SetScopes(Lists.newArrayList(new string[] { "openid", "profile" }));
            Command cmd = new Command(CommandType.get_tokens_by_code);
            cmd.setParamsObject(param);
            string commandresponse = client.send(cmd);
            GetTokensByCodeResponse response = new GetTokensByCodeResponse(JsonConvert.DeserializeObject<dynamic>(commandresponse).data);
            Assert.IsNotNull(response);
            return response;
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
            Logger.Debug(ex.Message);
            return null;
        }
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
!!!**Note:** 
    GetTokenByCode method further calls the GetAuthorizationCode for the Authorization code to be used

```
public static string GetAuthorizationCode(string host, int port, string userId, string userSecret)
{
    try
    {
        CommandClient client = new CommandClient(host, port);
        GetAuthorizationCodeParams param = new GetAuthorizationCodeParams();
        param.SetOxdId(StoredValues._oxd_id);
        param.SetUserName(userId);
        param.SetPassword(userSecret);
        param.SetAcrValues(new List<string>());
        Command cmd = new Command(CommandType.get_authorization_code);
        cmd.setParamsObject(param);
        string response = client.send(cmd);
        GetAuthorizationCodeResponse res = new GetAuthorizationCodeResponse(JsonConvert.DeserializeObject<dynamic>(response).data);
        Assert.IsNotNull(res);
        Assert.IsTrue(!String.IsNullOrEmpty(res.getCode()));
        return res.getCode();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        Logger.Debug(ex.Message);
        return ex.Message;
    }
}
```

#### Get User Info
**Required parameters:**
* op_host
* port
* accessToken

**Request:**
```
    public GetUserInfoResponse GetUserInfo(string host, int port, string accessToken)
    {
        try
        {
            CommandClient client = new CommandClient(host, port);

            GetUserInfoParams param = new GetUserInfoParams();
            param.setOxdId(StoredValues._oxd_id);
            param.setAccessToken(accessToken);

            Command cmd = new Command(CommandType.get_user_info);
            cmd.setParamsObject(param);

            string response = client.send(cmd);
            GetUserInfoResponse res = new GetUserInfoResponse(JsonConvert.DeserializeObject<dynamic>(response).data);
            Assert.IsNotNull(res);
            return res;
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
            Logger.Debug(ex.Message);
            return null;
        }
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
* op_host
* port

**Request:**
```
    public LogoutResponse GetLogoutURL(string host, int port)
    {
        try
        {
            CommandClient client = new CommandClient(host, port);

            GetLogoutUrlParams param = new GetLogoutUrlParams();
            param.setOxdId(StoredValues._oxd_id);
            param.setIdTokenHint("dummy_token"); 
            param.setState(Guid.NewGuid().ToString());

            Command cmd = new Command(CommandType.get_logout_uri);
            cmd.setParamsObject(param);

            string response = client.send(cmd);
            LogoutResponse res = new LogoutResponse(JsonConvert.DeserializeObject<dynamic>(response).data);
            Assert.IsNotNull(res);
            return res;
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
            Logger.Debug(ex.Message);
            return null;
        }
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
**Request:**
```
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
						Scopes = new List<string> { "https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1" },      // Your hosted Gluu server URL
						TicketScopes = new List<string> { "https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1" } // Your hosted Gluu server URL
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

#### UMA RS Check access
**Request:**
```
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

**Response:**
```
Access Denied with valid Ticket	
```
 

#### UMA Get RPT
**Request:**
```
  private GetRPTResponse ObtainRpt(OxdModel oxdModel)
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
```
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
```
public ActionResult GetGat(OxdModel oxd)
{
	var getGatInputParams = new GetGATParams();
	var getGatClient = new GetGATClient();

	//prepare input params for Getting GAT
	getGatInputParams.OxdId = oxd.OxdId;
	getGatInputParams.Scopes = new List<string> {
									"https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1",
									"https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas2" };

	//Get GAT
	var getGatResponse = getGatClient.GetGat(oxd.OxdHost, oxd.OxdPort, getGatInputParams);

	//Process response
	return Json(new { getGatResponse = getGatResponse.Data.Rpt });
}   
```
