# OpenID Connect Single Sign-On (SSO) WordPress Plugin By Gluu

![image](https://raw.githubusercontent.com/GluuFederation/wordpress-oxd-plugin/master/wordpress.png)

Gluu's OpenID Connect Single Sign-On (SSO) WordPress Plugin will enable you to 
authenticate users against any standard OpenID Connect Provider (OP). 
If you don't already have an OP you can use Google or 
[deploy the free open source Gluu Server](https://gluu.org/docs/ce/3.0.1/installation-guide/install/).  

## Requirements
In order to use the WordPress plugin you will need a standard OP (like Google or a Gluu Server) and the oxd server.

* Compatibility : 2.9 <= 4.7.2 versions

* [Gluu Server Installation Guide](https://gluu.org/docs/ce/3.0.1/installation-guide/install/).

* [oxd Webpage](https://oxd.gluu.org)


## Installation
 
### Download
[Github source](https://github.com/GluuFederation/wordpress-oxd-plugin/archive/v2.4.4.zip).

[Link to WordPress marketplace](https://wordpress.org/plugins/openid-connect-sso-by-gluu/)

### Upload
Open your WordPress site plugin page, e.g. `https://{site-base-url}/wp-admin/plugin-install.php?tab=upload`, upload the plugin and click on the install now button.

### Activate 

Activate the plugin by performing the following steps:
 
1. Go to `https://{site-base-url}/wp-admin/plugins.php`
2. Find OpenID Connect Single Sign-On (SSO) Plugin By Gluu and click the activate button.

## Configuration

### Gluu Server Configuration 

Before using this plugin with GLUU open id provider make sure you have configured gluu to return email claim.

To enable email claim in the gluu server do the following:

1.First navigate to `OpenID Connect` > `Scopes` in the `Display Name` column click the `Email` link and then set the default scope to `True` from the drop down menu and make sure to add email claim in the `claims menu`(see following images for better reference).

![image](../img/plugin/emailScope.PNG)

![image](../img/plugin/emailScopeInner.PNG)

2.Then navigate to `Configuration` > `Attributes` and make sure that the `Email` row is set to `Active` in the scopes.

![image](../img/plugin/emailInAttribute.PNG)

### General 

In your WP(WordPress) admin menu panel you should now see the OpenID Connect menu tab. Click the link to navigate to the General configuration  page:

![General](../img/plugin/1.png) 

1. Automatically register any user with an account in the OpenID Provider: By setting registration to automatic, any user with an account in the OP will be able to register for an account in your WordPress site. They will be assigned the new user default role specified below.
2. Only register and allow ongoing access to users with one or more of the following roles in the OP: Using this option you can limit registration to users who have a specified role in the OP, for instance `wordpress`. This is not configurable in all OP's. It is configurable if you are using a Gluu Server. [Follow the instructions below](#role-based-enrollment) to limit access based on an OP role. 
3. New User Default Role: specify which role to give to new users upon registration.  
4. URI of the OpenID Provider: insert the URI of the OpenID Connect Provider.
5. Custom URI after logout: custom URI after logout (for example "Thank you" page).
6. oxd port: enter the oxd-server port (you can find this in the `oxd-server/conf/oxd-conf.json` file).
7. Click `Register` to continue.

If your OpenID Provider supports dynamic registration, no additional steps are required in the general tab and you can navigate to the [OpenID Connect Configuration](#openid-connect-configuration) tab. 

If your OpenID Connect Provider doesn't support dynamic registration, you will need to insert your OpenID Provider `client_id` and `client_secret` on the following page.

![General](../img/plugin/2.png) 

To generate your `client_id` and `client_secret` use the redirect uri: `https://{site-base-url}/index.php?option=oxdOpenId`.

!!!Note:
    If you are using a Gluu server as your OpenID Provider, you can make sure everything is configured properly by logging into to your     Gluu Server, navigate to the `OpenID Connect` > `Clients` page. Search for your `oxd id`.

#### Enrollment and Access Management

1. Navigate to your Gluu Server admin GUI. 
2. Click the `Users` tab in the left hand navigation menu. 
3. Select `Manage People`. 
4. Find the person(s) who should have access. 
5. Click their user entry. 
6. Add the `User Permission` attribute to the person and specify the same value as in the plugin. For instance, if in the plugin you have limit enrollment to user(s) with role = `wordpress`, then you should also have `User Permission` = `wordpress` in the user entry. [See a screenshot example](https://cloud.githubusercontent.com/assets/5271048/19735932/2c3817c4-9b73-11e6-9d59-ace7ecdfed41.png).
7. Update the user record. 
8. Go back to the WP plugin and make sure the `permission` scope is requested (see below). 
9. Now they are ready for enrollment at your WordPress site. 

### OpenID Connect Configuration

![OpenID Connect Configuration](../img/plugin/5.png)

#### User Scopes

Scopes are groups of user attributes that are sent from the OP to the application during login and enrollment. By default, the requested scopes are `profile`, `email`, and `openid`.  

To view your OP's available scopes, open a web browser and navigate to `https://OpenID-Provider/.well-known/openid-configuration`. For example, here are the scopes you can request if you're using [Google as your OP](https://accounts.google.com/.well-known/openid-configuration). 

If you are using a Gluu server as your OpenID Provider, 
you can view all available scopes by navigating to the Scopes interface in Gluu CE Server Admin UI 

`OpenID Connect` > `Scopes` 

In the Plugin interface you can enable, disable and delete scopes. 

#### Authentication

Bypass the local WordPress login page and send users straight to the OP for authentication: 
Check this box so that when users attempt to login they are sent straight to the OP,
bypassing the local WP login screen. When it is not checked, users will see the following 
screen when trying to login: 

![upload](../img/plugin/6.png) 

**Select ACR**: To signal which type of authentication should be used, 
an OpenID Connect client may request a specific authentication context class reference value (a.k.a. "acr"). 
The authentication options available will depend on which types of mechanisms the OP has been 
configured to support. The Gluu Server supports the following authentication mechanisms 
out-of-the-box: username/password (basic), Duo Security, Super Gluu, and U2F tokens, like Yubikey.  

Navigate to your OpenID Provider configuration webpage `https://OpenID-Provider/.well-known/openid-configuration` to see supported `acr_values`. 

In the `Select acr` section of the plugin page, choose the mechanism which you 
want for authentication. If the `Select acr` value in the plugin is `none`, users will be 
sent to pass the OP's default authentication mechanism.



#### Support
If you are having any technical issue on using Gluu's OpenID Connect Single Sign-On (SSO) 
WordPress Plugin you can check our support page or raise support ticket at [https://support.gluu.org](https://support.gluu.org)
