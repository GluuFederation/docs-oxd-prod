# oxd-CSharp
 
CSharp Client Library for the [Gluu oxd](https://gluu.org/docs/oxd/latest/) 

oxd server. This can be used to access the OpenID connect Authorization end points of 
the Gluu Server via the oxd RP. This library provides the function calls required by a website 
to access user information from a OpenID Connect Provider (OP) by using the oxd as the Relying Party (RP).

* [Code on GitHub](https://github.com/GluuFederation/oxd-csharp).
* [Tests on GitHub](https://github.com/GluuFederation/oxd-csharp/tree/master/CSharp/client).
* [API Documentation(CSharpDocs)](https://github.com/GluuFederation/oxd-csharp).

## Install oxd Server
Please use the links below to download the `oxd Server` zip file. For complete installation instructions, please see the [Install Guide](https://oxd.gluu.org/docs/oxdserver/install/)

[oxd Server](https://ox.gluu.org/maven/org/xdi/oxd-server/3.0.1/oxd-server-3.0.1-distribution.zip)

* oxd-csharp is oxd Server client implemented in C# language which acts 
according to [Protocol](./protocol/).

### Register Site

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
		

* Parameters necessary for to request register_site protocol
	* op_host
	* port
	* redirectUrl	

* Response 
	* oxd_id (Type: String)	
	
### Update Site Registeration

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
	
* Parameters necessary for to request update_site_registration protocol
	* op_host
	* port 	

* Response 
	* oxd_id (Type: String)
	
### Get Authorization Url	

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
		
* Parameters necessary for to request get_authorization_url protocol
	* op_host
	* port 	

* Response 
	* authorization_url (Type: String)	

### Get Tokens By Code	

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
		
		
		
!!! Note : 
    GetTokenByCode method further calling GetAuthorizationCode for getting Authorization code to be used in GetTokenByCode method

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
		
* Parameters necessary for to request get_tokens_by_code protocol
	* op_host
	* port 	
	* userId
	* userSecret

* Response 
	* access_token (Type: String)		
	
### Get User Info

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
		
* Parameters necessary for to request get_user_info protocol
	* op_host
	* port 	
	* accessToken 

* Response 
	* response_claims (Type: Array)		
	
	
### Logout

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
		
* Parameters necessary for to request get_logout_uri protocol
	* op_host
	* port 

* Response 
	* response_html (Type: String)			
