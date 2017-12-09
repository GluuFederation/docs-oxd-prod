# oxd-python

## Overview

Use oxd's Python library to send users from a Python application to your Gluu Server OpenID Connect Provider (OP) for dynamic enrollment, single sign-on (SSO), strong authentication, and access management policy enforcement. 

## Installation

### Prerequisites

* Python 2.7

### Library

* *Install from Pip* - `pip install oxdpython`

* *Source from Github* -  Download the zip of the oxD Python Library from [here](https://github.com/GluuFederation/oxd-python/) and unzip to your location of choice

```
cd oxdpython-version
python setup.py install
```

#### Important Links

* [oxd docs](https://gluu.org/docs/oxd)
* oxd-python [API docs](https://rawgit.com/GluuFederation/oxd-python/master/docs/html/index.html) for in-depth information about the various functions and their parameters.
* See the code of a [sample Flask app](https://github.com/GluuFederation/oxd-python/blob/master/examples/flask_app) built using oxd-python.
* Browse the source code is hosted in Github [here](https://github.com/GluuFederation/oxd-python).

## Configuration

This library uses a configuration file to specify information needed
by OpenID Connect for dynamic client registration, and to save information 
that is returned, like the client id. So the config file needs to be 
*writable by the app*.

oxd can be deployed either as oxd-server communicating via sockets, or with a oxd-https-extension communicating over HTTP.
oxd-python can be configured to work with both oxd-server and oxd-https-extension with just a flag in the configuration and **no difference** in code.
The minimal config required to get a site working with oxd are given below:

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

**Note:** The [sample.cfg](https://github.com/GluuFederation/oxd-python/blob/master/sample.cfg)
file contains the full list of the parameters and their detailed documentation.

## Sample Code

#### Initialization

```python
from oxdpython import Client

config = "/opt/yoursite/yoursite.cfg"  # This should be writable by the server
client = Client(config)

```

#### Setup site

This step is necessary only for sites using oxd-https-extension. The sites using
oxd-server via sockets can skip `setup_site()` and directly register the app using
`register_site()`

```python
client.setup_client()
```

#### Get Client Token

After setting up site, the client should get a protection token. This token will
be cached by oxd-python as `protection_access_token` in the config file and passed
on to the https extension as an `Authentication` header for each request.

```python
client_token = client.get_client_token()
```

#### Website Registration

```python
client.register_site()
```

**Note:** `register_site()` can be skipped as any `get_authorization_url()`
automatically registers the site.

#### Get Authorization URL

```python
auth_url = client.get_authorization_url()
```

#### Get User Tokens

```python
# code = parse_callback_url_querystring()  # Refer your web framework
# state = parse_callback_url_querystring()  # Refer your web framework
tokens = client.get_tokens_by_code(code, state)
```

#### Get User Claims

```python
claims = client.get_user_info(tokens['access_token'])

print claims['username']
print claims['website']
```

#### Logout

```python
# redirect the user to this URL for logout
logout_uri = client.get_logout_uri()
```

#### Update Site

```python
client.config.set('client', 'post_logout_uri', 'https://client.example.org/post_logout')

# ensure lists are converted to comma sperated string
scopes = ','.join(['openid','profile','uma_protection'])
client.config.set('client', 'scope', scopes)

client.update_site_registration()
```

#### UMA RS Protect

```python
# define the resource
resources = [{"path": "/photo",
              "conditions": [
                {
                    "httpMethods": ["GET"],
                    "scopes": ["http://photoz.example.com/dev/actions/view"]
                 }]
            }]

result = client.uma_rs_protect(resources)
```

#### UMA RS Check Access

```python
rpt = 'lsjdfa-sfas234s'
path = '/photo'
http_method = 'GET'

response = client.uma_rs_check_access(rpt, path, http_method)
```

#### UMA RP Get RPT

```python
ticket = 'ticket obtained by app after user authorization'
rpt = client.uma_rp_get_rpt(ticket)
```

#### UMA RP Get Claims Gathering URL 

```python
ticket = 'ticket obtained by app after user authorization'
claims_url = client.uma_rp_get_claims_gathering_url(ticket)
```

## Tests

oxd-python contains extensive tests for quality assurance. 

* **Unit Tests** can be found in `tests` [directory](https://github.com/GluuFederation/oxd-python/tree/master/tests) and can be run using PyTest.

```bash
# from oxd-python directory
pytest tests
```

* **Integration Tests** need a OpenID provider and oxd-server to be setup before
  running and can be found in the `examples/e2e_tests` [directory](https://github.com/GluuFederation/oxd-python/tree/master/examples/e2e_tests).
 
 
## Examples
 
The [examples directory](https://github.com/GluuFederation/oxd-python/tree/master/examples) of the Github Repo contains apps and scripts written using oxd-python for OpenID Connect.


## Support
Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.
