# OpenID Connect Single Sign-On (SSO) Shopify App By Gluu

![image](../img/plugin.png)

Gluu's OpenID Connect Single Sign-On (SSO) Shopify App will enable you to authenticate users against any standard OpenID Connect Provider (OP). If you don't already have an OP you can use Google or [deploy the free open source Gluu Server](https://gluu.org/docs/deployment).  

## Requirements
In order to use the Shopify App you will need a standard OP (like Google or a Gluu Server) and the oxd server.

* [Gluu Server Installation Guide](https://www.gluu.org/docs/deployment/).

* [oxd Webpage](https://oxd.gluu.org)

![General](../img/apps.png) 
## Installation
 
### Download
[Github source](https://github.com/GluuFederation/wordpress-oxd-plugin/archive/v2.4.4.zip).

[Link to Shopify marketplace](https://apps.shopify.com/oxd-shopify)

### Upload
Open your Shopify App Page, e.g. `https://apps.shopify.com/oxd-shopify`, click on the Get and install in any store.

### Activate 

App Automatic Redirect To Plugin Settings:
 
1. Go to `https://{storename}/admin/settings/checkout` to enable the option to enable the login page.(Account are optional)
Then you can see login page in shopify

## Configuration
![image](../img/account.png)
### General
 
In your shopify app store on frontend login page you see the button to login with gluu server

![General](../img/login.png) 

1. Click on login button with gluu server and iframe will open and login to glu server it automatic create account on shopify same as (social app)



