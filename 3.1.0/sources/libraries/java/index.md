# oxd-java

## Overview

The following documentation demonstrates how to use 
the [oxd client software](http://oxd.gluu.org) Java library to send users from 
a Java application to an OpenID Connect Provider (OP), like the 
[Gluu Server](https://gluu.org/gluu-server) or Google, for login. 

## Sample Project

[Download a sample project](https://github.com/GluuFederation/oxd-java/archive/master.zip) 
specific to this oxd library.

### System Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows 7 or higher / Windows Server 2008 or higher
- Java 7 or higher
- Apache 2.4.4 or higher
- oxd-java 3.0.1


## Prerequisites

### Required Software
To use the oxd Java library, you will need:

- A valid OpenID Connect Provider (OP), like Google or the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/).    
- An active installation of the [oxd server](../../install/index.md) running in the same server as the client application.      
- A Windows server or Windows installed machine / Linux server or Linux installed machine meeting system requirements.

### Install oxd-java from Maven

Get oxd-java Jar files from [Maven repo](http://ox.gluu.org/maven/org/xdi/oxd-java/)

### Configure the Client Application

- There are no configuration files for oxd-java. Redirect URI and other information is set in the code.
- Your client application must have a valid ssl cert, so the url includes: `https://`    
- The client host name should be a valid `hostname`(FQDN), not localhost or an IP Address. 
You can configure the host name by adding the following entry in your host file.

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`
    
- Enable SSL by	adding the following lines on virtual host file of Apache in the location:

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

## oxd Server Methods

The oxd server provides the following six methods for authenticating users with an OpenID Connect Provider (OP):

- [Register Site](../../protocol/#register-site)    
- [Update Site Registration](../../protocol/#update-site-registration)    
- [Get Authorization URL](../../protocol/#get-authorization-url)   
- [Get Tokens by Code](../../protocol/#get-tokens-id-access-by-code)    
- [Get User Info](../../protocol/#get-user-info)   
- [Get Logout URI](../../protocol/#log-out-uri) 



## Sample code
Below is a sample pom, for more of pom file, please refer to [snippet](https://ox.gluu.org/maven/org/xdi/oxd-client/3.1.0.Final/oxd-client-3.1.0.Final.pom)

```
<dependency>
  <artifactId>oxd-client</artifactId>
  <groupId>org.xdi</groupId>
  <version>3.1.0.Final</version>
</dependency>
```

### Register Site

In order to use an OpenID Connect Provider (OP) for login, you need to register your client application at the OP. During registration oxd will dynamically register the OpenID Connect client and save its configuration. Upon successful registration a unique identifier will be issued by the oxd server. If your OpenID Connect Provider does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be passed to the `send` method as a parameter. The Register Site method is a one time task to configure a client in the oxd server and OP.

**Required parameters:**

- host: the URL of the oxd server 
- port: the port of the oxd server 
- opHost: The URL of the OpenID Connect Provider (OP)
- redirectUrl: A URL which the OP is authorized to redirect the user after authorization.
- postLogoutRedirectUrl: A URL which the OP is authorized to redirect the user after logout.
- logoutUri: logout uri of the client

**Request:**

```java
 CommandClient client = null;
 try {
     client = new CommandClient(host, port);

     final RegisterSiteParams commandParams = new RegisterSiteParams();
     commandParams.setOpHost(opHost);
     commandParams.setAuthorizationRedirectUri(redirectUrl);
     commandParams.setPostLogoutRedirectUri(postLogoutRedirectUrl);
     commandParams.setClientLogoutUri(Lists.newArrayList(logoutUri));
     commandParams.setScope(Lists.newArrayList("openid", "profile", "email"));

     final Command command = new Command(CommandType.REGISTER_SITE);
     command.setParamsObject(commandParams);

     final RegisterSiteResponse site = client.send(command).dataAsResponse(RegisterSiteResponse.class);

     // more code here
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

The `send` method can be used to update an existing client in the OP. Fields like Authorization redirect url, post logout url, scope, client secret and other fields, can be updated using this method.

**Required parameters:**

- host: the URL of the oxd server 
- port: the port of the oxd server 
- oxd_Id: oxd Id from client registration

**Request:**

```java
CommandClient client = null;
try {
     client = new CommandClient(host, port);

     Calendar calendar = Calendar.getInstance();
     calendar.add(Calendar.DAY_OF_YEAR, 1);

     // more specific site registration
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

The `send` method returns the OpenID Connect Provider authentication URL to which the client application must redirect the user to authorize the release of personal data. The response URL includes state value, which can be used to obtain tokens required for authentication. This state value used to maintain state between the request and the callback.

**Required parameters:**

- oxd_Id: oxd Id from client registration

**Request:**

```java
final GetAuthorizationUrlParams commandParams = new GetAuthorizationUrlParams();
commandParams.setOxdId(oxdId);

final Command command = new Command(CommandType.GET_AUTHORIZATION_URL);
command.setParamsObject(commandParams);

final GetAuthorizationUrlResponse resp = client.send(command).dataAsResponse(GetAuthorizationUrlResponse.class);
String authorizationUrl = resp.getAuthorizationUrl());
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

**Required parameters:**

- oxd_Id: oxd Id from client registration
- code: The Code from OP authorization redirect url

**Request:**

```java
final GetTokensByCodeParams commandParams = new GetTokensByCodeParams();
commandParams.setOxdId(oxdId);
commandParams.setCode(code);

final Command command = new Command(CommandType.GET_TOKENS_BY_CODE).setParamsObject(commandParams);

final GetTokensByCodeResponse resp = client.send(command).dataAsResponse(GetTokensByCodeResponse.class);
String accessToken = resp.getAccessToken();
String idToken = resp.getIdToken();
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

Once the user has been authenticated by the OpenID Connect Provider, the `send` method returns Claims (Like First Name, Last Name, emailId, etc.) about the authenticated end user.

**Required parameters:**

- host: the URL of the oxd server 
- port: the port of the oxd server 
- oxd_Id: oxd Id from client registration
- accessToken: accessToken from GetTokenByCode

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

`send` method returns the OpenID Connect Provider logout url. Client application  uses this logout url to end the user session.

**Required parameters:**

- host: the URL of the oxd server 
- port: the port of the oxd server 
- oxd_Id: oxd Id from client registration

```java
 CommandClient client = null;
 try {
     client = new CommandClient(host, port);

     final RegisterSiteResponse site = RegisterSiteTest.registerSite(client, opHost, redirectUrl, postLogoutRedirectUrl, "");

     final GetLogoutUrlParams commandParams = new GetLogoutUrlParams();
     commandParams.setOxdId(site.getOxdId());//oxdId
     commandParams.setIdTokenHint(idTokenHint);
     commandParams.setPostLogoutRedirectUri(postLogoutRedirectUrl);
     commandParams.setState(UUID.randomUUID().toString());
     commandParams.setSessionState(UUID.randomUUID().toString()); // here must be real session instead of dummy UUID

     final Command command = new Command(CommandType.GET_LOGOUT_URI).setParamsObject(commandParams);

     final LogoutResponse resp = client.send(command).dataAsResponse(LogoutResponse.class);
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


## Support

Please report technical issues and suspected bugs on 
our [support page](https://support.gluu.org/). You can use the same 
credentials you created to register for your oxd license to sign in on Gluu support.
