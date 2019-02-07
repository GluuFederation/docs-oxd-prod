# oxd 4.0.beta Documentation

## Introduction
oxd exposes simple, static APIs web application developers can use to implement user authentication and authorization against an OAuth 2.0 authorization server like [Gluu](https://gluu.org/docs/ce). 

## Architecture 
The oxd Linux package includes the `oxd-server` which is a simple REST application.

### oxd-server
`oxd-server` is designed to work over the web, making it possible for many apps across many servers to leverage a central oxd service for OAuth 2.0 security.

![oxd-https-architecture](./img/oxd-https.jpg) 

## Get Started

To get started:

1. [Install](./install/index.md) `oxd-server` 

1. [Configure](./configuration/index.md) the `oxd-server`           

1. [Start](./install/index.md) the `oxd-server`, as described in the installation docs 

1. Check [oxd API](./api/index.md) to understand how oxd works.

## Tutorial

Look into our [tutorial](./tutorial/index.md) that shows how to get simple python application work with oxd.  

## Benefits
oxd offers operational and security benefits for developers and organizations:

- oxd centralizes and standardizes secure OAuth 2.0 implementations across heterogeneous web application environments    
- When OAuth 2.0 vulnerabilities are discovered, simply update the oxd package--applications **never** have to be changed or regression tested    
- oxd is written, maintained and supported by OAuth 2.0 security experts   


## Compatibility
oxd 4.0.beta has been tested against the following OAuth 2.0 servers:

### OpenID Providers (OP)
- Gluu Server [3.1.4](https://gluu.org/docs/ce/3.1.4)


### UMA Authorization Servers (AS)
- Gluu Server [3.1.4](https://gluu.org/docs/ce/3.1.4)

!!! Note
    If you have successfully tested oxd against another OP or AS, please email details to [sales@gluu.org](mailto:sales@gluu.org).
    
## API
oxd implements the [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) and [UMA 2.0](https://docs.kantarainitiative.org/uma/wg/oauth-uma-grant-2.0-05.html) profiles of OAuth 2.0. 

- The [oxd OpenID Connect APIs](./api/index.md#openid-connect-authentication) can be used to send a user to an OpenID Connect Provider (OP) for authentication and to gather identity information ("claims") about the user. 

- The [oxd UMA APIs](./api/index.md#uma-2-authorization) can be used to send a user to an UMA Authorization Server (AS) for access management policy enforcement, for example to centrally manage which people (or software clients) can access which web pages and APIs.   

Learn more in the [oxd API section](./api/index.md) of the documentation.  

## Native Libraries

oxd has defined swagger specification [here](https://github.com/GluuFederation/oxd/blob/version_4.0.beta/oxd-server/src/main/resources/swagger.yaml). It is possible to generated native library in your favorite language by [Swagger Code Generator](https://swagger.io/tools/swagger-codegen/)
Check our FAQ about easiest way to generate native client [here](https://gluu.org/docs/oxd/4.0.beta/faq/#what-is-the-easiest-way-to-generate-native-library-for-oxd).

## Source code
The oxd source code is [available on GitHub](https://github.com/GluuFederation/oxd). 

## License
oxd 4.0.beta is available under the AGPL open source license. 

## Support
Gluu offers support for oxd on the [Gluu Support Portal](https://support.gluu.org). In fact, we use oxd and a Gluu Server to provide single sign-on across our oxd portal and support app! 

For guaranteed response times, private support, and more, Gluu offers [VIP support](https://gluu.org/pricing). 
