# oxd-spring
## Overview
The following documentation demonstrates 
how to use Gluu's commercial OAuth 2.0 client software, [oxd](http://oxd.gluu.org), to send users from a Java Spring app to an OpenID Connect Provider (OP) for login. You can send users to any standard OP for login, including Google. In these docs we use the [free open source Gluu Server](http://gluu.org/gluu-server) as the OP.

!!! Note
    You can also refer to the [oxd java library](../../libraries/java/) for more details on java classes.

## Requirements
The oxd-spring requires the oxd Server. Please use the following link to install it

* [oxd Server Installation Guide](../install/)


## Install oxd-spring
Clone oxd-spring from Github repo and run maven command to install it:
```
cd oxd-spring 
mvn clean package -Dmaven.test.skip=true
```

Now you can run the executable jar:
```
java -jar target/oxd-spring-0.0.1-SNAPSHOT.jar
```

Point browser to `https://127.0.0.1:8443/`. And log in into ce-dev2.gluu.org using test credentials: test_user/test_user_password 

!!!***Note:*** 
    oxd-server must run on *localhost* and be bound to port: *8099*, otherwise you'll need to configure `oxd-spring/src/main/resources/application.properties` file.

## Customize oxd-spring
To use your own server as openid provider you need to modify `oxd.server.op-host` property from `oxd-spring/src/main/resources/application.properties`, e.g:

```
oxd.server.op-host=https://gluu.localhost.info
```

Make sure the server already installed on your machine, or you can follow 
[this](https://gluu.org/docs/ce/latest/installation-guide/install/) guide to install it.

## Sample Code

Usage of oxd-spring is very simple. First of all we need to create parameter object related to command we are going to perform and pass to related method.
Check Sample code below we are creating commandParams object  related to commands and calling related method with created params.

###  register_site

---

```java

// create registerSiteParams
try{
        final RegisterSiteParams commandParams = new RegisterSiteParams();
        commandParams.setOpHost(opHost);
        commandParams.setAuthorizationRedirectUri(redirectUrl);
        commandParams.setPostLogoutRedirectUri(postLogoutRedirectUrl);
        commandParams.setClientLogoutUri(Lists.newArrayList(logoutUrl));
        commandParams.setRedirectUris(Arrays.asList(redirectUrl));
        commandParams.setAcrValues(new ArrayList<>());
        commandParams.setScope(Lists.newArrayList("openid", "profile"));
        commandParams.setGrantType(Lists.newArrayList("authorization_code"));
        commandParams.setResponseTypes(Lists.newArrayList("code"));
        final Command command = new Command(CommandType.REGISTER_SITE).setParamsObject(commandParams);
        return client.send(command);                
 }
catch (Exception e) 
{
    e.printStackTrace();
 }
 
//oxd_ host - oxd-server host eg.localhost or 127.0.0.1 port - oxd-server listing port (default port is 8099)

```



###  update_site_registration
   
---

```java

//create UpdateSiteParams
 try {
        final UpdateSiteParams commandParams = new UpdateSiteParams();
        commandParams.setOxdId(oxdId);
        commandParams.setAuthorizationRedirectUri(redirectUrl);
        final Command command = new Command(CommandType.UPDATE_SITE).setParamsObject(commandParams);
        return client.send(command);
}
catch (Exception e) {
  e.printStackTrace();
 }
```


### get_authorization_url

---

```java

try
{
        final GetAuthorizationUrlParams commandParams = new GetAuthorizationUrlParams();
        commandParams.setOxdId(oxdId);
        final Command command = new Command(CommandType.GET_AUTHORIZATION_URL).setParamsObject(commandParams);
        return client.send(command);
}
catch (Exception e) {
  e.printStackTrace();
 }        
```



### get_tokens_by_code

---
```java
try
{
        final GetTokensByCodeParams commandParams = new GetTokensByCodeParams();
        commandParams.setOxdId(oxdId);
        commandParams.setCode(code);
        commandParams.setState(state);
        final Command command = new Command(CommandType.GET_TOKENS_BY_CODE).setParamsObject(commandParams);
        return client.send(command);
}
 catch (Exception e) {
   e.printStackTrace();
  }
```


### get_user_info

---
```java
 
 try
 {
        GetUserInfoParams params = new GetUserInfoParams();
        params.setOxdId(oxdId);
        params.setAccessToken(accessToken);
        final Command command = new Command(CommandType.GET_USER_INFO).setParamsObject(params);
        return client.send(command);
 }
catch (Exception e) {
  e.printStackTrace();
 }            
            
```

### get_logout_uri

---
  
```java
//create GetLogoutUrlParams
try{
        GetLogoutUrlParams params = new GetLogoutUrlParams();
        params.setOxdId(oxdId);
        params.setIdTokenHint(idToken);
        final Command command = new Command(CommandType.GET_LOGOUT_URI).setParamsObject(params);
        return client.send(command);       
}
catch (Exception e) {
  e.printStackTrace();
 }        

```

----


