# oxd 3.0.1 Documentation

To make securing server-side web applications with a standard OpenID Connect Provider (OP) like the [Gluu Server](https://gluu.org/gluu-server) fast, easy, and secure, Gluu offers OAuth 2.0 client software called [oxd](http://oxd.gluu.org). 

oxd is commercial software licensed by Gluu. You can get a license and a $50 credit by registering for an account on the [oxd website](https://oxd.gluu.org).

[Watch the oxd demo video](https://youtu.be/zZMf84wB2f0). 

## Overview

The oxd server is designed to work as a standalone service demon. oxd is a web server, running in an embedded [Jetty](http://www.eclipse.org/jetty/) server. Start it and stop it like you would any other unix service.

By default, oxd is restricted to `localhost`, which means these APIs cannot be reached from another server on the network--only by services running locally on the server. oxd should be deployed on each server that has web applications.

oxd APIs can be called by any application that can make REST calls. Gluu also provides several native libraries that wrap the
oxd APIs, currently available for Php, Java, Python, Node, Ruby C#, and .Net.

[Watch the oxd demo video](https://youtu.be/zZMf84wB2f0). 

## Technical Architecture
oxd is middleware service that simplifies the process of properly securing applications with OpenID Connect and UMA, two standard profiles of OAuth 2.0 for federated authentication and authorization, respectively. 

By default, oxd is restricted to `localhost`, which means these APIs cannot be reached from another server on the network--only by services running locally on the server. oxd should be deployed on each server that has web applications. 

![oxd-technical-architecture](https://cloud.githubusercontent.com/assets/5271048/22804205/919112e8-eedd-11e6-85a7-60eab8f51585.png)

## Protocol 
The oxd server supports the OpenID Connect and UMA profiles of OAuth 2.0. OpenID Connect can be used to send a user for authentication and gather identity information about the user. UMA can be used to manage what digital resources the user should have access to.

Learn more in the [protocol section](./protocol/index.md/)

## Why OXD
If your goal is to integrate applications with your Gluu Server to achieve single sign-on (SSO), there are many strategies available to you. However, you should consider using oxd because:

1. oxd is super-easy to use;  

2. oxd provides a central place to manage security across heterogenous application environments. If you have applications written in many languages, e.g. php, python, ruby, java, etc., you **do not** want to use a different open source client library for each application. That makes managing security a nightmare!;

3. oxd is constantly being updated to address the latest OAuth 2.0 security knowledge;      

4. If you're also using the Gluu Server, Gluu can provide more complete end-to-end support if we know both the client and server software;      

5. In addition to a simple JSON/REST API, there are oxd libraries for Php, Python, Java, Node, Ruby, C#, .Net, Perl and Go; 

6. There are oxd plugins for many popular applications like: Wordpress, Drupal, Magento, OpenCart, SugarCRM, SuiteCRM, Roundcube, Shopify, and Kong. More are being added too. Next on the list are: MatterMost, RocketChat, NextCloud, and Liferay.      

To learn more about oxd review these docs and the [code on Github](https://github.com/GluuFederation/oxd). 

When you're ready to deploy oxd, head over to the website to [get your oxd license](https://oxd.gluu.org). 

## Installation

Follow [these instructions](./install/index.md) to install and start oxd.

## Configuration

Follow [these instructions](./conf/index.md) to configure oxd.

!!! Note
    You will need a valid license to properly configure the oxd server. If you have not yet registered for a license, visit the [oxd website](https://oxd.gluu.org). 

## Libraries
oxd client libraries provide simple, flexible, powerful access to the oxd OpenID Connect and UMA authentication and authorization APIs.     
- [Python](./libraries/python/index.md)       
- [Java](./libraries/java/index.md)       
- [Php](./libraries/php/index.md)       
- [Node](./libraries/node/index.md)       
- [Ruby](./libraries/ruby/index.md)       
- [C#](./libraries/csharp/index.md)       
- [Perl](./libraries/perl/index.md)       

## Plugins

Gluu currently publishes oxd plugins, modules, and extensions for the following open source applications (more coming!):      
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

## License
oxd is commercial software licensed by Gluu. Learn more on the [oxd website](https://oxd.gluu.org).

## FAQ's

**What is oxd?**       
oxd is a mediator: it provides API's that can be called by a web application that are easier than directly calling the API's of an OpenID Connect Provider (OP) or an UMA Authorization Server (AS).

**Where do I deploy oxd?**    
oxd is deployed on the same server as the web application(s) you want to protect.

**Why should I use oxd?**     
oxd offers a few key improvements over the traditional model of embedding OAuth 2.0 code in your applications:

1. If new vulnerabilities are discovered in OAuth2/OpenID Connect, oxd is the only component that needs to be updated. The oxd APIs remain the same, so you don't have to change and regression test your applications;     

2. oxd is written, maintained, and supported by developers who specialize in application security. Because of the complexity of the standards--and the liability associated with poor implementations--it makes sense to rely on professionals who have read the specifications in their entirety and understand how to properly implement the protocols;     

3. Centralization reduces costs. By using oxd across your IT infrastructure for application security (as opposed to a handful of homegrown and third party OAuth2 client implementations), the surface area for vulnerabilities, issue resolution, and support is significantly reduced. Plus you who have someone to call when something goes wrong!     

**How is oxd licensed?**        
oxd is commercially licensed. Each time you install oxd you will need to use your license. Active installations are billed $0.33 per day (roughly $10 USD per month per active installation). [Get your oxd license today](http://oxd.gluu.org).  

**Which programming languages and frameworks does oxd have libraries for?**        
Currently there are oxd libraries for the following languages and frameworks:    

- Php   
- Java    
- Python    
- Ruby    
- C#     
- Node.js    
- Spring    
- Lua     

**How do I get SSO across several websites?**                
You’ll need two things:     

1. A central OpenID Connect Provider that holds the passwords and user information;     

2. Websites that use the OpenID Connect protocol to authenticate users.     

An easy way to accomplish the first--utilize [Google as your OP](https://oxd.gluu.org/docs/conf/#using-google-as-an-openid-provider), or install and configure the [free open source Gluu Server](http://gluu.org/docs/deployment) using the Linux packages for CentOS, Ubuntu, Debian or Red Hat. 

The second is accomplished by [installing the oxd service](https://oxd.gluu.org/docs/oxdserver/install/) on each web server that needs SSO. This provides easy to use local API’s that can be called by your web applications, and enables you to use a number of plugins for popular open source software packages.

**Can I use oxd plugins for social login?**    
Since oxd simply makes it easy to send users to an OpenID Connect Provider (OP) for login, social login needs to be implemented at the OP. If you are using the Gluu Server, you can use [Passport.js](https://gluu.org/docs/ce/authn-guide/passport/) to configure and offer  social login to your users. 

**Can I use oxd for two-factor authentication (2FA)?**    
Again, since oxd simply makes it easy to send users to an OpenID Connect Provider (OP) for login, two-factor authentication needs to be enforced at the OP. If your OP supports two-factor authentication, your application can request it by specifying an `acr_value` in the oxd configuration file. To see which types of authentication mechanisms your OP supports navigate to `https://hostname/.well-known/openid-configuration` and look for `acr_values`. For instance, you can see that Google does not allow a client to request a specific type of authentication: [Google OpenID Configuration](https://accounts.google.com/.well-known/openid-configuration). The Gluu Server ships with several built in two-factor authentication mechanisms. Two that are very easy to use are FIDO U2F tokens (like Yubikey) and Duo Security. Check out the default [acr values for supported authentication mechanisms](https://gluu.org/docs/ce/3.0.1/admin-guide/openid-connect/#multi-factor-authentication-for-clients) in the Gluu Server.

**Can I use Google or Microsoft Azure Active Directory as my OpenID Connect Provider?**    
Probably, but Google and Microsoft do not support dynamic client registration. If you are successful with this, please let us know! It should work.

**Can I purchase support for the Gluu Server or oxd?**    
Yes, for information on paid support, [visit our website](http://gluu.org/pricing).

