# oxd-python

## Overview
The following documentation demonstrates 
how to use the [oxd client software](http://oxd.gluu.org) Python library to 
send users from a Python application to an OpenID Connect Provider (OP), 
like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 


## Sample Project

[Download a sample project](https://github.com/GluuFederation/oxd-python/archive/3.0.1.zip) specific to this oxd library.

### System Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows 7 or higher / Windows Server 2008 or higher
- Python 2.7
- Flask
- oxdpython 3.0.1
- Flask-SSLify


## Prerequisites

### Required Software
To use the oxd Python library, you will need:

- A valid OpenID Connect Provider (OP), like Google or the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/).    
- An active installation of the [oxd server](../../install/index.md) running in the same server as the client application.      
- A Windows server or Windows installed machine / Linux server or Linux installed machine.


### Install oxdpython via pip

To install oxdpython via pip, run following commands in Linux terminal or 
Windows command window

``` {.code }
pip install oxdpython
```

To install Flask-SSLify via pip, run following commands in Linux terminal or 
Windows command window 

``` {.code }
pip install Flask-SSLify
```



### Configure the Client Application

- This oxd python library uses a configuration file (demosite.cfg) to specify 
information needed by OpenID Connect dynamic client registration, and to save 
information that is returned, like the oxd_id. So the config file needs to be 
writable by the Client application.


	The minimal configuration required to get oxd-python working:

```
    [oxd]
    host = localhost
    port = 8099
    id = <oxd_id>
    
    [client]
    op_host = <https://ophost_url>
    authorization_redirect_uri = https://client.example.com/CodeState
    post_logout_redirect_uri = https://client.example.com
    client_logout_uris = https://client.example.com/logout
    scope = openid,profile,email
```
  
Upon successful registration of the client application oxd_id is set to the 
field 'id' in demosite.cfg.

!!!Note: 
    The [sample.cfg](https://github.com/GluuFederation/oxd-python/blob/3.0.1/sample.cfg)
    file contains detailed documentation about the configuration values.

- Your client application must have a valid ssl cert, so the url includes: `https://`    

- Enable SSL by	setting the valid certificate and key in your application startup file:

```{.code}
from flask_sslify import SSLify

app = Flask(__name__)
sslify = SSLify(app)

app.run('127.0.0.1', debug=True, port=8080, ssl_context=('<path>/demosite.crt', '<path>/demosite.key'))
```
    
- The client host name should be a valid `hostname`(FQDN), not localhost or an IP Address. 
You can configure the host name by adding the following entry in your host file.

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
        
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`



## oxd Server Methods

The oxd server provides the following six methods for authenticating users with 
an OpenID Connect Provider (OP):

- [Register Site](../protocol/#register-site)    
- [Update Site Registration](../protocol/#update-site-registration)    
- [Get Authorization URL](../protocol/#get-authorization-url)   
- [Get Tokens by Code](../protocol/#get-tokens-id-access-by-code)    
- [Get User Info](../protocol/#get-user-info)   
- [Get Logout URI](../protocol/#log-out-uri) 

## Sample Code

### Register Site

In order to use an OpenID Connect Provider (OP) for login, 
you need to register your client application at the OP. 
During registration oxd will dynamically register the OpenID Connect 
client and save its configuration. Upon successful registration a unique 
identifier will be issued by the oxd server. If your OpenID Connect Provider 
does not support dynamic registration (like Google), you will need to obtain a 
ClientID and Client Secret which can be passed to the `register_site` method as a 
parameter. The Register Site method is a one time task to configure a client in the 
oxd server and OP.

```python
from oxdpython import Client

config = "/var/www/demosite/demosite.cfg"  # This should be writable by the server
client = Client(config)
```

**Request:**

```python
oxd_id = client.register_site()
```

**Response:**

```python
oxd_id = 6F9619FF-8B86-D011-B42D-00CF4FC964FF
```

### Update Site Registration

The `update_site_registration` method can be used to update an existing client in the OP. 
Fields like Authorization redirect url, post logout url, scope, client secret and other fields, 
can be updated using this method.

**Required parameters:**

- logoutRedirecturl: URL to which the RP is requesting that the 
End-User's User Agent be redirected after a logout has been performed.

**Request:**

```python
contacts = request.form.get('updateEmail')
logoutRedirecturl = request.form.get('postLogoutRedirectUrl')

status = client.update_site_registration(contacts, logoutRedirecturl)
```
**Response:**
```python
status = ok
```

### Get Authorization URL

The `get_authorization_url` method returns the OpenID Connect Provider 
authentication URL to which the client application must redirect the user to 
authorize the release of personal data. The response URL includes state value, 
which can be used to obtain tokens required for authentication. This state value used 
to maintain state between the request and the callback.

**Request:**

```python
auth_url = client.get_authorization_url()
```

**Response:**
```python
authorization_url = https://client.example.com/authorize?response_type=code&client_id=s6BhdRkqt3&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb&scope=openid%20profile&acr_values=duo&state=af0ifjsldkj&nonce=n-0S6_WzA2Mj
```

### Get Tokens by Code

Upon successful login, the login result will return code and state. `get_tokens_by_code` 
uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**

- code: The Code from OP authorization redirect url
- state: The State from OP authorization redirect url

**Request:**

```python
tokens = client.get_tokens_by_code(code, state)

accessToken = tokens.access_token
refreshToken = tokens.refresh_token
```

**Response:**
```python
accessToken = SlAV32hkKG
refreshToken = aaAV32hkKG1
```

### Get User Info

Once the user has been authenticated by the OpenID Connect Provider, 
the `get_user_info` method returns Claims (Like First Name, Last Name, emailId, etc.) 
about the authenticated end user.

**Required parameters:**

- accessToken: accessToken from GetTokenByCode

**Request:**

```python
user = client.get_user_info(accessToken)

userName = user.name[0]
userEmail = user.email[0]
```

**Response:**
```python
user = {given_name=["Jane"], family_name=["Doe"], email=["janedoe@example.com"], sub=["248289761001"], name=["Jane Doe"] }
```

### Logout

`get_logout_uri` method returns the OpenID Connect Provider logout url. 
Client application  uses this logout url to end the user session.

**Request:**

```python
logout_url = client.get_logout_uri()
session.clear()
return redirect(logout_url)
```

**Response:**
```python
logout_url = https://<server>/end_session?id_token_hint=<id token>&state=<state>&post_logout_redirect_uri=<...>
```


## Support
Please report technical issues and suspected bugs on our [support page](https://support.gluu.org/). You can use the same credentials you created to register for your oxd license to sign in on Gluu support.