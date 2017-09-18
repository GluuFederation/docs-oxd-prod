# oxd-csharp

oxd c# is a client library for the oxd Server.

## Deployment

Download the zip file from the 
[Github Page](https://github.com/GluuFederation/oxd-csharp)

* [Tests in Github](https://github.com/GluuFederation/oxd-csharp/tree/master/CSharp/client)

* [CSharp API Documentation](https://oxd.gluu.org/api-docs/csharp/2.4.4/)

### Using the Library in your website

#### Register Site
The following snippet can be used to register the website

The required parameters are 

* op_host
* port - the port of the oxd server
* redirectURI - A URL which the OP is authorized to redirect the user
after authorization.

The response returned is 

* oxd_id (Type: String)

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

#### Update Site Registration
The following snippet can be used to update the website registration 

The required parameters are 

* op_host

* port

The response returned is

* oxd_id (Type: String)

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

#### Get Authorization URL
The following snippet can be used to get the authorization URL.

The required parameters are 

* op_host

* port

The response is

* authorization_url (Type: String)

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

#### Get Tokens by Code
The following snippet can be used to get tokens by code.

The required parameters are 

* op_host

* port

* userId

* userSecret

The response is 

* access_token (Type: String)

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

**Note:** GetTokenByCode method further calls the GetAuthorizationCode for the Authorization code to be used

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
The following snippet can be used to get user information.

The required parameters are 

* op_host

* port

* accessToken

The response is 

* response_claims (Type: Array)

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

#### Logout
The following snippet can be used to logout.

The required parameters are 

* op_host

* port

The response returned is

* response_html (Type: String)

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

