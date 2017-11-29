# oxd-java-play

Use oxd's Java Play library to send users from a Play application to your Gluu Server OpenID Connect Provider (OP) for dynamic enrollment, single sign-on (SSO), strong authentication, and access management policy enforcement. 

!!! Note
    You can also refer to the [oxd-java library](../../languages/java/index.md) docs for more details on java classes.
    
## Installation

Installation of oxd-play is very easy task With help of Maven and sbt.
To use maven  adding following line in build.sbt and sbt build will do rest for you.

    resolvers += "Gluu repository" at "http://ox.gluu.org/maven"

    libraryDependencies += "org.xdi" % "oxd-java" % "2.4.4.Final"

    libraryDependencies += "oxd.play.java" % "oxd-play" % "2.4.4-FINAL"


 **Import Oxd-Command class** (all are static methods of "oxdCommands" class.)

---

Import oxdCommands class from oxd-play by adding this oxd. 

    import static org.xdi.oxd.client.oxdCommands.*;



-----------------------------------------------------------------------



!!!**Note: 
    empty line required between every single line because sbt build use empty line as line separator**



* [GitHub source code](https://github.com/GluuFederation/oxd-play)

* [For demo project](https://github.com/GluuFederation/oxd-play/tree/master/oxd-play-client)

* Jar files are available on the [Maven repo](http://ox.gluu.org/maven/oxd/play/java/oxd-play/)

* [Jenkins build server](https://ox.gluu.org/jenkins/job/oxd-play/)


##Configuration

We need nothing to configuration before start using oxd-play everything can be set on run time but still we can configure our oxd-server's default configurations. 

!!!**Note:** 
    The website is registered with the OP and its ID is stored in this config file, also are the other peristant information about the website. So the config file needs to be writable for the server.

## Sample Code

Usage of Oxd-play is very simple. First of all we need to create parameter object related to command we are going to perform and pass to related method.
Check Sample code below we are creating commandParams object  related to commands and calling related method with created params.

when you call method as you will also pass a callback which can return result of operation.callback have two methods success and error.In success you will get a response from server and if any error occurs you will get a error message to simplify error. 

### register_site

---

```java

// create registerSiteParams
try{
        final RegisterSiteParams registerSiteParams = new RegisterSiteParams();
        commandParams.setAuthorizationRedirectUri(redirectUrl);//Required and must be https

// Call "registerSite" method using created registerSiteParams

        registerSite(oxd_host,oxd_port, registerSiteParams, new RegisterSiteCallback() {
                    @Override
                    public void success(RegisterSiteResponse registerSiteResponse) {
                    //this is your successful response for register_site command
                    //registerSiteResponse.getOxdId() to get oxdid returened by server.                  
                    }
                    @Override
                    public void error(String s) {
                    //returns error message
                    }
                });
                
 }
catch (Exception e) 
{
    e.printStackTrace();
 }
 
//oxd_ host - oxd-server host eg.localhost or 127.0.0.1 port - oxd-server listing port (default port is 8099)

```



### update_site_registration
   
---

```java

//create UpdateSiteParams
 try {
        final UpdateSiteParams commandParams = new UpdateSiteParams();
        commandParams.setOxdId("Registered Sites oxd_id");//Required

//Call "updateSite" method using created registerSiteParams

        updateSite(oxd_host,oxd_port, UpdateSiteParams, new UpdateSiteCallback() {
                   @Override
                   public void success(UpdateSiteResponse updateSiteResponse) {
                         //this is your successful response for update_site__registration command 
                         //updateSiteResponse.getOxdId() to get Oxd returened by server.
                        }
                    @Override
                     public void error(String s) {
                    //returns error message
                      }
        });
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
//create GetAuthorizationUrlParams

            GetAuthorizationUrlParams commandParams = new GetAuthorizationUrlParams();
            commandParams.setOxdId("Registered Sites oxd_id");//required
            commandParams.setAcrValues("List of arc values"); //optional
            commandParams.setState("State from redirected uri");//optional
            commandParams.setScopes("Scope from redirected uri");//required

// Call "getAuthorizationUrl" method using created GetAuthorizationUrlParams

            getAuthorizationUrl(oxd_host,oxd_port,GetAuthorizationUrlParams, new GetAuthorizationUrlCallback() {
                    @Override
                   public void success(GetAuthorizationUrlResponse getAuthorizationUrlResponse) {
                                     //successful  call will return getAuthorizationUrlResponse
                                    //getAuthorizationUrlResponse.getAuthorizationUrl() will return authorization url to redirect
                  }
                 @Override
                public void error(String s) {
                          //returns error message
                }
        });
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
//create GetTokensByCodeParams

            GetTokensByCodeParams getTokensByCodeParams = new GetTokensByCodeParams();
            commandParams.setOxdId("Registered Site oxd_id code");//required
            commandParams.setState("State from op redirected uri");//optional
            commandParams.setScopes("Scope from op redirected uri");//required
            commandParams.setCode("Code from op redirected uri");//required

// Call "getToken" method using created GetTokensByCodeParams

            getToken(host, port, getTokensByCodeParams, new GetTokensByCodeCallback() {
                         public void success(GetTokensByCodeResponse getTokensByCodeResponse) {
                                 //successful  call will return GetTokensByCodeResponse
                                 //getTokensByCodeResponse.getAccessToken() to get access Token
                            }

                         @Override
                            public void error(String s) {
                                 //will return error message if any
                            }
                });
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
 //create GetUserInfoParams

        GetUserInfoParams getUserInfoParams = new GetUserInfoParams();
        getUserInfoParams.setOxdId("Regitered site's oxd_id");
        getUserInfoParams.setAccessToken("Access token from GetTokensByCode call");

// Call "getUserInfo" method using created GetTokensByCodeParams

    getUserInfo(oxd_host,oxd_port, getUserInfoParams, new GetUserInfoCallback() {
            @Override
            public void success(GetUserInfoResponse getUserInfoResponse) {
                   //successful  call will return GetUserInfoResponse
                   //getUserInfoResponse.getClaims() Will return Hash map with calimed user informations.
                }
                @Override
                public void error(String s) {
                    //will return error message if any
                }
            });
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
       final GetLogoutUrlParams getLogoutUrlParams = new GetLogoutUrlParams();
                commandParams.setOxdId("Registered site's oxd_id"); //     required


// Call "getLogoutUri" method using created GetLogoutUrlParams

        getLogoutUri(oxd_host,oxd_port, getLogoutUrlParams, new GetlogoutUrlCallback() {
            @Override
            public void success(LogoutResponse AlogoutResponse) {
                //successful  call will return LogoutResponse
                //AlogoutResponse.getUri() will return uri to be redirected 
            }
            @Override
            public void error(String s) {
                //will return error message if any
            }
        });
        
}
catch (Exception e) {
  e.printStackTrace();
 }        

```

----


!!!**Note:** 
    You can also refer "[OXD_JAVA](https://oxd.gluu.org/docs/libraries/java/)" for more details of java classes**
