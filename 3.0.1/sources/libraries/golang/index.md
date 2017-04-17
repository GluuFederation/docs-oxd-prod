# oxd Golang

## Overview
The following documentation demonstrates 
how to use Gluu's commercial OAuth 2.0 client software, 
[oxd](http://oxd.gluu.org), to send users from a Golang app to an 
OpenID Connect Provider (OP) for login. You can securely send users to 
any standard OP for login, including Google and 
the [free open source Gluu Server](http://gluu.org/gluu-server).

## Plugin description
- src/oxd-client - client source code
- src/oxd-client-demo - simple communication demo
- src/oxd-client-test - unit tests for communication

## Deployment

Download the zip file from the 
[Github Page](https://github.com/GluuFederation/oxd-go)

##Demo
Demo shows simple web client with OPC flow. Demo configuration is located in conf/main.toml.
Demo requires public/private key pair (params: key and cert).

To start demo launch run.sh script, which will setup server on  htts://localhost:8080.

##Samples
All calls have the same structure that is described in the [Call Structure](###Call Structure) section. 
Some examples of requests are described below. Usage of each call can be found in the unit tests.
###Call Structure 
1. Prepare request params
2. Wrap into request structure
3. Prepare response and response params structures
4. Call server
5. Fetch response params

###Register client
```go
requestParams := utils.PrepareRegisterParams() //func from test utils
request := client.BuildOxdRequest(constants.REGISTER_SITE,requestParams)
var response transport.OxdResponse
var responseParams model.RegisterSiteResponseParams
  
client.Send(request,/* host */,&response)
  
response.GetParams(&responseParams)
```

###Get Authorization Url
```go
requestParams := model.AuthorizationUrlRequestParams{/*parameters*/}
request := client.BuildOxdRequest(constants.GET_AUTHORIZATION_URL,requestParams)
var response transport.OxdResponse
var responseParams model.AuthorizationUrlResponseParams
  
client.Send(request,/* host */,&response)
  
response.GetParams(&responseParams)
```

###Get Token by Code
```go
requestParams := model.TokensByCodeRequestParams{/*parameters*/}
request := client.BuildOxdRequest(constants.GET_TOKENS_BY_CODE,requestParams)
var response transport.OxdResponse
var responseParams model.TokensByCodeResponseParams
  
client.Send(request,conf.TestConfiguration.Host,&response)
  
response.GetParams(&responseParams)
```
