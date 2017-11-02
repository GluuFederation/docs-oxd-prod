# oxd-java

## Overview

Use oxd's Java library to send users from a Java application to your Gluu Server OpenID Connect Provider (OP) for dynamic enrollment, single sign-on (SSO), strong authentication, and access management policy enforcement. 

Download a [Sample Project](https://github.com/GluuFederation/oxd/archive/version_3.1.1.zip) 
specific to this oxd-java library.

## Software Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows Server 2008 or higher
- Java 1.8 or higher
- Apache 2.4.4 or higher
- oxd-java 3.1.1

To use the oxd-java library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/) or Google.    
- An active installation of the [oxd-server](../../../install/index.md).
	- Note: If you want to make RESTful (https) calls from your app to your `oxd-server`, you will need an active installation of the [oxd-https-extension](../../../oxd-https/start/index.md).
- A Windows server or Windows installed machine / Linux server or Linux installed machine.

## Install oxd-java

Get oxd-java JAR files from [Maven Repo](http://ox.gluu.org/maven/org/xdi/oxd-java/)

## Configure the Client Application

There are no configuration files for oxd-java. Redirect URI and other information is set in the code.

Your client application must have a valid SSL certificate, so the URL includes: `https://`    

The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP address. You can configure the hostname by adding the following entry in the host file:

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`
    
Enable SSL by	adding the following lines on virtual host file of Apache in the location:

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


## Endpoints

The oxd-server and oxd-https-extension provide the following methods for authenticating users with an OpenID Connect Provider (OP):

- Available OpenID Connect Endpoints
    - [Setup Client](https://gluu.org/docs/oxd/3.1.1/api/#setup-client)  
    - [Get Client Token](https://gluu.org/docs/oxd/3.1.1/api/#get-client-token)
    - [Register Site](https://gluu.org/docs/oxd/3.1.1/api/#register-site) 
    - [Update Site Registration](https://gluu.org/docs/oxd/3.1.1/api/#update-site-registration)
    - [Get Authorization URL](https://gluu.org/docs/oxd/3.1.1/api/#get-authorization-url)   
    - [Get Tokens by Code](https://gluu.org/docs/oxd/3.1.1/api/#get-tokens-id-access-by-code)
    - [Get Access Token by Refresh Token](https://gluu.org/docs/oxd/3.1.1/api/#get-access-token-by-refresh-token)    
    - [Get User Info](https://gluu.org/docs/oxd/3.1.1/api/#get-user-info)   
    - [Get Logout URI](https://gluu.org/docs/oxd/3.1.1/api/#log-out-uri) 


The oxd-server provides the following methods for performing access management with a UMA Authorization Server (AS):

- Available UMA (User Managed Access) Endpoints  
    - [RS Protect](https://gluu.org/docs/oxd/3.1.1/api/#uma-rs-protect-resources) 
    - [RS Check Access](https://gluu.org/docs/oxd/3.1.1/api/#uma-rs-check-access) 
    - [RP Get RPT](https://gluu.org/docs/oxd/3.1.1/api/#uma-rp-get-rpt) 
    - [RP Get Claims Gathering URL](https://gluu.org/docs/oxd/3.1.1/api/#uma-rp-get-claims-gathering-url) 


## Sample Code - OpenID Connect

Below is a sample pom. To download the pom file please refer to this [Snippet](https://ox.gluu.org/maven/org/xdi/oxd-client/3.1.0.Final/oxd-client-3.1.0.Final.pom)

```
<dependency>
  <artifactId>oxd-client</artifactId>
  <groupId>org.xdi</groupId>
  <version>3.1.1.Final</version>
</dependency>
```

### Setup Client

In order to use an OpenID Connect Provider (OP) for login, 
you need to setup your client application at the OpenID Connect Provider (OP). 
During setup, oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful setup, the oxd-server will assign a unique oxd ID, return a Client ID and Client Secret. This Client ID and Client Secret can be used for `get_client_token` method. If your OpenID Connect Provider (OP) does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be passed to the `send` method as a parameter. The Setup Client method is a one time task to configure a client in the oxd-server and OpenID Connect Provider (OP).

**Parameters:**

- redirectUrl: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- opHost: URL of the OpenID Connect Provider (OP)
- postLogoutRedirectUrl: (Optional) URL to which the user is redirected to after successful logout
- application_type: (Optional) Kind of the application. The default, if omitted, is web. The defined values are native or web.
- response_types: (Optional) Determines the authorization processing flow to be used
- grant_types: (Optional) Grant Types that the client is declaring that it will restrict itself to using
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- acr_values: (Optional) Required for extended authentication. Custom authentication script from Gluu server.
- client_name: (Optional) Client application name
- client_jwks_uri: (Optional) URL for the Client's JSON Web Key Set (JWKS) document
- client_token_endpoint_auth_method: (Optional) Requested Client Authentication method for the Token Endpoint
- client_request_uris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OpenID Connect Provider (OP)
- logoutUrl: (Optional) Client application Logout URL
- client_sector_identifier_uri: (Optional) URL using the HTTPS scheme to be used in calculating Pseudonymous Identifiers by the OpenID Connect Provider (OP)
- contacts: (Optional) Array of e-mail addresses of people responsible for this client
- ui_locales: (Optional) End-User's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference.
- claims_locales: (Optional) End-User's preferred languages and scripts for Claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference.
- client_id: (Optional) Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: (Optional) Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- claims_redirect_uri: (Optional)
- host: the URL of the oxd-server
- port: the port of the oxd-server


**Request:**

```java
CommandClient client = null;
try {
    client = new CommandClient(host, port);

    SetupClientResponse resp = setupClient(client, opHost, redirectUrl, postLogoutRedirectUrl, logoutUrl);
    assertResponse(resp);

    final SetupClientParams commandParams = new SetupClientParams();
    commandParams.setOpHost(opHost);
    commandParams.setAuthorizationRedirectUri(redirectUrl);
    commandParams.setPostLogoutRedirectUri(postLogoutRedirectUrl);
    commandParams.setClientFrontchannelLogoutUri(Lists.newArrayList(logoutUrl));
    commandParams.setRedirectUris(Arrays.asList(redirectUrl));
    commandParams.setAcrValues(new ArrayList<String>());
    commandParams.setScope(Lists.newArrayList("openid", "profile"));
    commandParams.setGrantType(Lists.newArrayList("authorization_code"));
    commandParams.setResponseTypes(Lists.newArrayList("code"));

    final Command command = new Command(CommandType.SETUP_CLIENT);
    command.setParamsObject(commandParams);

    resp = client.send(command).dataAsResponse(SetupClientResponse.class);
    assertResponse(resp);
} finally {
    CommandClient.closeQuietly(client);
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


### Get Client Token

The `send` method is used to get a token which is sent as an input parameter for other methods when the `protect_commands_with_access_token` is enabled in oxd-server.

**Parameters:**

- client_id: Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret.
- client_secret: Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- opHost: URL of the OpenID Connect Provider (OP)
- op_discovery_path: (Optional) Path to discovery document. For example if it's https://client.example.com/.well-known/openid-configuration then path is blank. But if it is https://client.example.com/oxauth/.well-known/openid-configuration then path is oxauth.
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- host: the URL of the oxd-server
- port: the port of the oxd-server

**Request:**

```java
CommandClient client = null;
try {
    client = new CommandClient(host, port);

    SetupClientResponse setup = SetupClientTest.setupClient(client, opHost, redirectUrl, postLogoutRedirectUrl, logoutUrl);

    final GetClientTokenParams params = new GetClientTokenParams();
    params.setOpHost(opHost);
    params.setScope(Lists.newArrayList("openid"));
    params.setClientId(setup.getClientId());
    params.setClientSecret(setup.getClientSecret());

    GetClientTokenResponse resp = client.send(new Command(CommandType.GET_CLIENT_TOKEN).setParamsObject(params)).dataAsResponse(GetClientTokenResponse.class);

    assertNotNull(resp);
    notEmpty(resp.getAccessToken());
} finally {
    CommandClient.closeQuietly(client);
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


### Register Site

In order to use an OpenID Connect Provider (OP) for login, you need to register your client application at the OpenID Connect Provider (OP). During registration oxd will dynamically register the OpenID Connect client and save its configuration. Upon successful registration a unique identifier will be issued by the oxd-server. If your OpenID Connect Provider (OP) does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be passed to the `send` method as a parameter. The Register Site method is a one time task to configure a client in the oxd-server and OpenID Connect Provider (OP).

!!! Note: 
    The `Register Site` endpoint is not required if client is registered using `Setup Client`

**Parameters:**

- redirectUrl: URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- opHost: URL of the OpenID Connect Provider (OP)
- postLogoutRedirectUrl: (Optional) URL to which the user is redirected to after successful logout
- application_type: (Optional) Kind of the application. The default, if omitted, is web. The defined values are native or web.
- response_types: (Optional) Determines the authorization processing flow to be used
- grant_types: (Optional) Grant Types that the client is declaring that it will restrict itself to using
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- acr_values: (Optional) Required for extended authentication. Custom authentication script from Gluu server.
- client_name: (Optional) Client application name
- client_jwks_uri: (Optional) URL for the Client's JSON Web Key Set (JWKS) document
- client_token_endpoint_auth_method: (Optional) Requested Client Authentication method for the Token Endpoint
- client_request_uris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OpenID Connect Provider (OP)
- logoutUrl: (Optional) Client application Logout URL
- client_sector_identifier_uri: (Optional) URL using the HTTPS scheme to be used in calculating Pseudonymous Identifiers by the OpenID Connect Provider (OP)
- contacts: (Optional) Array of e-mail addresses of people responsible for this client
- ui_locales: (Optional) End-User's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference.
- claims_locales: (Optional) End-User's preferred languages and scripts for Claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference.
- client_id: (Optional) Client ID from OpenID Connect Provider (OP). Should be passed with the Client Secret
- client_secret: (Optional) Client Secret from OpenID Connect Provider (OP). Should be passed with the Client ID.
- claims_redirect_uri: (Optional)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- host: the URL of the oxd-server
- port: the port of the oxd-server


**Request:**

```java
CommandClient client = null;
try {
    client = new CommandClient(host, port);

    //final SetupClientResponse setupClient = SetupClientTest.setupClient(client, opHost, redirectUrl);

    RegisterSiteResponse resp = registerSite(client, opHost, redirectUrl, postLogoutRedirectUrl, logoutUrl);
    assertNotNull(resp);

    notEmpty(resp.getOxdId());

    final RegisterSiteParams commandParams = new RegisterSiteParams();
    //commandParams.setProtectionAccessToken(setupClient.getClientRegistrationAccessToken());
    commandParams.setOpHost(opHost);
    commandParams.setAuthorizationRedirectUri(redirectUrl);
    commandParams.setPostLogoutRedirectUri(postLogoutRedirectUrl);
    commandParams.setClientFrontchannelLogoutUri(Lists.newArrayList(logoutUrl));
    commandParams.setRedirectUris(Lists.newArrayList(redirectUrl));
    commandParams.setAcrValues(new ArrayList<String>());
    commandParams.setScope(Lists.newArrayList("openid", "profile"));
    commandParams.setGrantType(Lists.newArrayList("authorization_code"));
    commandParams.setResponseTypes(Lists.newArrayList("code"));

    final Command command = new Command(CommandType.REGISTER_SITE);
    command.setParamsObject(commandParams);

    resp = client.send(command).dataAsResponse(RegisterSiteResponse.class);
    assertNotNull(resp);
    assertNotNull(resp.getOxdId());
    oxdId = resp.getOxdId();
} finally {
    CommandClient.closeQuietly(client);
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

The `send` method can be used to update an existing client in the OpenID Connect Provider (OP). Fields like Authorization Redirect URL, Post Logout URL, Scope, Client Secret and other fields, can be updated using this method.

**Parameters:**

- oxdId: oxd ID from client registration
- authorization_redirect_uri:  (Optional) URL to which the OpenID Connect Provider (OP) is authorized to redirect the user to after authorization
- post_logout_uri: (Optional) URL to which the RP is requesting the End-User's User Agent be redirected to after a logout has been performed
- response_types: (Optional) Determines the authorization processing flow to be used
- grant_types: (Optional) Grant Types that the client is declaring that it will restrict itself to using
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- acr_values: (Optional) Required for extended authentication. Custom authentication script from Gluu server.
- client_name: (Optional) Client application name
- client_secret_expires_at: (Optional) Used to extend client lifetime (milliseconds since 1970)
- client_jwks_uri: (Optional) URL for the client's JSON Web Key Set (JWKS) document
- client_token_endpoint_auth_method: (Optional) Requested Client Authentication method for the Token Endpoint
- client_request_uris: (Optional) Array of request_uri values that are pre-registered by the RP for use at the OpenID Connect Provider (OP)
- client_frontchannel_logout_uris: (Optional) Client application Logout URL
- client_sector_identifier_uri: (Optional) URL using the HTTPS scheme to be used in calculating Pseudonymous Identifiers by the OpenID Connect Provider (OP)
- contacts: (Optional) Array of e-mail addresses of people responsible for this client
- ui_locales: (Optional) End-User's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference.
- claims_locales: (Optional) End-User's preferred languages and scripts for claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference.
- protection_access_token: (Optional) Generated from get_client_token method (Optional, required if oxd-https-extension is used).
- host: the URL of the oxd-server
- port: the port of the oxd-server

**Request:**

```java
CommandClient client = null;
try {
     client = new CommandClient(host, port);

     Calendar calendar = Calendar.getInstance();
     calendar.add(Calendar.DAY_OF_YEAR, 1);

     final UpdateSiteParams commandParams = new UpdateSiteParams();
     commandParams.setOxdId(oxdId);
     commandParams.setClientSecretExpiresAt(calendar.getTime());
     commandParams.setScope(Lists.newArrayList("profile"));

     final Command command = new Command(CommandType.UPDATE_SITE);
     command.setParamsObject(commandParams);

     UpdateSiteResponse resp = client.send(command).dataAsResponse(UpdateSiteResponse.class);
     assertNotNull(resp);
} finally {
     CommandClient.closeQuietly(client);
}
```

**Response:**

```javascript
{
    "status":"ok"
}
```


### Get Authorization URL

The `send` method returns the OpenID Connect Provider (OP) Authentication URL to which the client application must redirect the user to authorize the release of personal data. The Response URL includes state value, which can be used to obtain tokens required for authentication. This state value used to maintain state between the request and the callback.

**Parameters:**

- oxdId: oxd ID from client registration
- scope: (Optional) A scope is an indication by the client that it wants to access some resource
- acr_values: (Optional) Required for extended authentication. Custom authentication script from Gluu server.
- prompt: (Optional) Values that specifies whether the Authorization Server prompts the end-user for re-authentication and consent
- custom_params: (Optional) custom parameters
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used).
- host: the URL of the oxd-server
- port: the port of the oxd-server

**Request:**

```java
CommandClient client = null;
try {
    client = new CommandClient(host, port);

    final RegisterSiteResponse site = RegisterSiteTest.registerSite(client, opHost, redirectUrl);

    final GetAuthorizationUrlParams commandParams = new GetAuthorizationUrlParams();
    commandParams.setOxdId(site.getOxdId());

    final Command command = new Command(CommandType.GET_AUTHORIZATION_URL);
    command.setParamsObject(commandParams);

    final GetAuthorizationUrlResponse resp = client.send(command).dataAsResponse(GetAuthorizationUrlResponse.class);
    assertNotNull(resp);
    notEmpty(resp.getAuthorizationUrl());
} finally {
    CommandClient.closeQuietly(client);
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

Upon successful login, the login result will return code and state. `send` uses code and state to retrieve token which can be used to access user claims.

**Parameters:**

- oxdId: oxd ID from client registration
- code: The code from OpenID Connect Provider (OP) Authorization Redirect URL
- state: The state from OpenID Connect Provider (OP) Authorization Redirect URL
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```java
final String state = CoreUtils.secureRandomString();

String code = codeRequest(client, site.getOxdId(), userId, userSecret, state, nonce);

notEmpty(code);

final GetTokensByCodeParams commandParams = new GetTokensByCodeParams();
commandParams.setOxdId(site.getOxdId());
commandParams.setCode(code);
commandParams.setState(state);

final Command command = new Command(CommandType.GET_TOKENS_BY_CODE).setParamsObject(commandParams);

final GetTokensByCodeResponse resp = client.send(command).dataAsResponse(GetTokensByCodeResponse.class);
assertNotNull(resp);
notEmpty(resp.getAccessToken());
notEmpty(resp.getIdToken());
notEmpty(resp.getRefreshToken());
return resp;
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

The `send` method is used to get a new access token and a new refresh token by using the refresh token which is obtained from `get_tokens_by_code` method.

**Parameters:**

- oxdId: oxd ID from client registration
- refreshToken: Obtained from the get_tokens_by_code method
- scope: (Optional) A scope is an indication by the client that it wants to access some resource
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```java
String oxdId = setup.getOxdId();
final GetAccessTokenByRefreshTokenParams commandParams = new GetAccessTokenByRefreshTokenParams();
commandParams.setOxdId(oxdId);
commandParams.setScope(Lists.newArrayList("openid"));
commandParams.setRefreshToken(resp.getRefreshToken());
 
GetAccessTokensByRefreshTokenResponse resp = client.send(new Command(CommandType.GET_ACCESS_TOKEN_BY_REFRESH_TOKEN).setParamsObject(commandParams)).dataAsResponse(GetAccessTokensByRefreshTokenResponse.class);
 
assertNotNull(resp);
notEmpty(resp.getAccessToken());
notEmpty(resp.getRefreshToken());
return resp;
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

Once the user has been authenticated by the OpenID Connect Provider, the `send` method returns claims (First Name, Last Name, E-Mail, etc.) about the authenticated end-user.

**Parameters:**

- oxdId: oxd ID from client registration
- access_token: access_token from GetTokenByCode or GetAccessTokenbyRefreshToken
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- host: the URL of the oxd-server 
- port: the port of the oxd-server 

**Request:**

```java
CommandClient client = null;
try {
    client = new CommandClient(host, port);

    final RegisterSiteResponse site = RegisterSiteTest.registerSite(client, opHost, redirectUrl);
    final GetTokensByCodeResponse tokens = requestTokens(client, site, userId, userSecret);

    GetUserInfoParams params = new GetUserInfoParams();
    params.setOxdId(oxdId);
    params.setAccessToken(tokens.getAccessToken());//accessToken

    final GetUserInfoResponse resp = client.send(new Command(CommandType.GET_USER_INFO).setParamsObject(params)).dataAsResponse(GetUserInfoResponse.class);
} finally {
    CommandClient.closeQuietly(client);
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

`send` method returns the OpenID Connect Provider Logout URL. Client application uses this Logout URL to end the user session.

**Parameters:**

- oxdId: oxd ID from client registration
- id_token_hint: (Optional) ID Token previously issued by the Authorization Server being passed as a hint about the end-user's current or past authenticated session with the client
- postLogoutRedirectUrl: (Optional) URL to which user is redirected to after successful logout
- state: (Optional) Value used to maintain state between the request and the callback
- session_state: (Optional) JSON string that represents the end-user's login state at the OpenID Connect Provider (OP)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- host: the URL of the oxd-server
- port: the port of the oxd-server

**Request:**

```java
CommandClient client = null;
try {
    client = new CommandClient(host, port);

    final RegisterSiteResponse site = RegisterSiteTest.registerSite(client, opHost, redirectUrl, postLogoutRedirectUrl, "");

    final GetLogoutUrlParams commandParams = new GetLogoutUrlParams();
    commandParams.setOxdId(site.getOxdId());
    commandParams.setIdTokenHint("dummy_token");
    commandParams.setPostLogoutRedirectUri(postLogoutRedirectUrl);
    commandParams.setState(UUID.randomUUID().toString());
    commandParams.setSessionState(UUID.randomUUID().toString()); // here must be real session instead of dummy UUID

    final Command command = new Command(CommandType.GET_LOGOUT_URI).setParamsObject(commandParams);

    final LogoutResponse resp = client.send(command).dataAsResponse(LogoutResponse.class);
    assertNotNull(resp);
    assertTrue(resp.getUri().contains(URLEncoder.encode(postLogoutRedirectUrl, "UTF-8")));
} finally {
    CommandClient.closeQuietly(client);
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


## Sample Code - UMA

### RS Protect

`send` method is used for protecting resources by the Resource Server. The Resource Server is needed to construct the command which will protect the resource.
The command will contain an API path, HTTP methods (POST, GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy is mapped, uma_rs_check_access method will always return access as granted. For more information about uma_rpt_policies you can reference this [document](https://gluu.org/docs/oxd/3.1.1/api/#uma-2-client-apis).

**Parameters:**

- oxdId: oxd ID from client registration
- resources: One or more protected resources that a resource server manages, abstractly, as a set. In authorization policy terminology, a resource set is the "object" being protected. 
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```java
final RsProtectParams commandParams = new RsProtectParams();
commandParams.setOxdId(site.getOxdId());
commandParams.setResources(resources);

final RsProtectResponse resp = client
        .send(new Command(CommandType.RS_PROTECT).setParamsObject(commandParams))
        .dataAsResponse(RsProtectResponse.class);
assertNotNull(resp);
return resp;
```

**Response:**

```javascript
{
    "status":"ok"
}
```


### RS Check Access 

`send` method is used in the UMA Resource Server to check the access to the resource.

**Parameters:**

- oxdId: oxd ID from client registration
- rpt: Requesting Party Token
- path: Path of the resource to be checked 
- http_method: HTTP methods (POST, GET and PUT)
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```java
final RsCheckAccessParams params = new RsCheckAccessParams();
params.setOxdId(site.getOxdId());
params.setHttpMethod("GET");
params.setPath("/ws/phone");
params.setRpt("dummy");

final RsCheckAccessResponse response = client
        .send(new Command(CommandType.RS_CHECK_ACCESS).setParamsObject(params))
        .dataAsResponse(RsCheckAccessResponse.class);

Assert.assertNotNull(response);
Assert.assertTrue(StringUtils.isNotBlank(response.getAccess()));
return response;
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

***Resource is not Protected:***

```javascript
{
    "status":"error",
    "data":{
        "error":"invalid_request",
        "error_description":"Resource is not protected. Please protect your resource first with uma_rs_protect command."
    }
}
```


### RP Get RPT 

The method `send` is called in order to obtain the RPT (Requesting Party Token). 

**Parameters:**

- oxdId: oxd ID from client registration
- ticket: Client Access Ticket generated by uma_rs_check_access method
- claim_token: (Optional)
- claim_token_format: (Optional)
- pct: (Optional) Persisted Claims Token
- rpt: (Optional) Requesting Party Token. 
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- state: (Optional) state that is returned from uma_rp_get_claims_gathering_url method
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)
- host: the URL of the oxd-server
- port: the port of the oxd-server

**Request:**

```java
CommandClient client = null;
try {
    client = new CommandClient(host, port);

    RegisterSiteResponse site = RegisterSiteTest.registerSite(client, opHost, redirectUrl);

    RsProtectTest.protectResources(client, site, UmaFullTest.resourceList(rsProtect).getResources());

    final RsCheckAccessResponse checkAccess = RsCheckAccessTest.checkAccess(client, site);

    final RpGetRptParams params = new RpGetRptParams();
    params.setOxdId(site.getOxdId());
    params.setTicket(checkAccess.getTicket());

    final RpGetRptResponse response = client
            .send(new Command(CommandType.RP_GET_RPT).setParamsObject(params))
            .dataAsResponse(RpGetRptResponse.class);

    assertNotNull(response);
    assertTrue(StringUtils.isNotBlank(response.getRpt()));
    assertTrue(StringUtils.isNotBlank(response.getPct()));

} finally {
    CommandClient.closeQuietly(client);
}
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


### RP Get Claims Gathering URL 

**Parameters:**

- oxdId: oxd ID from client registration
- ticket: Client Access Ticket generated by uma_rs_check_access method
- claims_redirect_uri: The URI to which the client wishes the authorization server to direct the requesting party’s user agent after completing its interaction
- protection_access_token: Generated from get_client_token method (Optional, required if oxd-https-extension is used)

**Request:**

```java
CommandClient client = null;
try {
    client = new CommandClient(host, port);

    RegisterSiteResponse site = RegisterSiteTest.registerSite(client, opHost, redirectUrl);

    RsProtectTest.protectResources(client, site, UmaFullTest.resourceList(rsProtect).getResources());

    final RsCheckAccessResponse checkAccess = RsCheckAccessTest.checkAccess(client, site);

    final RpGetClaimsGatheringUrlParams params = new RpGetClaimsGatheringUrlParams();
    params.setOxdId(site.getOxdId());
    params.setTicket(checkAccess.getTicket());
	params.setClaimsRedirectUri(claims_redirect_uri);

    final RpGetClaimsGatheringUrlResponse response = client
            .send(new Command(CommandType.RP_GET_CLAIMS_GATHERING_URL).setParamsObject(params))
            .dataAsResponse(RpGetClaimsGatheringUrlResponse.class);

    assertNotNull(response);
    assertTrue(StringUtils.isNotBlank(response.getUrl()));

} finally {
    CommandClient.closeQuietly(client);
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

Please report technical issues and suspected bugs on 
our [Support Page](https://support.gluu.org/). You can use the same 
credentials you created to register your oxd license to sign in on Gluu support.
