## Installation

### Prerequisites

* Ruby 2.4.0
* Gluu oxd server - [Installation docs](https://gluu.org/docs/oxd/install/)
* SSL certificate for the application

### Library

To install the gem, add this line to your application's Gemfile:

```ruby
gem 'oxd-ruby', '~> 1.0.1'
```

Run bundle command to install it:

```bash
$ bundle install
```

#### Important Links

* [oxd docs](https://gluu.org/docs/oxd)
* oxd-ruby [API docs](http://www.rubydoc.info/gems/oxd-ruby/1.0.1) for the auto-generated ruby docs, which includes more in-depth information about the various functions and parameters.
* See the code of a [sample Rails app](https://github.com/GluuFederation/oxd-ruby-demo-app) built using oxd-ruby.
* Browse the oxd-ruby [source code on Github](https://github.com/GluuFederation/oxd-ruby).

## Configuration

oxd-Ruby uses a configuration file to specify information needed to configure your OpenID Connect client. After you installed oxd-Ruby, you need to run the generator command to generate the configuration file:

```bash
$ rails generate oxd:config
```

The generator will install `oxd_config.rb` initializer file in `config/initializers` directory which conatins all the global configuration options for oxd-Ruby plugin. The generated configuration file looks like this:

```ruby
Oxd.configure do |config|
  config.oxd_host_ip                       = '127.0.0.1'
  config.oxd_host_port                     = 8099
  config.op_host                           = "https://your.openid.provider.com"
  config.client_id                         = ""
  config.client_secret                     = ""
  config.client_name                       = "Gluu oxd Sample Client"
  config.op_discovery_path                 = ""
  config.authorization_redirect_uri        = "https://domain.example.com/callback"
  config.post_logout_redirect_uri          = "https://domain.example.com/logout"
  config.claims_redirect_uri               = ["https://domain.example.com/claims"]
  config.scope                             = ["openid","profile", "email", "uma_protection","uma_authorization"]
  config.grant_types                       = ["authorization_code","client_credenitals"]
  config.application_type                  = "web"
  config.response_types                    = ["code"]
  config.acr_values                        = ["basic"]
  config.client_jwks_uri                   = ""
  config.client_token_endpoint_auth_method = ""
  config.client_request_uris               = []
  config.contacts                          = ["example-email@gmail.com"]
  config.client_frontchannel_logout_uris   = ['https://domain.example.com/logout']
  config.connection_type                   = "local"
  config.dynamic_registration              = true
end
```

oxd-Ruby can communicate with the oxd server via sockets or HTTPS. There is **no difference** in code--just toggle the `config.connection_type` configuration property from `local` to `web` when you need to use `oxd-https-extension`. Sockets are used when the oxd server is running locally.

Below are minimal configuration examples for sockets and HTTPS transport.

!!! Note
    The client hostname should be a valid hostname (FQDN), not a localhost or an IP Address

## Sample Code

### Initialization

The plugin requires editing the `application_controller.rb` file to include the following snippet.

```ruby
require 'oxd-ruby'
before_filter :set_oxd_commands_instance
  
  protected
    def set_oxd_commands_instance
      @oxd_command = Oxd::ClientOxdCommands.new
      @uma_command = Oxd::UMACommands.new
      @oxdConfig = @oxd_command.oxdConfig
    end
```

### Setup Client

This step is necessary only for sites using oxd-https-extension. The sites using oxd-server via sockets can skip `setup_client` and directly register the app using `register_site`

```ruby
@oxd_command.setup_client
```

### Get Client Token

After setting up site, the client should get a protection token. This token will be cached by oxd-Ruby as `protection_access_token` in the config file and passed on to the HTTPS extension as an `Authentication` header for each request.

```ruby
access_token = @oxd_command.get_client_token
```

### Introspect Access Token

The `introspect_access_token` method is used to gain information about the `access_token` generated from `get_client_token` method.

```ruby
@oxd_command.introspect_access_token
```

### Register Site

```ruby
@oxd_command.register_site
```

### Get Authorization URL

```ruby
authorization_url = @oxd_command.get_authorization_url
```

### Get Tokens By Code

```ruby
@access_token = @oxd_command.get_tokens_by_code(code, state)
```

### Get User Info

```ruby
user_claims = @oxd_command.get_user_info(@access_token)

print user_claims['username']
print user_claims['website']
```

### Get Logout URI

```ruby
# redirect the user to this URL for logout
logout_url = @oxd_command.get_logout_uri(state, session_state)
```

### Update Site

```ruby
@oxdConfig.scope = ['openid','profile','uma_protection']
@oxdConfig.contacts = ["example.user@gmail.com"]

status = @oxd_command.update_site
```

### Remove Site

```ruby
@oxd_command.remove_site
```

### UMA RS Protect

Request with scopes:

```ruby
# define the resource
condition1 = {
      :httpMethods => ["GET"],
      :scopes => ["http://photoz.example.com/dev/actions/view"]
    }
@uma_command.uma_add_resource("/photoz", condition)
response = @uma_command.uma_rs_protect # Register above resources with UMA RS
```

Request with `scope_expression`. `scope_expression` is a Gluu-invented extension which can use JsonLogic expressions instead of a single list of scopes.

```ruby
condition = {
  :httpMethods => ["GET"],
  :scope_expression => {
    :rule => { 
      :and => [
        {
          :or => [{:var => 0}, {:var => 1}]
        },
        {:var => 2}
      ]
    },
    :data => [
      "http://photoz.example.com/dev/actions/all",
      "http://photoz.example.com/dev/actions/add",
      "http://photoz.example.com/dev/actions/internalClient"
    ]
  }
}
@uma_command.uma_add_resource("/photoz", condition)
response = @uma_command.uma_rs_protect # Register above resources with UMA RS
```

### UMA RS Check Access

```ruby
path = '/photoz'
http_method = 'GET'

@uma_command.uma_rs_check_access(path, http_method)
```

### Introspect RPT

`introspect_rpt` method is used to gain information about obtained RPT

```ruby
@uma_command.introspect_rpt
```

### UMA RP Get RPT

```ruby
@uma_command.uma_rp_get_rpt( claim_token: claim_token, claim_token_format: claim_token_format, pct: pct, rpt: rpt, scope: scope, state: state )
```

### UMA RP Get Claims Gathering URL 

```ruby
claims_redirect_uri = 'https://client.example.com/gather_claims' # Required parameter
@uma_command.uma_rp_get_claims_gathering_url(claims_redirect_uri)
```

## Tests

oxd-ruby contains `rspec` tests for Behaviour Driven Development. 

* **Rspec Tests** can be found in `spec` directory and can be run using `rspec` command.

```bash
rspec
```

Make sure you have set up OpenID provider and oxd-server before running specs.

## Support

Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.
