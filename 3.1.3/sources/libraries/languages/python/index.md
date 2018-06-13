
## Installation

### Prerequisites

- Python 2.7
- Gluu oxd server - [Installation docs](https://gluu.org/docs/oxd/install/)
- SSL certificate for the application

### Library

- *Install from Pip* - `pip install oxdpython`

- *Source from Github* -   [Download](https://github.com/GluuFederation/oxd-python/archive/3.1.3.zip) the zip of the oxd Python library and run the setup script:

```
cd oxdpython-version
python setup.py install
```

### Important Links

- [oxd docs](https://gluu.org/docs/oxd)
- oxd-python [API docs](https://rawgit.com/GluuFederation/oxd-python/3.1.3/docs/html/index.html) for the auto-generated pydocs, which includes more in-depth information about the various functions and parameters
- See the code of a [sample Flask app](https://github.com/GluuFederation/oxd-python/blob/3.1.3/examples/flask_app) built using oxd-python
- Browse the oxd-python [source code on Github](https://github.com/GluuFederation/oxd-python)

## Configuration

oxd-python uses a configuration file to specify information needed to configure your OpenID Connect client. If OpenID dynamic client registration is used, the config file needs to be *writable by the app*, because oxd will save the client ID and client secret to this file.

oxd-python can communicate with the oxd server via sockets or HTTPS. There is **no difference** in code--just toggle the `https_extension` configuration property. Sockets are used when the oxd server is running locally.

Below are minimal configuration examples for sockets and HTTPS transport. The [sample.cfg](https://github.com/GluuFederation/oxd-python/blob/3.1.3/sample.cfg) file contains a full list of configuration parameters and sample values. 

!!! Note
    The client hostname should be a valid hostname (FQDN), not a localhost or an IP Address.

**Configuration for oxd-server via sockets:**

```ini
[oxd]
host = localhost
port = 8099

[client]
authorization_redirect_uri=https://your.site.org/callback
```

**Configuration for oxd-https-extension:**

```ini
[oxd]
host = https://server.running.oxd-https-extension/
https_extension = true

[client]
authorization_redirect_uri=https://your.site.org/callback
```

## Sample Code

### Initialization

```python
from oxdpython import Client

config = "/opt/yoursite/yoursite.cfg"  # This should be writable by the server
client = Client(config)

```

### Setup Client

This step is necessary only for sites using oxd-https-extension. The sites using
oxd-server via sockets can skip `setup_client()` and directly register the app using
`register_site()`

```python
client.setup_client()
```

### Get Client Token

After setting up the site, the client should get a protection token. This token will
be cached by oxd-python as `protection_access_token` in the config file and passed
on to the HTTPS extension as an `Authentication` header for each request.

```python
client_token = client.get_client_token()
```

### Introspect Access Token

```python
response = client.introspect_access_token(token['access_token'])
```

### Register Site

```python
client.register_site()
```

!!! Note 
    `register_site()` can be skipped since any request to the `get_authorization_url()` automatically registers the site.

### Update Site

```python
client.config.set('client', 'post_logout_uri', 'https://client.example.org/post_logout')

# ensure lists are converted to comma separated string
scopes = ','.join(['openid','profile','uma_protection'])
client.config.set('client', 'scope', scopes)

client.update_site_registration()
```

### Remove Site

```python
client.remove_site()
```

### Get Authorization URL

```python
auth_url = client.get_authorization_url()
```

### Get Tokens By Code

```python
# code = parse_callback_url_querystring()  # Refer your web framework
# state = parse_callback_url_querystring()  # Refer your web framework
tokens = client.get_tokens_by_code(code, state)
```

### Get Access Token by Refresh Token

```python
new_tokens = client.get_access_token_by_refresh_token(tokens["refresh_token"])
```

### Get User Info

```python
claims = client.get_user_info(tokens['access_token'])

print claims['username']
print claims['website']
```

### Get Logout URI

```python
# redirect the user to this URL for logout
logout_uri = client.get_logout_uri()
```


### UMA RS Protect

```python
# define the resource
resources = [{"path": "/photo",
              "conditions": [
                {
                    "httpMethods": ["GET"],
                    "scopes": ["http://photoz.example.com/dev/actions/view"]
                 }]
            }]

result = client.uma_rs_protect(resources=resources, overwrite=True)
```

**RS Protect with scope_expression**

```python
resources = [{
	"path": "/photo",
	"conditions": [{
		"httpMethods": ["GET"],
		"scope_expression": {
			"rule": {
				"and": [{
						"or": [{
								"var": 0
							},
							{
								"var": 1
							}
						]
					},
					{
						"var": 2
					}
				]
			},
			"data": [
				"http://photoz.example.com/dev/actions/all",
				"http://photoz.example.com/dev/actions/add",
				"http://photoz.example.com/dev/actions/internalClient"
			]
		}
	}]
}]

result = client.uma_rs_protect(resources=resources, overwrite=True)
```


### UMA RS Check Access

```python
rpt = 'lsjdfa-sfas234s'
path = '/photo'
http_method = 'GET'

response = client.uma_rs_check_access(rpt, path, http_method)
```

### UMA Introspect RPT

```python
client.introspect_rpt(rpt=rpt_response['access_token'])
```

### UMA RP Get RPT

```python
ticket = 'ticket obtained by app after user authorization'
rpt = client.uma_rp_get_rpt(ticket)
```

### UMA RP Get Claims Gathering URL 

```python
ticket = 'ticket obtained by app after user authorization'
claims_url = client.uma_rp_get_claims_gathering_url(ticket)
```

## Tests

oxd-python contains extensive tests for quality assurance. 

- **Unit Tests** can be found in `tests` directory and can be run using PyTest

```bash
pytest tests
```

- **Integration Tests** need a OpenID provider and oxd-server to be setup before
  running and can be found in the `examples/e2e_tests` directory
 
## Examples
 
The `examples` directory contains apps and scripts written using oxd-python for OpenID Connect.

## Support

Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.
