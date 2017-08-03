# oxd-ruby

## Overview

The following documentation demonstrates how to use the [oxd client software](http://oxd.gluu.org) Ruby library to send users from a Ruby application to an OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google, for login. 

## Sample Project

[Download a sample project](https://github.com/GluuFederation/oxd-ruby/archive/master.zip) specific to this oxd library.


### System Requirements

- Ubuntu / Debian / CentOS / RHEL / Windows 7 or higher / Windows Server 2008 or higher
- Ruby 2.2
- Rails
- Passenger
- Apache 2.4.4 or higher
- oxd-ruby 3.0.1

## Prerequisites

### Required Software
To use the oxd Ruby library, you will need:

- A valid OpenID Connect Provider (OP), like Google or the [Gluu Server](https://gluu.org/docs/ce/installation-guide/install/).    
- An active installation of the [oxd server](../../install/index.md) running in the same server as the client application.     
- A Windows server or Windows installed machine / Linux server or Linux installed machine meeting system requirements.

### Install oxd-ruby from RubyGems

The Ruby Client is installed using RubyGems. Include following line in the Gemfile of the application using Oxd Ruby Library.

```
gem 'oxd-ruby', '~> 0.1.6'
```

Run the bundle command after to install the `oxd-ruby` plugin.

```
$ bundle install
```


### Configure the Client Application

- The configurations must be generated first using the following command.
  ```
  $ rails generate oxd:config
  ```

  This command will install the `oxd_config.rb` initializer file in the `config/initializers` directory which contains all the global configuration options for the ruby plugin. The following configurations must be set before the plugin can be used.

  1. config.oxd_host_ip
  2. config.oxd_host_port
  3. config.op_host 
  4. config.authorization_redirect_uri

- Your client application must have a valid ssl cert, so the url includes: `https://`    
- The client host name should be a valid `hostname`(FQDN), not localhost or an IP Address. You can configure the host name by adding the following entry in your host file.

    **Linux**

    Host file location `/etc/host` :

    `127.0.0.1  client.example.com`  
    
    **Windows**

    Host file location `C:\Windows\System32\drivers\etc\host` :

    `127.0.0.1  client.example.com`
    
- Enable SSL by	adding the following lines on virtual host file of Apache in the location:

	**Linux**
    
    `/etc/apache2/sites-available/000-default.conf`
    
    **Windows**
    
    `C:/apache/conf/extra/httpd-vhosts.conf`

    ```
    <VirtualHost *>
      ServerName client.example.com
      ServerAlias client.example.com
      DocumentRoot "<apache web root directory>"
    </VirtualHost>

    <VirtualHost *:443>
      DocumentRoot "<apache web root directory>"
      ServerName client.example.com
      SSLEngine on
      SSLCertificateFile "<Path to your ssl certificate file>"
      SSLCertificateKeyFile "<Path to your ssl certificate key file>"
      <Directory "<apache web root directory>">
        AllowOverride All
        Order allow,deny
        Allow from all
      </Directory>
    </VirtualHost>
    ```



## oxd Server Methods

The oxd server provides the following six methods for authenticating users with an OpenID Connect Provider (OP):

- [Register Site](../../protocol/#register-site)    
- [Update Site Registration](../../protocol/#update-site-registration)    
- [Get Authorization URL](../../protocol/#get-authorization-url)   
- [Get Tokens by Code](../../protocol/#get-tokens-id-access-by-code)    
- [Get User Info](../../protocol/#get-user-info)   
- [Get Logout URI](../../protocol/#log-out-uri) 


## Sample code

- [API Docs](http://www.rubydoc.info/gems/oxd-ruby)

The plugin requires editing the `application_controller.rb` file to include the following snippet.

```
require 'oxd-ruby'

before_filter :set_oxd_commands_instance
protected
    def set_oxd_commands_instance
        @oxd_command = Oxd::ClientOxdCommands.new
    end
```


### Register Site

In order to use an OpenID Connect Provider (OP) for login, you need to register your client application at the OP. During registration oxd will dynamically register the OpenID Connect client and save its configuration. Upon successful registration a unique identifier will be issued by the oxd server. If your OpenID Connect Provider does not support dynamic registration (like Google), you will need to obtain a ClientID and Client Secret which can be passed to the `register_site` method as a parameter. The Register Site method is a one time task to configure a client in the oxd server and OP.

**Request:**

```ruby
def register_site			
	@oxd_command.register_site 
end
```

### Update Site Registration

The `update_site_registration` method can be used to update an existing client in the OP. Fields like Authorization redirect url, post logout url, scope, client secret and other fields, can be updated using this method.

**Required parameters:**

- oxdId: oxd Id from client registration
- postLogoutUrl: A URL which the OP is authorized to redirect the user after logout.

**Request:**
```ruby
def update_site_registration			
	status = @oxd_command.update_site_registration(@oxdId, @postLogoutUrl)
end
```

**Response:**

```javascript
{
    "status":"ok"
}
```

### Get Authorization URL

The `get_authorization_url` method returns the OpenID Connect Provider authentication URL to which the client application must redirect the user to authorize the release of personal data. The response URL includes state value, which can be used to obtain tokens required for authentication. This state value used to maintain state between the request and the callback.

**Request:**

```ruby
def get_authorization_url
	authorization_url = @oxd_command.get_authorization_url
	redirect_to authorization_url
end
```

**Response:**
```javascript
{
    "status":"ok",
    "data":{
        "authorization_url":"https://client.example.com/authorize?response_type=code&client_id=s6BhdRkqt3&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb&scope=openid%20profile&acr_values=duo&state=af0ifjsldkj&nonce=n-0S6_WzA2Mj"
    }
}
```

### Get Tokens by Code

Upon successful login, the login result will return code and state. `get_tokens_by_code` uses code and state to retrieve token which can be used to access user claims.

**Required parameters:**

- code: The Code from OP authorization redirect url

**Request:**

```ruby
def get_tokens_by_code
	if (params[:code].present?)
		@access_token = @oxd_command.get_tokens_by_code( params[:code] ) 
	end 
end
```

**Response:**

```javascript
{
    "status":"ok",
    "data":{
        "access_token":"SlAV32hkKG",
        "expires_in":3600,
        "refresh_token":"aaAV32hkKG1"
        "id_token":"eyJ0 ... NiJ9.eyJ1c ... I6IjIifX0.DeWt4Qu ... ZXso",
        "id_token_claims": {
             "iss": "https://client.example.com",
             "sub": "24400320",
             "aud": "s6BhdRkqt3",
             "nonce": "n-0S6_WzA2Mj",
             "exp": 1311281970,
             "iat": 1311280970,
             "at_hash": "MTIzNDU2Nzg5MDEyMzQ1Ng"
        }
    }
}
```

### Get User Info

Once the user has been authenticated by the OpenID Connect Provider, the `get_user_info` method returns Claims (Like First Name, Last Name, emailId, etc.) about the authenticated end user.

**Required parameters:**

- access_token: access_token from get_tokens_by_code

**Request:**

```ruby
def get_user_info
	@user = @oxd_command.get_user_info(@access_token) 	
end
```

**Response:**
```javascript
{
    "status":"ok",
    "data":{
        "claims":{
            "sub": ["248289761001"],
            "name": ["Jane Doe"],
            "given_name": ["Jane"],
            "family_name": ["Doe"],
            "preferred_username": ["j.doe"],
            "email": ["janedoe@example.com"],
            "picture": ["http://example.com/janedoe/me.jpg"]
        }
    }
}
```

### Logout

`get_logout_uri` method returns the OpenID Connect Provider logout url. Client application  uses this logout url to end the user session.

**Required parameters:**

- access_token: access_token from get_tokens_by_code
- state: The State from OP authorization redirect url
- session_state: The session_state from OP authorization redirect url

**Request:**

```ruby
def logout
	if(@access_token)
		@logout_url = @oxd_command.get_logout_uri(@access_token, @state, @session_state)
		redirect_to @logout_url
	end	    
end
```

**Response:**

```javascript
{
    "status":"ok",
    "data":{
        "uri":"https://<server>/end_session?id_token_hint=<id token>&state=<state>&post_logout_redirect_uri=<...>"
    }
}
```

## Support

Please report technical issues and suspected bugs on 
our [support page](https://support.gluu.org/). You can use the same credentials 
you created to register for your oxd license to sign in on Gluu support.
