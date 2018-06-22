# Node

## Installation

### Prerequisites

- Node >= 6.x.x and NPM >= 3.x.x
- npm 3.10.10

### Library

- *Install oxd-node via npm* - `npm install oxd-node`
- *Source from Github* -   [Download](https://github.com/GluuFederation/oxd-node/archive/3.1.2.zip)

### Important Links

- [oxd docs](https://gluu.org/docs/oxd)
- See the code of a [sample node app](https://github.com/GluuFederation/oxd-node/tree/3.1.3/oxd-node-demo) built using oxd-node
- Browse the oxd-node [source code on Github](https://github.com/GluuFederation/oxd-node)


## Configuration

oxd-node can communicate with the oxd server via sockets or HTTPS. There is no difference in code--just toggle the https_extension configuration property. Sockets are used when the oxd server is running locally.

!!! Note
    The client hostname should be a valid hostname (FQDN), not a localhost or an IP Address

**Configuration for oxd-server via sockets:**

```
const config = {
  https_extension: false,
  host: 'localhost',
  port: '8099'
};

const oxd = require('oxd-node')(config);
```

**Configuration for oxd-https-extension:**

```
const config = {
  https_extension: true,
  host: 'https://server.running.oxd-https-extension',
};

const oxd = require('oxd-node')(config);
```

## Sample Code 

### Setup Client

```javascript
oxd.setup_client({
  op_host: 'https://<ophostname>',
  authorization_redirect_uri: 'https://client.example.com/cb',
  scope: ['openid', 'email', 'profile', 'uma_protection'],
  grant_types: ['authorization_code'],
  client_name: 'oxd_node_client'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }

  console.log(response);
});
```

**Response:**

```javascript
{
   "status":"ok",
   "data":{
      "oxd_id":"3157eea6-ae23-4fab-bc30-67bf7b232fa2",
      "op_host":"<op host name>",
      "client_id":"@!81F6.6C2A.5FCE.C408!0001!28BA.CADF!0008!8BAE.6164.744B.1179",
      "client_secret":"fe1125ad-a690-4445-80c0-07689b150567",
      "client_registration_access_token":"1aa82ea6-5d6d-4e30-9bb6-65d2b9a76ace",
      "client_registration_client_uri":"<op host name>/oxauth/restv1/register?client_id=@!81F6.6C2A.5FCE.C408!0001!28BA.CADF!0008!8BAE.6164.744B.1179",
      "client_id_issued_at":1506538221,
      "client_secret_expires_at":1506624621
   }
}
```


### Get Client Token

```javascript
oxd.get_client_token({
  client_id: '<client id>',
  client_secret: '<client secret>',
  scope: ['openid', 'email', 'profile', 'uma_protection'],
  protection_access_token:'<access token of the client>'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }

  console.log(response);
});
```

**Response:**

```javascript
{
   "status":"ok",
   "data":{
      "scope":"openid",
      "access_token":"c9561a2b-0d20-4dc1-a047-9dd36a9ef932",
      "expires_in":299,
      "refresh_token":null
   }
}
```


### Introspect Access Token

```javascript
oxd.introspect_access_token({
  oxd_id: '<oxd Id>',
  access_token:'<access token of the client>' 
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }
  console.log(response);
});
```

**Response:**

```javascript
{
  "status":"ok",
  "data":{
      "active": true,
      "client_id": "l238j323ds-23ij4",
      "username": "John Black",
      "scopes": ["read", "write"],
      "token_type":"bearer"
      "sub": "jblack",
      "aud": "l238j323ds-23ij4",
      "iss": "https://as.gluu.org/",
      "exp": 1419356238,
      "iat": 1419350238,
      "acr_values": ["basic","duo"],
      "extension_field": "twenty-seven"
  }
}
```


### Register Site

```javascript
oxd.register_site({
  op_host: 'https://<ophostname>',
  authorization_redirect_uri: 'https://client.example.org/cb',
  scope: ['openid', 'email', 'profile', 'uma_protection'],
  grant_types: ['authorization_code'],
  client_name: 'oxd_node_client',
  protection_access_token:'<access token of the client>'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }

  console.log(response);
});
```

**Response:**

```javascript
{
    "status":"ok",
    "data":{
        "oxd_id":"6F9619FF-8B86-D011-B42D-00CF4FC964FF"
    }
}
```


### Update Site

```javascript
oxd.update_site({
  op_host: 'https://gluu.local.org',
  authorization_redirect_uri: 'https://client.example.com:1338',
  scope: ['openid', 'email', 'profile', 'uma_protection'],
  grant_types: ['authorization_code'],
  client_name: 'oxd_node_client',
  protection_access_token:'<access token of the client>'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }

  console.log(response);
});
```

**Response:**

```javascript
{
   "status":"ok",
   "data":{
      "oxd_id":"3157eea6-ae23-4fab-bc30-67bf7b232fa2"
   }
}
```


### Remove Site

```javascript
oxd.remove_site({
  oxd_id: '<oxd Id>',
  protection_access_token:'<access token of the client>' 
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }
  console.log(response);
});
```

**Response:**

```javascript
{
    "status":"ok",
    "data": {
        "oxd_id": "6F9619FF-8B86-D011-B42D-00CF4FC964FF"
    }
}
```


### Get Authorization URL

```javascript
oxd.get_authorization_url({
  oxd_id: '<oxd Id>',
  scope: ['openid', 'profile', 'email', 'uma_protection'],
  protection_access_token:'<access token of the client>'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }

  console.log(response);
});
```

**Response:**

```javascript
{
   "status":"ok",
   "data":{
      "authorization_url":"<op host name>/oxauth/restv1/authorize?response_type=code&client_id=@!81F6.6C2A.5FCE.C408!0001!28BA.CADF!0008!4556.1F73.37B4.58A3&redirect_uri=https://client.example.com:5053/login_redirect&scope=openid+profile+email+uma_protection+uma_authorization&state=59qn4ebb3b2v3idnvbge92ja52&nonce=cdrnf8dgpm1rjbora0q9ev0u0&custom_response_headers=%5B%7B%22param1%22%3A%22value1%22%7D%2C%7B%22param2%22%3A%22value2%22%7D%5D"
   }
}
```


### Get Tokens by Code

```javascript
oxd.get_tokens_by_code({
  oxd_id: '<oxd Id>',
  code: '<code>',
  state: '<state>',
  protection_access_token:'<access token of the client>'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }

  console.log(response);
});
```

**Response:**

```javascript
{
   "status":"ok",
   "data":{
      "access_token":"1fd5b6b4-ffe3-49e3-b979-c277eacc53e9",
      "expires_in":299,
      "id_token":"eyJraWQiO...kecPC87xw20ntg",
      "refresh_token":"1ffe5bf0-6cbc-44e9-a1a9-cfdb48f2a499",
      "id_token_claims":{}
   }
}
```


### Get Access Token by Refresh Token

```javascript
oxd.get_access_token_by_refresh_token({
  oxd_id: '<oxd Id>',
  refresh_token: '<refresh token>',
  scope: ['openid', 'profile'],
  protection_access_token:'<access token of the client>'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }

  console.log(response);
});
```

**Response:**

```javascript
{  
   "status":"ok",
   "data":{  
      "scope":"openid profile uma_protection uma_authorization email",
      "access_token":"147a8147-b7bc-40f4-b280-18449a140d42",
      "expires_in":299,
      "refresh_token":"57ad0c8b-4da8-45d7-8cf0-e348dca75dad"
   }
}
```

### Get User Info

```javascript
oxd.get_user_info({
  oxd_id: '<oxd Id>',
  access_token: '<access token>',
  protection_access_token:'<access token of the client>'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }

  console.log(response);
});
```

**Response:**

```javascript
{  
   "status":"ok",
   "data":{  
      "claims":{  
         "sub":[  
            "3Mu18fK0_ZOKb56pVUDp0sQEluwE5j1tpPfu0PK1YZE"
         ],
         "updated_at":[  
            "20170925070950.669Z"
         ],
         "name":[  
            "Jane Doe"
         ],
         "given_name":[  
            "Jane"
         ],
         "locale":[  
            "en"
         ],
         "family_name":[  
            "Doe"
         ],
         "email":[  
            "janedoe@example.com"
         ]
      },
      "refresh_token":null,
      "access_token":null
   }
}
```


### Get Logout URI

```javascript
oxd.get_logout_uri({
  oxd_id: '<oxd Id>',
  protection_access_token:'<access token of the client>'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }

  console.log(response);
});
```

**Response:**

```javascript
{  
   "status":"ok",
   "data":{  
      "uri":"<op host name>/oxauth/restv1/end_session?id_token_hint=eyJraWQiO..87xw20ntg"
   }
}
```

### UMA RS Protect

```javascript
oxd.uma_rs_protect({
    oxd_id: '<oxd Id>',
    protection_access_token:'<access token of the client>'
    resources: [
      {
        path: '/photo',
        conditions: [
          {
            httpMethods: [
              'GET'
            ],
            scopes:["https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1"],
            ticketScopes:["https://scim-test.gluu.org/identity/seam/resource/restv1/scim/vas1"]
          }
        ]
      }
    ]
  },
  (err, response) => {
    if (err) {
      console.log('Error : ', err);
      return;
    }

    console.log(response);
  });
```

**RS Protect with scope_expression:**

```javascript
oxd.uma_rs_protect({
    oxd_id: '<oxd Id>',
    protection_access_token:'<access token of the client>'
    resources: [
      {
        path: '/photo',
        conditions: [
          {
            httpMethods: [
              'GET'
            ],
            scope_expression: {
              rule: {
                and: [
                  {
                    or: [
                      {
                        var: 0
                      },
                      {
                        var: 1
                      }
                    ]
                  },
                  {
                    var: 2
                  }
                ]
              },
              data: [
                'http://photoz.example.com/dev/actions/all',
                'http://photoz.example.com/dev/actions/add',
                'http://photoz.example.com/dev/actions/internalClient'
              ]
            }
          }
        ]
      }
    ]
  },
  (err, response) => {
    if (err) {
      console.log('Error : ', err);
      return;
    }

    console.log(response);
  });
```

**Response:**

```javascript
result = true
```


### UMA RS Check Access 

```javascript
oxd.uma_rs_check_access({
  oxd_id: '<oxd Id>',
  rpt: '<RPT>',
  path: '<path of resource>',
  http_method: '<http method of RP request>',
  protection_access_token:'<access token of the client>'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }
  console.log(response);
});
```

**Response:**

***Access Granted response:***

```javascript
{
    "status":"ok",
    "data":{
        "access":"granted"
    }
}
```

***Access Denied with ticket response:***

```javascript
{
    "status":"ok",
    "data":{
        "access":"denied"
        "www-authenticate_header":"UMA realm=\"example\",
                                   as_uri=\"https://as.example.com\",
                                   error=\"insufficient_scope\",
                                   ticket=\"016f84e8-f9b9-11e0-bd6f-0021cc6004de\"",
        "ticket":"016f84e8-f9b9-11e0-bd6f-0021cc6004de"
    }
}
```

***Access Denied without ticket response:***

```javascript
{
    "status":"ok",
    "data":{
        "access":"denied"
    }
}
```

***Resource is not protected***

```javascript
{
    "status":"error",
    "data":{
        "error":"invalid_request",
        "error_description":"Resource is not protected. Please protect your resource first with uma_rs_protect command."
    }
}
```


### UMA Introspect RPT

```javascript
oxd.introspect_rpt({
  oxd_id: '<oxd Id>',
  rpt:'<access token of the client>' 
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }
  console.log(response);
});
```

**Response:**

```javascript
{
  "status":"ok",
  "data":{
      "active":true,
      "exp":1256953732,
      "iat":1256912345,
      "permissions":[  
          {  
              "resource_id":"112210f47de98100",
              "resource_scopes":[  
                  "view",
                  "http://photoz.example.com/dev/actions/print"
              ],
              "exp":1256953732
          }
      ]
  }
}
```


### UMA RP Get RPT 

```javascript
oxd.uma_rp_get_rpt({
  oxd_id: '<oxd Id>',
  ticket: '<ticket>',
  protection_access_token:'<access token of the client>'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }
  console.log(response);
});
```

**Response:**

***Success Response:***

```javascript
 {
     "status":"ok",
     "data":{
         "access_token":"SSJHBSUSSJHVhjsgvhsgvshgsv",
         "token_type":"Bearer",
         "pct":"c2F2ZWRjb25zZW50",
         "upgraded":true
     }
}
```

***Needs Info Error Response:***

```javascript
{
     "status":"error",
     "data":{
              "error":"need_info",
              "error_description":"The authorization server needs additional information in order to determine whether the client is authorized to have these permissions.",
              "details": {  
                  "error":"need_info",
                  "ticket":"ZXJyb3JfZGV0YWlscw==",
             "required_claims":[  
                   {  
                     "claim_token_format":[  
                         "http://openid.net/specs/openid-connect-core-1_0.html#IDToken"
                     ],
                     "claim_type":"urn:oid:0.9.2342.19200300.100.1.3",
                     "friendly_name":"email",
                     "issuer":["https://example.com/idp"],
                     "name":"email23423453ou453"
                   }
                 ],
             "redirect_user":"https://as.example.com/rqp_claims?id=2346576421"
         }
     }
}
```

***Invalid Ticket Error Response:***

```javascript
 {
    "status":"error",
    "data":{
            "error":"invalid_ticket",
            "error_description":"Ticket is not valid (outdated or not present on Authorization Server)."
           }
 }
```


### UMA RP Get Claims Gathering URL 

```javascript
oxd.uma_rp_get_claims_gathering_url({
  oxd_id: '<oxd Id>',
  ticket: '<ticket>',
  claims_redirect_uri: 'https://client.example.com/cb',
  protection_access_token:'<access token of the client>'
}, (err, response) => {
  if (err) {
    console.log('Error : ', err);
    return;
  }
  console.log(response);
});
```

**Response:**

```javascript
{
    "status":"ok",
    "data":{
        "url":"https://<op host>/restv1/uma/gather_claims
              ?client_id=@!1736.179E.AA60.16B2!0001!8F7C.B9AB!0008!AB77!1A2B
              &ticket=4678a107-e124-416c-af79-7807f3c31457
              &claims_redirect_uri=https://client.example.com/cb
              &state=af0ifjsldkj",
        "state":"af0ifjsldkj" 
    }
}
```

## Tests

oxd-node contains extensive tests for quality assurance

```
npm test
```

## Examples

The `oxd-node-demo` directory contains apps and scripts written using oxd-node for OpenID Connect.

## Support

Please report technical issues and suspected bugs on our [Support Page](https://support.gluu.org/). You can use the same credentials you created to register your oxd license to sign in on Gluu support.
