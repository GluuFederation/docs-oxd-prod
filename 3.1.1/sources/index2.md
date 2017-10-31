# oxd 3.1.1 Documentation

## Introduction
oxd is client software that simplifies the process of implementing OpenID Connect and UMA in **server-side web applications**. 

Using oxd, you can securely send users from web apps to your [Gluu Server OpenID Connect Provider (OP) and UMA Authorization Server (AS)](https://gluu.org/docs/ce) for dynamic enrollment, single sign-on (SSO), and access management policy enforcement. 

!!! Note
    If you need to integrate single-page apps (SPAs), native apps, and/or SaaS apps with your Gluu Server, review the Gluu Server [SSO integration guide](https://gluu.org/docs/ce/integration/). 

## Get Started

The oxd software package includes the `oxd-server` and the `oxd-https-extension`. The oxd-server is designed to work as a standalone service daemon via sockets. By default, applications must connect to the oxd-server via `localhost`. With the `oxd-https-extension` enabled, applications can also call your `oxd-server` over the web using HTTPS. 

To get started using oxd, follow these steps (note: you can ignore steps 4 and 5 if you **do not** want to use the `oxd-https-extension`): 

Step 1: [Sign up](https://oxd.gluu.org) on the oxd website to obtain your oxd license and $50 credit

Step 2: [Install](./install/index.md) oxd on a server or VM

!!! Note: 
    if you only plan on using the `oxd-server`, oxd will need to be installed on the same server(s) as the application(s) you want to secure. If you enable the `oxd-https-extension`, oxd can be installed on any any server or VM with network access.

Step 3: [Configure](./configuration/index.md) the `oxd-server` and add your license keys           

Step 4: [Start](./install/index.md) the `oxd-server`

!!! Note: 
    If you wish to use RESTful extension of `oxd-server` called `oxd-https-extension` then please
    
Step 5: [Install](./oxd-https/start/index.md) the `oxd-https-extension` (for manual installation only, skip if you installed oxd via Linux Package)
    
Step 6: [Configure ](./oxd-https/configuration/index.md) the `oxd-https-exntesion` 

Step 7: [Start](./oxd-https/start/index.md) the `oxd-https-extension`

Step 8: Use the oxd API or one of the native libraries to securely send users from your apps to your Gluu Server for authentication and authorization. 

## API
oxd implements the [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) and [UMA 2.0](https://docs.kantarainitiative.org/uma/wg/oauth-uma-grant-2.0-05.html) profiles of OAuth 2.0. 

OpenID Connect can be used to send a user for authentication and gather identity information about the user. UMA can be used to manage which people (or software clients) can access which digital resources, like web pages and APIs.    

Learn more in the [oxd API section](./api/index.md) of the documentation. 

### Native Libraries
oxd client libraries provide simple and flexible access to the oxd OpenID Connect and UMA authentication and authorization APIs.   

**Languages**:       
- [Python](./libraries/python/index.md)       
- [Java](./libraries/java/index.md)       
- [Php](./libraries/php/index.md)       
- [Node](./libraries/node/index.md)       
- [Ruby](./libraries/ruby/index.md)       
- [C#](./libraries/csharp/index.md)       
- [Perl](./libraries/perl/index.md)  
- [Go](./libraries/go/index.md)  

**Frameworks**:           
- [Java Spring](https://github.com/GluuFederation/docs-oxd-prod/blob/3.1.1/3.1.1/sources/framework/spring/index.md)     
- [Java Play](https://github.com/GluuFederation/docs-oxd-prod/blob/3.1.1/3.1.1/sources/framework/play/index.md)     
- [Ruby on Rails](https://github.com/GluuFederation/docs-oxd-prod/blob/3.1.1/3.1.1/sources/framework/rails/index.md) 
- [Python Flask](https://github.com/GluuFederation/docs-oxd-prod/blob/3.1.1/3.1.1/sources/framework/flask/index.md)      
- [Node Express](https://github.com/GluuFederation/docs-oxd-prod/blob/3.1.1/3.1.1/sources/framework/node/index.md)     
- [.Net](https://github.com/GluuFederation/docs-oxd-prod/blob/3.1.1/3.1.1/sources/framework/net/index.md)      


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

## Supported OpenID Providers (OP)
oxd has been confirmed to work with the following standard OPs:

- [Gluu Server](https://gluu.org/docs/ce/installation-guide/)    
- [Google](https://developers.google.com/identity/protocols/OpenIDConnect)   

If you have successfully tested oxd against another OP, or for other OP related requests, please email us at [sales@gluu.org](mailto:sales@gluu.org).  

## Pricing & Billing

oxd 3.1.1 costs **USD $0.33 per application per day**. New accounts include a $50 credit that is automatically applied to usage fees incurred during the first 60 days after account creation.  

A few additional notes about pricing and billing: 

- Each time a new application leverages your oxd server, a record is added to your oxd dashboard. You will be charged USD $0.33 each day the application remains active.    

- At the end of each month usage fees are compiled and a billing summary is sent to all users associated with your account.   

- On the 7th day of each month we will attempt to bill your credit card for usage fees incurred during the previous month.

- If the transaction is declined, or there is no credit card on file, your oxd installation(s) will be deactivated and the sign-in process will stop working for applications that leverage your inactive oxd server.  

- If you can not add a credit card, or would like to discuss volume discounts, [contact us for an oxd site license](https://gluu.org/contact). 

   
## Support
Gluu offers free community support for oxd on the [Gluu Support Portal](https://support.gluu.org). You can login to the support site using the same credentials that you use to access the oxd license management app (and vice versa). In fact, we use oxd and a Gluu Server to provide single sign-on across our oxd portal and support app! 

If your organization needs guaranteed response times, private support, and priority access to our support and development team, Gluu offers a range of [VIP support plans](https://gluu.org/pricing). You can [schedule a meeting](https://gluu.org/booking) with us to discuss and move forward with purchasing a support contract.  


## FAQ's

**What is oxd?**       
oxd is a mediator: it provides API's that can be called by a web application that are easier than directly calling the API's of an OpenID Connect Provider (OP) or an UMA Authorization Server (AS).

**How is oxd licensed, and how much does it cost?**        
oxd is commercially licensed software. To start the oxd server you will need a valid license, which you can [obtain for free by registering on the oxd website](https://oxd.gluu.org). For each application that leverages your oxd service, you will be charged USD $0.33 per day. So for example, if you have 10 applications leveraging your oxd server, you will be charged USD $3.30 per day. Usage fees are accumulated daily and billed at the end of each month. If you need a site license for oxd, [schedule a call](https://gluu.org/booking).
 
**What types of applications can use oxd?**        
oxd only supports server-side web applications. If you are using the Gluu Server as your OP and need single sign-on (SSO) to single-page apps (SPAs), native apps, or SaaS apps, please review the Gluu Server [SSO integration guide](https://gluu.org/docs/ce/integration/).  

**What is the oxd-server?**       
oxd-server is a standalone service with socket connection. By default it's restricted to localhost (`localhost_only: true` configuration in `oxd-conf.json`). It's possible to turn off this restriction if you set `localhost_only: false` in `oxd-conf.json`.

**What is the oxd-https-extension?**
oxd-https-extension is a RESTful Jetty based server which accepts HTTP calls and redirects them to the `oxd-server`. If you want to connect apps to your oxd server via HTTPS, you can simply [start the oxd-https-extension](./oxd-https/start/index.md) after deploying and configuring your oxd-server. 

**Where do I deploy oxd-server?**    
By default, the oxd-server must be deployed on the same server as the web application(s) you want to protect. However, with the oxd-https-extension running, you can deploy a central, robust oxd service on dedicated server(s).

**Why should I use oxd?**     
oxd offers a few key improvements over the traditional model of embedding OAuth 2.0 code in your applications:

1. If new vulnerabilities are discovered in OAuth2/OpenID Connect, oxd is the only component that needs to be updated. The oxd APIs remain the same, so you don't have to change and regression test your applications.     

2. oxd is written, maintained, and supported by developers who specialize in application security. Because of the complexity of the standards--and the liability associated with poor implementations--it makes sense to rely on professionals who have read the specifications in their entirety and understand how to properly implement the protocols.     

3. Centralization reduces costs. By using oxd across your IT infrastructure for application security (as opposed to a handful of homegrown and third party OAuth2 client implementations), the surface area for vulnerabilities, issue resolution, and support is significantly reduced. Plus you who have someone to call if something goes wrong!     

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
- Node Express
- .Net

**How do I get SSO across several websites?**                
You’ll need two things:     

1. A central OpenID Connect Provider (OP) that holds the passwords and user information  

2. Websites that use the OpenID Connect protocol to authenticate users

An easy way to accomplish the first--install and configure the [free open source Gluu Server](http://gluu.org/docs/deployment) using the Linux packages for CentOS, Ubuntu, Debian or Red Hat. Or you can also utilize Google as your OpenID Connect Provider (OP). The second is accomplished by calling the oxd APIs in your applications to send users to the OP for login. 

**Can I use oxd plugins for social login?**    
oxd simply makes it easy to send users to an OpenID Connect Provider (OP) for login. If you want to offer users the option to use social login, that needs to be implemented at the OP. If you are using the Gluu Server as your OP, you can use [Passport.js](https://gluu.org/docs/ce/authn-guide/passport/) to configure and support social login. 

**Can I use oxd for two-factor authentication (2FA)?**    
Again, since oxd simply makes it easy to send users to an OpenID Connect Provider (OP) for login, two-factor authentication needs to be enforced at the OP. If you are using the Gluu Server as your OP, there are several built in two-factor authentication mechanisms. Learn more in the [Gluu Server authentication guide](https://gluu.org/docs/ce/3.1.1/authn-guide/intro/).

**Can I use Google or Microsoft Azure Active Directory as my OpenID Connect Provider?**    
We have tested and confirmed oxd to work with Google as an OP. Microsoft's OP implementation is not totally standard though, and therefore may require changes or updates to oxd to work. 

**Can I purchase support for the Gluu Server or oxd?**    
Yes, for information on paid support, [visit our website](http://gluu.org/pricing).

