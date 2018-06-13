# oxd-java

## Overview

Use the oxd Java library to:

- Easily implement user authentication with an OpenID Connect Provider (OP) following the steps of the [Authorization Code Flow](http://openid.net/specs/openid-connect-core-1_0.html#Authentication) in your Java web app

- Obtain identity information about the authenticated user

- Log out of the OP

- Implement protection of web resources enabling a resource server and a client to support the [UMA 2.0](https://docs.kantarainitiative.org/uma/ed/oauth-uma-grant-2.0-04.html) workflow in conjuction with a UMA 2.0 compliant server (such as Gluu Server)
 
## Sample Project

In [this repo](https://github.com/GluuFederation/oxd-java-sample), you can find a Java web project that showcases how to integrate the library and illustrates the step-by-step process of OpenId Authentication.

Check the [readme](https://github.com/GluuFederation/oxd-java-sample/blob/master/README.md) and learn how easy it is to get it up and running.

## Operations summary and sample code 

In this section, code snippets that exemplify the authentication steps of OpenID Connect are presented. They resemble the operations listed in the [API page](https://gluu.org/docs/oxd/api/) using Java. Use the hints given in the API page to determine which parameters are required for each operation and learn more about the operations intent.

### Requisites

In your Maven project, add the following dependency to your `pom.xml`:

```
<dependency>
  <artifactId>oxd-client</artifactId>
  <groupId>org.xdi</groupId>
  <version>3.1.2.Final</version>
</dependency>
```

### OpenID Connect

#### Setup Client

!!! Note: 
    Use this operation only when communication with oxd is established via HTTPS.

The purpose of Setup Client is similar to that of [Register Site](#register-site), registering a new OpenID Client at your OP and retrieving an "**oxd_id**" (an identifier that must be passed when calling the rest of operations except [Get Client Token](#get-client-token)).

Setup Client additionally returns a **client_id** and a **client_secret**. These two pieces of data are needed when calling the [Get Client Token](#get-client-token) operation.

If your OP does not support dynamic registration (e.g. Google), you will need to obtain a **client_id** and a **client_secret** yourself and supply those as parameters of this operation. This will make oxd skip the attempt to register a new client.

To learn about the parameters supported by this operation, check the [API page](https://gluu.org/docs/oxd/api/#setup-client) and the "Client Metadata" section of [OpenID Connect Dynamic Client Registration 1.0](http://openid.net/specs/openid-connect-registration-1_0.html#ClientMetadata).

A working example can be found in the sample project: See method `doRegistrationHttps` in the [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/master/src/main/java/org/xdi/oxd/sample/bean/OxdService.java) class.

Recall that Setup Client is a one-time task.

#### Get Client Token

!!! Note:
    Use this operation only when communication with oxd is established via HTTPS.

When you use the oxd HTTPS extension, all API operations (except Setup Client) must be protected by an access token. Get Client Token allows you to obtain such a "protection API token" very easily: Just provide the **client_id** and **client_secret** you obtained in the call to [Setup Client](#setup-client).

Besides a token, this operation will give you additional information such as an expiration period in seconds. Once the expiration time has elapsed, the access token is no longer valid and you should request a new one by using this operation or by a call to [Get Access Token by Refresh Token](#get-access-token-by-refresh-token).

To learn more about the input and output of this operation check the [API page](https://gluu.org/docs/oxd/api/#get-client-token).

A working example can be found in the sample project: See method `getPAT` in class [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/master/src/main/java/org/xdi/oxd/sample/bean/OxdService.java). There, for simplicity a new PAT is requested every time an operation is called (the expiration time is not being used).

#### Register Site

!!! Note: 
    You can skip this operation if you already used [Setup Client](#setup-client)

Think of this operation as a means to introduce your website (app) to oxd. It returns an identifier that must be passed when calling any API operation (except for the two listed above). This is called the "**oxd_id**".

This operation will attempt to register a new OpenID Client at your OP. Remember that if your OP does not support dynamic registration (e.g. Google), you have to obtain a **client_id** and a **client_secret** yourself and supply those as parameters of this operation. This will make oxd skip the client registration.

To learn about the parameters supported by this operation check the [API page](https://gluu.org/docs/oxd/api/#setup-client) and the "Client Metadata" section of [OpenID Connect Dynamic Client Registration 1.0](http://openid.net/specs/openid-connect-registration-1_0.html#ClientMetadata). Worth to mention is the `authorization_redirect_uri`, a URL where the OP will redirect the user's browser after successful authentication.

A working example can be found in the sample project: See method `doRegistrationSocket` in the [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/master/src/main/java/org/xdi/oxd/sample/bean/OxdService.java) class.

Recall that Register Site is a one-time task.


#### Update Site Registration

This operation is aimed at updating a current registration. The parameters are equivalent to those of [Register Site](#register-site), in addition to **oxd_id**.

To learn about the parameters supported by this operation, check the [API page](https://gluu.org/docs/oxd/api/#update-site-registration)

A typical use case could be extending the lifetime of a client: When using dynamic registration in Gluu Server, the client is created with an expiration time (around one day by default). If your application is a long-running one, you may like to set a value further in the future. Here is an example of how to do so:

```
//client should be a global variable (host and port are those of oxd-server)
CommandClient client=new CommandClient(host, port);

GregorianCalendar cal=new GregorianCalendar();
cal.add(Calendar.YEAR, 1);

UpdateSiteParams cmdParams = new UpdateSiteParams();
cmdParams.setOxdId(oxdId);
cmdParams.setClientSecretExpiresAt(new Date(cal.getTimeInMillis()));

Command command = new Command(CommandType.UPDATE_SITE).setParamsObject(cmdParams);
UpdateSiteResponse resp = client.send(command).dataAsResponse(UpdateSiteResponse.class);

boolean updated = resp!=null;
```        

#### Get Authorization URL

This operation returns a URL to which your application must redirect the user's browser to start the authentication process at the OP.

To learn about the parameters supported by this operation, check the [API page](https://gluu.org/docs/oxd/api/#get-authorization-url)

A working example can be found in the sample project: See method `getAuthzUrl` in class [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/master/src/main/java/org/xdi/oxd/sample/bean/OxdService.java).


#### Get Tokens by Code

After authentication, the OP sends the user's browser back to the `redirect_uri` page with a couple of query parameters in the URL: `code` and `state`. Use those to issue a call to this API operation.

As a response, you will get an access token (not to be confused with the token of [Get Client Token](#get-client-token) operation), as well as an ID Token. The former token is used to call [Get User Info](#get-user-info) operation while the latter when calling [Get Logout URI](#get-logout-uri).

A working example can be found at sample project: See method `GetTokensByCodeResponse` in class [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/master/src/main/java/org/xdi/oxd/sample/bean/OxdService.java).

#### Get Access Token by Refresh Token

The access token obtained at [Get Tokens by Code](#get-tokens-by-code) has an expiration time, too. If your application has the need to obtain a fresher access token, this operation is useful. Simply provide the `refresh_token` that you received when the call to Get Tokens by Code was made.

To learn more about the input and output of this operation, check the [API page](https://gluu.org/docs/oxd/api/#get-access-token-by-refresh-token).

Here is an example of the usage of this operation using the standard oxd-server (not https):

```
//client should be a global variable (host and port are those of oxd-server)
CommandClient client=new CommandClient(host, port);

GetAccessTokenByRefreshTokenParams cmdParams = new GetAccessTokenByRefreshTokenParams();
cmdParams.setOxdId(oxd_id);
cmdParams.setScope(Collections.singletonList("openid"));
cmdParams.setRefreshToken(refresh_token);

Command command = new Command(CommandType.GET_ACCESS_TOKEN_BY_REFRESH_TOKEN).setParamsObject(commandParams)
GetAccessTokensByRefreshTokenResponse resp = client.send(command).dataAsResponse(GetAccessTokensByRefreshTokenResponse.class);
 
String newAccessToken=resp.getAccessToken();
String newRefreshToken=resp.getRefreshToken();
```

#### Get User Info

Use this operation to obtain user claims (e.g. first name, last name, e-mail, etc.) about the authenticated end-user. 

To learn more about the input and output of this operation, check the [API page](https://gluu.org/docs/oxd/api/#get-user-info).

The set of claims in the response depends on the access privileges associated to the access token provided. This has to do with the scopes passed in [Site Registration](#register-site) API operation.

A working example can be found in the sample project: See method `getUserInfo` in class [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/master/src/main/java/org/xdi/oxd/sample/bean/OxdService.java).

#### Get Logout URI

Use this method if you intend to log the user out of the OP. This will return a URL where you can redirect the user's browser to initiate the logout process.

To learn more about the input and output of this operation check the [API page](https://gluu.org/docs/oxd/api/#get-logout-uri).

A working example can be found at sample project: See method `getLogoutUrl` in class [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/master/src/main/java/org/xdi/oxd/sample/bean/OxdService.java).

### UMA

#### RS Protect

`send` method is used for protecting resources with the Resource Server. The Resource Server is needed to construct the command which will protect the resource.
The command will contain an API path, HTTP methods (POST, GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy is mapped, uma_rs_check_access method will always return access as granted. For more information about uma_rpt_policies you can reference this [document](https://gluu.org/docs/oxd/3.1.3/api/#uma-2-client-apis).

**Parameters:**

- oxdID: oxd ID from client registration
- resources: One or more protected resources that a resource server manages, abstractly, as a set. In authorization policy terminology, a resource set is the "object" being protected 
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

#### RS Check Access 

The `send` method is used in the UMA Resource Server to check the access to the resource.

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


#### RP Get RPT 

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


#### RP Get Claims Gathering URL 

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
