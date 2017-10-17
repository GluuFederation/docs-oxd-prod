# oxd 3.1.1 Documentation

## Introduction
oxd is middleware software that makes it easy to send users from **server-side web applications** to a standard OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server), for login and/or enrollment.

In addition to OpenID Connect, oxd implements the User Managed Access ("UMA") 2.0 profile of OAuth 2.0. UMA 2.0 enables claims-based authorization and multi-step consent, user-interactions and stepped-up authentication flows. 

oxd exposes a simple REST API and native libraries that wrap the API for Php, Java, Python, Node, Ruby, Perl, C#, and Go. 

!!! Note
    If you need to integrate other types of apps with your Gluu Server, like single-page apps (SPAs) or native apps, review the [SSO integration guide](https://gluu.org/docs/ce/integration/) in the Gluu Server documentation.

### oxd-server
The oxd-server is designed to work as a standalone service daemon via sockets. By default, applications must connect to the oxd-server via `localhost`.

### oxd-https-extension
If you want to connect applications to your oxd server over the web via HTTPS, you can use the oxd-https-extension. The extension is included in the standard oxd package (it simply needs to be started), and will enable you to host one central oxd service that can be leveraged by all your web applications. 

## Get Started

Step 1: [Install the oxd package](./install/index.md);   

Step 2*: [Configure your oxd-server](./configuration/index.md);           

Step 3: If you want to support application connections via HTTPS, [start the oxd-https-extension](./oxd-https/start/index.md);      

Step 4: Use the oxd API or one of the native libraries to securely send users from your apps to your OP for enrollment and/or login.  

*Note: you will need a valid license to configure oxd. Get your license and a $50 credit on the [oxd website](https://oxd.gluu.org).
   
## Supported OpenID Providers (OP)
oxd has been confirmed to work with the following standard OP's:

- [Gluu Server](https://gluu.org/docs/ce/installation-guide/)    
- [Google](https://developers.google.com/identity/protocols/OpenIDConnect)   

If you have successfully tested oxd against another OP, or for other OP related requests, please email us at [sales@gluu.org](mailto:sales@gluu.org).  

## API
The oxd-server implements the [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) and [UMA 2.0](https://docs.kantarainitiative.org/uma/wg/oauth-uma-grant-2.0-05.html) profiles of OAuth 2.0. 

OpenID Connect can be used to send a user for authentication and gather identity information about the user. UMA can be used to manage what digital resources, like web pages and APIs, the user (or client) can access.    

Learn more in the [oxd API section](./api/index.md) of the documentation. 

## Libraries
oxd client libraries provide simple and flexible access to the oxd OpenID Connect and UMA authentication and authorization APIs.   

- [Python](./libraries/python/index.md)       
- [Java](./libraries/java/index.md)       
- [Php](./libraries/php/index.md)       
- [Node](./libraries/node/index.md)       
- [Ruby](./libraries/ruby/index.md)       
- [C#](./libraries/csharp/index.md)       
- [Perl](./libraries/perl/index.md)  
- [Go](./libraries/go/index.md)  

## Plugins

Gluu currently publishes oxd plugins, modules, and extensions for the following open source applications:    

- [Wordpress](./plugin/wordpress/index.md)      
- [Magento](./plugin/magento/index.md)       
- [Drupal](./plugin/drupal/index.md)       
- [OpenCart](./plugin/opencart/index.md)       
- [SuiteCRM](./plugin/suitecrm/index.md)       
- [SugarCRM](./plugin/sugarcrm/index.md)       
- [Roundcube](./plugin/roundcube/index.md)  
- [NextCloud](./plugin/nextcloud/index.md) 

Gluu does its best to keep these plugins up to date but does not guarantee specific functionality. If you find a bug, or would like feature enhancements, we would be happy to discuss allocating a resource to work on the plugin on a time and materials basis. [Schedule a call](https://gluu.org/booking) with us to discuss the project scope and funding.

## Support
Gluu offers free community support for oxd on the [Gluu Support Portal](https://support.gluu.org). You can login to the support site using the same credentials that you use to access the oxd license management app (and vice versa). In fact, we use oxd and a Gluu Server to provide single sign-on across our oxd portal and support app! 

If your organization needs guaranteed response times, private support, and priority access to our support and development team, Gluu offers a range of [VIP support plans](https://gluu.org/pricing). You can [schedule a meeting](https://gluu.org/booking) with us to discuss and move forward with purchasing a support contract. 

## FAQ's

**What is oxd?**       
Under oxd we mean oxd-server and oxd-https-extension. oxd is a mediator: it provides API's that can be called by a web application that are easier than directly calling the API's of an OpenID Connect Provider (OP) or an UMA Authorization Server (AS).

**What is oxd-server?**       
oxd-server is a standalon service with socket connection. By default it's restricted to localhost only (by `localhost_only: true` configuration in `oxd-conf.json`). It's possible to turn off this restriction if set `localhost_only: false` in `oxd-conf.json`

**What is oxd-https-extension?**
oxd-https-extension is a standalone RESTful Jetty based server which accepts HTTP calls and redirects them to `oxd-server`. In order to use `oxd-https-extension` oxd-server must be installed. 

**Where do I deploy oxd-server?**    
oxd-server is deployed on the same server as the web application(s) you want to protect. With the oxd-https-extension, oxd-server can be deployed on its own standalone server.

**Why should I use oxd?**     
oxd offers a few key improvements over the traditional model of embedding OAuth 2.0 code in your applications:

1. If new vulnerabilities are discovered in OAuth2/OpenID Connect, oxd is the only component that needs to be updated. The oxd APIs remain the same, so you don't have to change and regression test your applications.     

2. oxd is written, maintained, and supported by developers who specialize in application security. Because of the complexity of the standards--and the liability associated with poor implementations--it makes sense to rely on professionals who have read the specifications in their entirety and understand how to properly implement the protocols.     

3. Centralization reduces costs. By using oxd across your IT infrastructure for application security (as opposed to a handful of homegrown and third party OAuth2 client implementations), the surface area for vulnerabilities, issue resolution, and support is significantly reduced. Plus you who have someone to call if something goes wrong!     

**How is oxd licensed?**        
oxd is commercially licensed. Each time you install oxd you will need to use your license. Active installations are billed $0.33 per day (roughly $10 USD per month per active installation). [Get your oxd license today](http://oxd.gluu.org).  

**Which programming languages and frameworks does oxd have libraries for?**        
Currently there are oxd libraries for the following languages and frameworks:    

- Python
- Java
- Php  
- Node
- Ruby    
- C#     
- Perl
- Go
- Java Spring
- Java Play
- Ruby on Rails
- Python Flask
- Node/Express
- .Net

**How do I get SSO across several websites?**                
You’ll need two things:     

1. A central OpenID Connect Provider (OP) that holds the passwords and user information  

2. Websites that use the OpenID Connect protocol to authenticate users

An easy way to accomplish the first--install and configure the [free open source Gluu Server](http://gluu.org/docs/deployment) using the Linux packages for CentOS, Ubuntu, Debian or Red Hat. You can also utilize Google as your OpenID Connect Provider (OP).

The second is accomplished by [installing the oxd service](https://gluu.org/docs/oxd/3.1.1/install/) on each web server that needs SSO. This provides easy to use local API’s that can be called by your web applications, and enables you to use a number of plugins for popular open source software packages.

**Can I use oxd plugins for social login?**    
Since oxd simply makes it easy to send users to an OpenID Connect Provider (OP) for login, social login needs to be implemented at the OP. If you are using the Gluu Server, you can use [Passport.js](https://gluu.org/docs/ce/authn-guide/passport/) to configure and offer social login to your users. 

**Can I use oxd for two-factor authentication (2FA)?**    
Again, since oxd simply makes it easy to send users to an OpenID Connect Provider (OP) for login, two-factor authentication needs to be enforced at the OP. If your OP supports two-factor authentication, your application can request it by specifying an `acr_value` in the oxd configuration file. To see which types of authentication mechanisms your OP supports navigate to `https://hostname/.well-known/openid-configuration` and look for `acr_values`. For instance, you can see that Google does not allow a client to request a specific type of authentication: [Google OpenID Configuration](https://accounts.google.com/.well-known/openid-configuration). The Gluu Server ships with several built in two-factor authentication mechanisms. Two that are very easy to use are FIDO U2F tokens (like Yubikey) and Duo Security. Check out the default [acr values for supported authentication mechanisms](https://gluu.org/docs/ce/3.0.1/admin-guide/openid-connect/#multi-factor-authentication-for-clients) in the Gluu Server.

**Can I use Google or Microsoft Azure Active Directory as my OpenID Connect Provider?**    
Probably, but Google and Microsoft do not support dynamic client registration. If you are successful with this, please let us know! It should work.

**Can I purchase support for the Gluu Server or oxd?**    
Yes, for information on paid support, [visit our website](http://gluu.org/pricing).

