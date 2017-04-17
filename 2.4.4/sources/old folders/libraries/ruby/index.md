#oxd-ruby

oxd-ruby is a client library for the oxd Server. 

## Installation

- [Github sources](https://github.com/GluuFederation/oxd-ruby)

- [Rubygems](https://rubygems.org/gems/oxd-ruby)

### Install Instructions

**Note :** This library will not work if your website does not have SSL installed. Make sure your website runs on https:// before using this library.

The Ruby Client is installed using RubyGems. Please include following 
line in the Gemfile of the application using Oxd Ruby Library.

```
gem 'oxd-ruby', '~> 0.1.6'
```

Please run the bundle command after to install the `oxd-ruby` plugin.

```
$ bundle install
```

## Configuration
The configurations must be generated first using the following command.

```
$ rails generate oxd:config
```

This command will install the `oxd_config.rb` initializer file in the `config/initializers` directory which contains all the global configuration options for the ruby plugin. The following configurations must be set before the plugin can be used.

1. config.oxd_host_ip
2. config.oxd_host_port
3. config.op_host 
4. config.authorization_redirect_uri

## Sample code

- [API Docs](http://www.rubydoc.info/gems/oxd-ruby)

The plugin requires editing the `application_controller.rb` file to include the following snippet.

```
require 'oxd-ruby'

before_filter :set_oxd_commands_instance
protected
    def set_oxd_commands_instance
        @oxd_command = Oxd::ClientOxdCommands.new
        @uma_command = Oxd::UMACommands.new
    end
```

### OP commands sample

```ruby
def register_site			
	@oxd_command.register_site 
	authorization_url = @oxd_command.get_authorization_url
	redirect_to authorization_url
end

def login
	if (params[:code].present?)
		@access_token = @oxd_command.get_tokens_by_code( params[:code] ) 
	end
        session.delete('oxd_access_token') if(session[:oxd_access_token].present?)
    	session[:oxd_access_token] = @access_token
    	session[:state] = params[:state]
    	session[:session_state] = params[:session_state]
	@user = @oxd_command.get_user_info(session[:oxd_access_token]) 	
end

def logout
	if(session[:oxd_access_token])
		@logout_url = @oxd_command.get_logout_uri(
				session[:oxd_access_token], session[:state], session[:session_state]
			)
		redirect_to @logout_url
	end	    
end
```

### UMA Commands sample

#### UMA RS commands

```ruby
def protect_resources
	condition1 = {:httpMethods => ["GET"], :scopes => ["http://photoz.example.com/dev/actions/view"]}
	condition2 = {:httpMethods => ["PUT", "POST"], :scopes => ["http://photoz.example.com/dev/actions/add"]}
	@uma_command.uma_add_resource("/photo", condition1, condition2)

    response = @uma_command.uma_rs_protect # Register above resources with UMA RS
end

 def check_access
     # Pass the resource path and http method to check access
    response = @uma_command.uma_rs_check_access('/photo', 'GET') 
end
```

#### UMA RP commands

```ruby
def get_rpt
    rpt = @uma_command.uma_rp_get_rpt('false') # Get RPT
end

def authorize_rpt
	response = @uma_command.uma_rp_authorize_rpt # Authorize RPT
end
```
#### Get GAT

```ruby
def get_gat 
    scopes = ["http://photoz.example.com/dev/actions/add","http://photoz.example.com/dev/actions/view"]
	gat = @uma_command.uma_rp_get_gat(scopes) # Pass scopes array to get GAT
end
```
