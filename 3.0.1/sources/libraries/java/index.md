# oxd Java

##Overview
The following documentation demonstrates how to use Gluu's commercial OAuth 2.0 client software, [oxd](http://oxd.gluu.org), to send users from a Java app to an OpenID Connect Provider (OP) for login. You can securely send users to any standard OP for login, including Google and the [free open source Gluu Server](http://gluu.org/gluu-server).

## Installation

* [Github sources](https://github.com/GluuFederation/oxd)
* Jar files are available on the [Maven repo](http://ox.gluu.org/maven/org/xdi/oxd-java/)
* [Jenkins build server](https://ox.gluu.org/jenkins/job/oxd-java/ws/target/)
* [Tests on github](https://github.com/GluuFederation/oxd-java/blob/master/src/test/java/org/xdi/oxd/client)
* [API Documentation (Javadocs)](https://ox.gluu.org/maven/org/xdi/oxd/3.0.1/)

## Configuration

There are no configuration files for oxd-java. Redirect URI and
other information is set in the code.

## Sample code

### Register 
```java
 CommandClient client = null;
 try {
     client = new CommandClient(host, port);

     final RegisterSiteParams commandParams = new RegisterSiteParams();
     commandParams.setOpHost(opHost);
     commandParams.setAuthorizationRedirectUri(redirectUrl);
     commandParams.setPostLogoutRedirectUri(postLogoutRedirectUrl);
     commandParams.setClientLogoutUri(Lists.newArrayList(logoutUri));
     commandParams.setScope(Lists.newArrayList("openid", "uma_protection", "uma_authorization"));

     final Command command = new Command(CommandType.REGISTER_SITE);
     command.setParamsObject(commandParams);

     final RegisterSiteResponse site = client.send(command).dataAsResponse(RegisterSiteResponse.class);

     // more code here
 } finally {
     CommandClient.closeQuietly(client);
 }
```

### Get Authorization URL
```java
final GetAuthorizationUrlParams commandParams = new GetAuthorizationUrlParams();
commandParams.setOxdId(site.getOxdId());

final Command command = new Command(CommandType.GET_AUTHORIZATION_URL);
command.setParamsObject(commandParams);

final GetAuthorizationUrlResponse resp = client.send(command).dataAsResponse(GetAuthorizationUrlResponse.class);
String authorizationUrl = resp.getAuthorizationUrl());
```

### Get Tokens 
```java

// after login to Authorization Server (authorizationUrl) it redirects back to redirect_uri (registered by register_site command)
// and returns back code. This code must be used to obtain tokens

final GetTokensByCodeParams commandParams = new GetTokensByCodeParams();
commandParams.setOxdId(site.getOxdId());
commandParams.setCode(code);

final Command command = new Command(CommandType.GET_TOKENS_BY_CODE).setParamsObject(commandParams);

final GetTokensByCodeResponse resp = client.send(command).dataAsResponse(GetTokensByCodeResponse.class);
String accessToken = resp.getAccessToken();
String idToken = resp.getIdToken();

```

### Get User Info

```java
CommandClient client = null;
try {
    client = new CommandClient(host, port);

    final RegisterSiteResponse site = RegisterSiteTest.registerSite(client, opHost, redirectUrl);
    final GetTokensByCodeResponse tokens = requestTokens(client, site, userId, userSecret);

    GetUserInfoParams params = new GetUserInfoParams();
    params.setOxdId(site.getOxdId());
    params.setAccessToken(tokens.getAccessToken());

    final GetUserInfoResponse resp = client.send(new Command(CommandType.GET_USER_INFO).setParamsObject(params)).dataAsResponse(GetUserInfoResponse.class);
} finally {
    CommandClient.closeQuietly(client);
}

```

### Logout

```java
 CommandClient client = null;
 try {
     client = new CommandClient(host, port);

     final RegisterSiteResponse site = RegisterSiteTest.registerSite(client, opHost, redirectUrl, postLogoutRedirectUrl, "");

     final GetLogoutUrlParams commandParams = new GetLogoutUrlParams();
     commandParams.setOxdId(site.getOxdId());
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


### Update Site

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

### UMA RS Resource protection

```java
final RsProtectParams commandParams = new RsProtectParams();
commandParams.setOxdId(site.getOxdId());
commandParams.setResources(UmaFullTest.resourceList(rsProtect).getResources());

final Command command = new Command(CommandType.RS_PROTECT).setParamsObject(commandParams);

final RsProtectResponse resp = client.send(command).dataAsResponse(RsProtectResponse.class);
```

### UMA RS Check access

```java
final RsCheckAccessParams params = new RsCheckAccessParams();
params.setOxdId(site.getOxdId());
params.setHttpMethod("GET");
params.setPath("/rest/photo");
params.setRpt("d6s-54asr-vfgm6-388dsl");

final Command command = new Command(CommandType.RS_CHECK_ACCESS).setParamsObject(commandParams);

final RsCheckAccessResponse resp = client.send(command).dataAsResponse(RsCheckAccessResponse.class);
```

### UMA Get RPT

```java
final RpGetRptParams params = new RpGetRptParams();
params.setOxdId(site.getOxdId());

final Command command = new Command(CommandType.RP_GET_RPT);
command.setParamsObject(params);
final CommandResponse response = client.send(command);

final RpGetRptResponse rptResponse = response.dataAsResponse(RpGetRptResponse.class);
```

### UMA Get GAT

```java
final RpGetGatParams params = new RpGetGatParams();
params.setOxdId(site.getOxdId());
params.setScopes(Lists.newArrayList("http://photoz.example.com/dev/actions/all"));

final Command command = new Command(CommandType.RP_GET_GAT);
command.setParamsObject(params);
final CommandResponse response = client.send(command);

final RpGetRptResponse rptResponse = response.dataAsResponse(RpGetRptResponse.class);
```
