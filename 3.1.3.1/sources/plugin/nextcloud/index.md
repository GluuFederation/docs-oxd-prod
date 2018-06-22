# OpenID Connect Single Sign-On (SSO) NextCloud APP by Gluu

![image](https://raw.githubusercontent.com/GluuFederation/nextcloud-oxd-plugin/master/nextcloud.png)

Gluu's OpenID Connect Single Sign-On (SSO) 
NextCloud APP will enable you to authenticate users against any standard 
OpenID Connect Provider (OP). If you don't already have an OP you can use 
Google or [deploy the free open source Gluu Server](https://gluu.org/docs/ce/installation-guide/install/).  

## Requirements

In order to use the NextCloud APP you will need a standard OP 
(like Google or a Gluu Server) and the oxd server.

- Compatibility : 11 version

- [Gluu Server Installation Guide](https://gluu.org/docs/ce/installation-guide/install/)

- [oxd Webpage](https://oxd.gluu.org)


## Installation
 
### Download
[Github source](https://github.com/GluuFederation/nextcloud-oxd-plugin/blob/master/gluusso.tar.gz?raw=true)

[Link to NextCloud marketplace](https://apps.nextcloud.com/apps/gluusso)

### Unzip 
Unzip the package to your NextCloud site root/apps folder.

### Activate 

Activate the app by performing the following steps:
 
1. Go to `https://{site-base-url}/index.php/settings/apps`
2. In the `Not enabled` tab, find the OpenID Connect SSO APP By Gluu and click the `Enable` button
![upload](https://raw.githubusercontent.com/GluuFederation/nextcloud-oxd-plugin/master/docu/1.png) 
3. Open menu tab OpenID Connect SSO
![upload](https://raw.githubusercontent.com/GluuFederation/nextcloud-oxd-plugin/master/docu/2.png) 

## Configuration

### General
 
In your NextCloud admin menu panel you should now see the OpenID Connect menu tab. Click the link to navigate to the General configuration page:

![General](https://raw.githubusercontent.com/GluuFederation/nextcloud-oxd-plugin/master/docu/3.png) 

1. Automatically register any user with an account in the OpenID Provider: By setting registration to automatic, any user with an account in the OP will be able to register for an account in your NextCloud site. They will be assigned the new user default role specified below
1. Only register and allow ongoing access to users with one or more of the following roles in the OP: Using this option you can limit registration to users who have a specified role in the OP, for instance `nextcloud`. This is not configurable in all OPs, but is supported by the Gluu Server. [Follow the instructions below](#role-based-enrollment) to limit access based on an OP role 
1. New User Default Group: specify which Group to assign to new users upon registration  
1. URI of the OpenID Provider: insert the URI of the OpenID Connect Provider
1. Custom URI after logout: custom URI after logout (for example, a "Thank you" page)
1. oxd port: enter the oxd-server port (you can find this in the `oxd-server/conf/oxd-conf.json` file)
1. Click `Register` to continue

If your OpenID Provider supports dynamic registration, no additional steps are required in the general tab and you can navigate to the [OpenID Connect Configuration](#openid-connect-configuration) tab. 

If your OpenID Connect Provider doesn't support dynamic registration, you will need to insert your OpenID Provider `client_id` and `client_secret` on the following page.

![General](https://raw.githubusercontent.com/GluuFederation/nextcloud-oxd-plugin/master/docu/4.png) 

To generate your `client_id` and `client_secret` use the redirect URI: `https://{site-base-url}/index.php/apps/gluusso/loginfromopenid`.

If you are using a Gluu Server as your OpenID Provider, you can make sure everything is configured properly by logging into your Gluu Server, then navigating to the OpenID Connect > Clients page. Search for your `oxd id`.

#### Enrollment and Access Management

1. Navigate to your Gluu Server admin GUI 
1. Click the `Users` tab in the left-hand navigation menu 
1. Select `Manage People` 
1. Find the person(s) who should have access 
1. Click their user entry 
1. Add the `User Permission` attribute to the person and specify the same value as in the app. For instance, if in the app you have limit enrollment to user(s) with role = `nextcloud`, then you should also have `User Permission` = `nextcloud` in the user entry. [See a screenshot example](https://cloud.githubusercontent.com/assets/5271048/19735932/2c3817c4-9b73-11e6-9d59-ace7ecdfed41.png)
1. Update the user record 
1. Go back to the NextCloud app and make sure the `permission` scope is requested (see below) 
1. Now they are ready for enrollment at your NextCloud site 

### OpenID Connect Configuration

![OpenID Connect Configuration](https://raw.githubusercontent.com/GluuFederation/nextcloud-oxd-plugin/master/docu/5.png)

#### User Scopes

Scopes are groups of user attributes that are sent from the OP to the application during login and enrollment. By default, the requested scopes are `profile`, `email`, and `openid`.  

To view your OP's available scopes, open a web browser and navigate to `https://OpenID-Provider/.well-known/openid-configuration`. For example, here are the scopes you can request if you're using [Google as your OP](https://accounts.google.com/.well-known/openid-configuration). 

If you are using a Gluu server as your OpenID Provider, you can view all available scopes by navigating to the Scopes interface in the Gluu CE Server Admin UI:

`OpenID Connect` > `Scopes` 

In the the app interface, you can enable, disable and delete scopes. 

#### Authentication

Bypass the local NextCloud login page and send users straight to the OP for authentication: Check this box so that when users attempt to log in, they are sent straight to the OP, bypassing the local NextCloud login screen. When it is not checked, users will see the following screen when trying to log in: 

![upload](https://raw.githubusercontent.com/GluuFederation/nextcloud-oxd-plugin/master/docu/6.png) 

Select ACR: To signal which type of authentication should be used, an OpenID Connect client may request a specific authentication context class reference value (a.k.a. "acr"). The authentication options available will depend on which types of mechanisms the OP has been configured to support. The Gluu Server supports the following authentication mechanisms out-of-the-box: username/password (basic), Duo Security, Super Gluu, and U2F tokens, like Yubikey.  

Navigate to your OpenID Provider configuration webpage `https://OpenID-Provider/.well-known/openid-configuration` to see supported `acr_values`. 

In the `Select acr` section of the app page, choose the mechanism which you want for authentication. If the `Select acr` value in the app is `none`, users will be sent to pass the OP's default authentication mechanism.
