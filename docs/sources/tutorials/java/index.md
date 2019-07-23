# oxd tutorial - Java

## Overview

Use the oxd Java library to:

- Easily implement user authentication with an OpenID Connect Provider (OP) following the steps of the [Authorization Code Flow](http://openid.net/specs/openid-connect-core-1_0.html#Authentication) in your Java web app

- Obtain identity information about the authenticated user

- Log out of the OP
 
## Sample Project

In [this repo](https://github.com/GluuFederation/oxd-java-sample/tree/version_4.0), you can find a Java web project that showcases how to integrate the library and illustrates the step-by-step process of OpenId Authentication.

Check the [readme](https://github.com/GluuFederation/oxd-java-sample/blob/version_4.0/README.md) and learn how easy it is to get it up and running.

## Operations summary and sample code 

In this section, code snippets that exemplify the authentication steps of OpenID Connect are presented. They resemble the operations listed in the [API page](https://gluu.org/docs/oxd/4.0/api/) using Java. Use the hints given in the API page to determine which parameters are required for each operation and learn more about the operations intent.

### Requisites

In your Maven project, add the following dependency to your `pom.xml`:

```
<dependency>
    <artifactId>oxd-client</artifactId>
    <groupId>org.gluu</groupId>
    <version>4.0-SNAPSHOT</version>
</dependency>

<dependency>
    <artifactId>oxd-common</artifactId>
    <groupId>org.gluu</groupId>
    <version>4.0-SNAPSHOT</version>
</dependency>
```

### OpenID Connect

#### Register Site

The purpose of Register Site is to register a new OpenID Client at your OP and retrieving an "**oxd_id**" (an identifier that must be passed when calling the rest of operations except [Get Client Token](#get-client-token)).

Register Site additionally returns a **client_id** and a **client_secret**. These two pieces of data are needed when calling the [Get Client Token](#get-client-token) operation.

If your OP does not support dynamic registration (e.g. Google), you will need to obtain a **client_id** and a **client_secret** yourself and supply those as parameters of this operation.

To learn about the parameters supported by this operation, check the [API page](https://gluu.org/docs/oxd/4.0/api/#register-site) and the "Client Metadata" section of [OpenID Connect Dynamic Client Registration 1.0](http://openid.net/specs/openid-connect-registration-1_0.html#ClientMetadata).

A working example can be found in the sample project: See method `doRegistration` in the [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/version_4.0/src/main/java/org/gluu/oxd/sample/bean/OxdService.java) class.

Recall that Register Site is a one-time task.

#### Get Client Token

When you set `protect_commands_with_access_token` to `true` in oxd-server.yml, all API operations (except Register Site) must be protected by an access token. Get Client Token allows you to obtain such a "protection API token" very easily: Just provide **op_host**, **client_id** and **client_secret** you obtained in the call to [Register Site](#register-site).

Besides a token, this operation will give you additional information such as an expiration period in seconds. Once the expiration time has elapsed, the access token is no longer valid and you should request a new one by using this operation or by a call to [Get Access Token by Refresh Token](#get-access-token-by-refresh-token).

To learn more about the input and output of this operation check the [API page](https://gluu.org/docs/oxd/4.0/api/#get-client-token).

A working example can be found in the sample project: See method `getClientToken` in class [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/version_4.0/src/main/java/org/gluu/oxd/sample/bean/OxdService.java). There, for simplicity a new token is requested every time an operation is called.

#### Update Site Registration

This operation is aimed at updating a current registration. The parameters are equivalent to those of [Register Site](#register-site), in addition to **oxd_id**.

To learn about the parameters supported by this operation, check the [API page](https://gluu.org/docs/oxd/4.0/api/#update-site)
Here is an example of how to update `Redirect Uris` and `Scope` of client.
```
//client should be a global variable (host and port are those of oxd-server)
ClientInterface client = OxdClient.newClient(getTargetHost(host, port));

final UpdateSiteParams updateParams = new UpdateSiteParams();
updateParams.setOxdId(oxdId);
updateParams.setRedirectUris(Lists.newArrayList("https://www.application.com/home", "https://www.application.com/index"));
updateParams.setScope(Lists.newArrayList("profile"));

UpdateSiteResponse updateResponse = client.updateSite(getClientToken(), updateParams);
```        

#### Get Authorization URL

This operation returns a URL to which your application must redirect the user's browser to start the authentication process at the OP.

To learn about the parameters supported by this operation, check the [API page](https://gluu.org/docs/oxd/4.0/api/#get-authorization-url)

A working example can be found in the sample project: See method `getAuthzUrl` in class [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/version_4.0/src/main/java/org/gluu/oxd/sample/bean/OxdService.java).


#### Get Tokens by Code

After authentication, the OP sends the user's browser back to the `redirect_uri` page with a couple of query parameters in the URL: `code` and `state`. Use those to issue a call to this API operation.

As a response, you will get an access token (not to be confused with the token of [Get Client Token](#get-client-token) operation), as well as an ID Token. The former token is used to call [Get User Info](#get-user-info) operation while the latter when calling [Get Logout URI](#get-logout-uri).

A working example can be found at sample project: See method `getTokens` in class [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/version_4.0/src/main/java/org/gluu/oxd/sample/bean/OxdService.java).

#### Get Access Token by Refresh Token

The access token obtained at [Get Tokens by Code](#get-tokens-by-code) has an expiration time, too. If your application has the need to obtain a fresher access token, this operation is useful. Simply provide the `oxd_id`, `refresh_token` and `scope` that you received when the call to Get Tokens by Code was made.

To learn more about the input and output of this operation, check the [API page](https://gluu.org/docs/oxd/4.0/api/#get-access-token-by-refresh-token).

Here is an example of the usage of this operation :

```
//client should be a global variable (host and port are those of oxd-server)
ClientInterface client = OxdClient.newClient(getTargetHost(host, port));

GetAccessTokenByRefreshTokenParams refreshParams = new GetAccessTokenByRefreshTokenParams();
refreshParams.setOxdId(oxdId);
refreshParams.setScope(Lists.newArrayList("openid"));
refreshParams.setRefreshToken(refresh_token);

GetClientTokenResponse refreshResponse = client.getAccessTokenByRefreshToken(getClientToken(), refreshParams);
```

#### Get User Info

Use this operation to obtain user claims (e.g. first name, last name, e-mail, etc.) about the authenticated end-user. 

To learn more about the input and output of this operation, check the [API page](https://gluu.org/docs/oxd/4.0/api/#get-user-info).

The set of claims in the response depends on the access privileges associated to the access token provided. This has to do with the scopes passed in [Site Registration](#register-site) API operation.

A working example can be found in the sample project: See method `getUserInfo` in class [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/version_4.0/src/main/java/org/gluu/oxd/sample/bean/OxdService.java).

#### Get Logout URI

Use this method if you intend to log the user out of the OP. This will return a URL where you can redirect the user's browser to initiate the logout process.

To learn more about the input and output of this operation check the [API page](https://gluu.org/docs/oxd/4.0/api/#get-logout-uri).

A working example can be found at sample project: See method `getLogoutUrl` in class [OxdService](https://github.com/GluuFederation/oxd-java-sample/blob/master/src/main/java/org/xdi/oxd/sample/bean/OxdService.java).

### UMA

#### UMA RS Protect

Register a protected resource with the umaRsProtect method. The command will contain an API path, HTTP methods (POST, GET and PUT) and scopes. Scopes can be mapped with authorization policy (uma_rpt_policies). If no authorization policy is mapped, uma_rs_check_access method will always return access as granted. For more information about uma_rpt_policies you can reference this [document](https://gluu.org/docs/oxd/4.0/api/#uma-rs-protect-resources).

**Parameters:**

- oxdID: oxd ID from client registration
- resources: One or more protected resources that a resource server manages, abstractly, as a set. In authorization policy terminology, a resource set is the "object" being protected 

**Request:**

```java
final RsProtectParams2 params = new RsProtectParams2();
params.setOxdId(site.getOxdId());
commandParams.setResources(Jackson2.createJsonMapper().readTree(Jackson2.asJsonSilently(resources_string)));

RsProtectResponse resp = client.umaRsProtect(getClientToken(), params);
```

#### UMA RS Check Access

The `umaRsCheckAccess` method is used in the UMA Resource Server to check the access to the resource. For more information about uma-rs-check-access you can reference this [document](https://gluu.org/docs/oxd/4.0/api/#uma-rs-check-access).

**Parameters:**

- oxdId: oxd ID from client registration
- rpt: Requesting Party Token
- path: Path of the resource to be checked 
- http_method: HTTP methods (POST, GET and PUT)

**Request:**

```java
RsCheckAccessParams params = new RsCheckAccessParams();
params.setOxdId(site.getOxdId());
params.setHttpMethod("GET");
params.setPath("/GetAll");
params.setRpt("");

final RsCheckAccessResponse response = client.umaRsCheckAccess(getClientToken(), params);
```


***Access Denied with Ticket Response:***

```javascript

HTTP/1.1 200 OK
{
    "access":"denied"
    "www-authenticate_header":"UMA realm=\"example\",
                               as_uri=\"https://as.example.com\",
                               error=\"insufficient_scope\",
                               ticket=\"016f84e8-f9b9-11e0-bd6f-0021cc6004de\"",
    "ticket":"016f84e8-f9b9-11e0-bd6f-0021cc6004de"
}
```

***Access Denied without Ticket Response:***

```javascript
HTTP/1.1 200 OK
{
    "access":"denied"
}
```

***Resource is not Protected:***

```javascript
HTTP/1.1 400 Bad request
{
    "error":"invalid_request",
    "error_description":"Resource is not protected. Please protect your resource first with uma_rs_protect command."
}
```


#### RP Get RPT 

The method `umaRpGetRpt` is called in order to obtain the RPT (Requesting Party Token). For more information about uma-rp-get-rpt you can reference this [document](https://gluu.org/docs/oxd/4.0/api/#uma-rp-get-rpt).

**Parameters:**

- oxdId: oxd ID from client registration
- ticket: Client Access Ticket generated by uma_rs_check_access method
- claim_token: (Optional)
- claim_token_format: (Optional)
- pct: (Optional) Persisted Claims Token
- rpt: (Optional) Requesting Party Token. 
- scope: (Optional) A scope is an indication by the client that it wants to access some resource provided by the OpenID Connect Provider (OP)
- state: (Optional) state that is returned from uma_rp_get_claims_gathering_url method

**Request:**

```java

RpGetRptParams params = new RpGetRptParams();
params.setOxdId(OxdId);
params.setTicket(checkAccess.getTicket());

final RpGetRptResponse response = client.umaRpGetRpt(getClientToken(), params);
```

**Response:**

***Success Response:***

```javascript
HTTP 1.1 200 OK
{
     "access_token":"SSJHBSUSSJHVhjsgvhsgvshgsv",
     "token_type":"Bearer",
     "pct":"c2F2ZWRjb25zZW50",
     "upgraded":true
}
```

***Needs Info Error Response:***

```javascript
HTTP 1.1 403 Forbidden
{
  "error": "need_info",
  "ticket": "38569d82-6e4a-4287-aac8-1d9ea2b8c439",
  "required_claims": [
    {
      "issuer": [
        "https://gluu.local.org"
      ],
      "name": "country",
      "claim_token_format": [
        "http://openid.net/specs/openid-connect-core-1_0.html#IDToken"
      ],
      "claim_type": "string",
      "friendly_name": "country"
    },
    {
      "issuer": [
        "https://gluu.local.org"
      ],
      "name": "city",
      "claim_token_format": [
        "http://openid.net/specs/openid-connect-core-1_0.html#IDToken"
      ],
      "claim_type": "string",
      "friendly_name": "city"
    }
  ],
  "redirect_user": "https://gluu.local.org/oxauth/restv1/uma/gather_claims?customUserParam2=value2&customUserParam1=value1&client_id=@!B28D.DF29.C16D.8E6F!0001!5489.C322!0008!7946.30C2.ACFC.73D8&ticket=38569d82-6e4a-4287-aac8-1d9ea2b8c439"
}
```

#### UMA RP - Get Claims-Gathering URL

**Parameters:**

- oxdId: oxd ID from client registration
- ticket: Client Access Ticket generated by uma_rs_check_access method
- claims_redirect_uri: The URI to which the client wishes the authorization server to direct the requesting party’s user agent after completing its interaction

**Request:**

```java
final RpGetClaimsGatheringUrlParams params = new RpGetClaimsGatheringUrlParams();
params.setOxdId(OxdId);
params.setTicket(checkAccess.getTicket());
params.setClaimsRedirectUri(paramRedirectUrl);
params.setState(state);

final RpGetClaimsGatheringUrlResponse response = client.umaRpGetClaimsGatheringUrl(getClientToken(), params);
```

## Support

Please report technical issues and suspected bugs on 
our [Support Page](https://support.gluu.org/).
