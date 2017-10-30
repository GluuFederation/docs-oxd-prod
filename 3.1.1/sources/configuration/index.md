# Configuration

oxd configuration consists of two files :

- `conf/oxd-conf.json` is the general configuration file for oxd, including where you add your license. If you have not yet obtained a license, you can [register on the website for free](https://oxd.gluu.org). 

- `conf/oxd-default-site-config.json` is the fallback configuration file for the OpenID Connect `Register Site` command. Learn more on the [oxd API page](../api/index.md#register-site).

## oxd-conf.json

The contents of the `oxd-conf.json` file is as follows:

```
oxd-conf.json
{
    "server_name":"",
    "port":8099,
    "localhost_only":true,
    "time_out_in_seconds":0,
    "use_client_authentication_for_pat":true,
    "trust_all_certs":true,
    "trust_store_path":"",
    "trust_store_password":"",
    "license_id":"",
    "public_key":"",
    "public_password":"",
    "license_password":"",
    "support-google-logout":true,
    "state_expiration_in_minutes":5,
    "nonce_expiration_in_minutes":5,
    "public_op_key_cache_expiration_in_minutes":60,
    "protect_commands_with_access_token":false,
    "uma2_auto_register_claims_gathering_endpoint_as_redirect_uri_of_client":true,
    "migration_source_folder_path":"",
    "storage":"h2",
    "storage_configuration": {
        "dbFileLocation":"/opt/oxd-server/bin/oxd_db"
    }
}
```
### oxd-conf.json field descriptions

- **server_name**: human readable name which will be used by the License Server to identify the server you are installing oxd on. It is good idea to put some sensible name here.

- **port**: oxd socket port.

- **localhost_only**: flag to restrict communication.

- **time_out_in_seconds**: time out for oxd socket (measured in seconds). oxd closes sockets automatically after this period of time (stops listen commands). Zero means listen indefinitely.

- **use_client_authentication_for_pat**: `true` if client authentication is required. If `false` then user authentication requires `user_id` and `user_secret` to be specified during the `register_site` command.

- **trust_all_certs**: `true` to trust all certificates, if `false` then `trust_store_path` must be specified to store with valid certificates.

- **trust_store_path**: Path to Java `.jks` trust store to be used for an SSL connection.

- **trust_store_password**: password of trust store.

- **license_id**: Will be supplied when you [register for a license](https://oxd.gluu.org). 

- **public_key**: Will be supplied when you [register for a license](https://oxd.gluu.org). It's very big--make sure to add the `public_key` as one line with no spaces.

- **public_password**: Will be supplied when you [register for a license](https://oxd.gluu.org).

- **license_password**: Will be supplied when you [register for a license](https://oxd.gluu.org).

- **support-google-logout**: whether to support Google logout or not. Only use this if you are using Google as your OP.

- **state_expiration_in_minutes**: expiration time of `state` parameter in seconds.

- **nonce_expiration_in_minutes**: expiration time of `nonce` parameter in seconds.

- **public_op_key_cache_expiration_in_minutes**: OP keys are put into cache after fetching. This value controls how long to keep it in cache (after expiration on first attempt keys are fetched again from OP).

- **protect_commands_with_access_token**: if you are only using the `oxd-server` this value can be `false`. If you are using the `oxd-https-extension`, then this value MUST be `true` in order to protect communication between `oxd-https-extension` and the client application (RP).

- **uma2_auto_register_claims_gathering_endpoint_as_redirect_uri_of_client**: notifies the `oxd-server` whether to automatically register the `Claims Gathering Endpoint` as the `redirect_uri` for a given client. It is useful for UMA 2 clients that wish to force authorization against the Gluu Server.

- **migration_source_folder_path**: `oxd-server` has built-in migration from older version of `oxd-server`. To migrate old json files from previous versions please specify path to folder/directory that contains those json files in this property. Those files will be read and imported one time (during restart `oxd-server` will not import them again). Note, if you are under Windows OS don't forget to escape path separator, e.g. `C:\\OXD_OLD\\oxd-server\\conf`

- **storage**: This value can either be `h2` or `redis`. If `redis` is set then `storage_configuration` must be specified with redis configuration details. 

- **storage_configuration**: storage configuration details. Required if `redis` value is set for `storage` key.

Redis storage configuration sample:

```json
  "storage_configuration": {
    "host":"localhost",
    "port":6379
  }
```

H2 storage configuration sample:
```json
    "storage_configuration": {
        "dbFileLocation":"/opt/oxd-server/bin/oxd_db"
    }
```

!!! Note
    If you need a license to start your oxd-server, you can register on the [oxd website](https://oxd.gluu.org). 

## oxd-default-site-config.json

The contents of the `oxd-default-site-config.json` file is as follows:


```json
conf/oxd-default-site-config.json
{
    "op_host":"",
    "op_discovery_path":"",
    "authorization_redirect_uri":"",
    "post_logout_redirect_uri":"",
    "redirect_uris":[""],
    "response_types":["code"],
    "grant_type":["authorization_code"],
    "acr_values":[""],
    "scope":["openid", "profile", "email"],
    "ui_locales":["en"],
    "claims_locales":["en"],
    "client_jwks_uri":"",
    "contacts":[]
}
```

### oxd-default-site-config.json field descriptions

- **op_host**: must point to a valid 
[Gluu Server CE installation](https://gluu.org/docs/ce/3.0.1/installation-guide/install/). (Sample : "op_host":"https://idp.example.org")

- **op_discovery_path**: path to the OpenID Connect Provider's discovery document. For example if it is `https://example.com/.well-known/openid-configuration` then path is blank ` `. But if it is `https://example.com/oxauth/.well-known/openid-configuration` then path is `oxauth`. 

- **authorization_redirect_uri**: URL that the OpenID Connect Provider (OP) will redirect the person to after  successful authentication

- **post_logout_redirect_uri**: URL to which the RP is requesting that the End-User's User Agent be redirected after a logout has been performed

- **redirect_uris**: additional URLs that the OpenID Connect Provider (OP) will use to redirect the person to after  successful authentication

- **response_types**: JSON array containing a list of the OAuth 2.0 response_type values that the site is declaring that it will restrict itself to using

- **grant_type**: JSON array containing a list of the OAuth 2.0 Grant Types that the Client is declaring that it will restrict itself to using

- **acr_values**: specified authentication method (basic, duo, u2f)

- **scope**: JSON array containing a list of the scopes that the Client is declaring that it will restrict itself to using

- **ui_locales**: End-User's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference

- **claims_locales**: End-User's preferred languages and scripts for Claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference

- **contacts**: array of e-mail addresses of people responsible for this Client
