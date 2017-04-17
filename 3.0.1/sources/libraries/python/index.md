# oxd Python

## Overview
The following documentation demonstrates 
how to use Gluu's commercial OAuth 2.0 client software, [oxd](http://oxd.gluu.org), 
to send users from a Python app to an OpenID Connect Provider (OP) for login. You can 
securely send users to any standard OP for login, including Google and 
the [free open source Gluu Server](http://gluu.org/gluu-server).

## Installation

### Prerequisites

* Python 2.7
* Gluu oxd Server - [Installation docs](../../install/)

### Library
* Install via pip

```
pip install oxdpython
```

* *Source from Github* -  Download the zip of the oxD Python Library from 
[here](https://github.com/GluuFederation/oxd-python/releases) and unzip to your location of choice

```
cd oxdpython-version
python setup.py install
```

#### Important Links

* See the [API docs](https://oxd.gluu.org/api-docs/python/) for in-depth information about 
the various functions and their parameters.
* See the code of a [sample Flask app](https://github.com/GluuFederation/oxd-python/blob/master/demosite) built using oxd-python.
* Browse the source code is hosted in Github [here](https://github.com/GluuFederation/oxd-python).


## Configuration

This library uses a configuration file to specify information needed
by OpenID Connect dynamic client registration, and to save information 
that is returned, like the client id. So the config file needs to be 
*writable by the app*.

The minimal configuration required to get oxd-python working:

```
[oxd]
host = localhost
port = 8099

[client]
authorization_redirect_uri=https://your.site.org/callback
```

!!!Note: 
    The [sample.cfg](https://github.com/GluuFederation/oxd-python/blob/master/sample.cfg)
file contains detailed documentation about the configuration values.

## Sample Code

#### Website Registration

```python
from oxdpython import Client

config = "/var/www/demosite/demosite.cfg"  # This should be writable by the server
client = Client(config)
client.register_site()
```

!!!**Note:** 
    `register_site()` can be skipped as any `get_authorization_url()`
automatically registers the site.

#### Get Authorization URL

```python
auth_url = client.get_authorization_url()
```

#### Get Tokens

```python
# code = parse_callback_url_querystring()  # Refer your web framework
tokens = client.get_tokens_by_code(code)
```

#### Get User Claims

```python
user = oxc.get_user_info(tokens.access_token)

# The claims can be accessed using the dot notation.
print user.username
print user.website

print user._fields  # to print all the fields

# to check for a particular field and get the information
if 'website' in user._fields:
    print user.website
```

#### Logout

```python
logout_uri = oxc.get_logout_uri()
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
rpt = client.uma_rp_get_rpt()

# To force a new RPT
rpt = client.uma_rp_get_rpt(True)
```

#### UMA RP Authorize RPT

```python
rpt = 'rpt-token-string'
ticket = 'ticket-value-as-string'

response = client.uma_rp_authorize_rpt(rpt, ticket)
```

#### UMA RP Get GAT

```python
scopes = ["http://photoz.example.com/dev/actions/add",
          "http://photoz.example.com/dev/actions/view"
          ]

gat = client.uma_rp_get_gat(scopes)
```
