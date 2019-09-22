# Configuration (oxd-server.yml)

oxd configuration is located at `/opt/oxd-server/conf/oxd-server.yml`. It consists of three major parts:

- `server configuration` - oxd specific configuration
- `defaultSiteConfig` - fallback configuration values for the OpenID Connect `/register-site` command. Learn more on the [oxd API page](../api/index.md#register-site)
- Everything else that is inside comes from the Dropwizard framework. For a complete list of server-related parameters, click [here](http://www.dropwizard.io/1.3.1/docs/manual/configuration.html)

Here we will explain `server configuration` and `defaultSiteConfig`. Dropwizard configuration parameters can be checked in the Dropwizard [configuration documentation](http://www.dropwizard.io/1.3.1/docs/manual/configuration.html).

The content of the `/opt/oxd-server/conf/oxd-server.yml` file is as follows:

```yaml
oxd-server.yml

# server configuration
use_client_authentication_for_pat: true
trust_all_certs: true
trust_store_path: ''
trust_store_password: ''
crypt_provider_key_store_path: ''
crypt_provider_key_store_password: ''
crypt_provider_dn_name: ''
support-google-logout: true
state_expiration_in_minutes: 5
nonce_expiration_in_minutes: 5
public_op_key_cache_expiration_in_minutes: 60
protect_commands_with_access_token: true
uma2_auto_register_claims_gathering_endpoint_as_redirect_uri_of_client: true
add_client_credentials_grant_type_automatically_during_client_registration: true
migration_source_folder_path: ''
allowed_op_hosts: []
storage: h2
storage_configuration:
  dbFileLocation: /opt/oxd-server/data/oxd_db

# Dropwizard configurations
# Connectors
server:
  applicationConnectors:
    - type: https
      port: 8443
      keyStorePath: /opt/oxd-server/conf/oxd-server.keystore
      keyStorePassword: example
      validateCerts: false
  adminConnectors:
    - type: https
      port: 8444
      keyStorePath: /opt/oxd-server/conf/oxd-server.keystore
      keyStorePassword: example
      validateCerts: false

# Logging settings.
logging:

  # The default level of all loggers. Can be OFF, ERROR, WARN, INFO, DEBUG, TRACE, or ALL.
  level: INFO

  # Logger-specific levels.
  loggers:
    org.gluu: TRACE
    org.xdi: TRACE

# Logback's Time Based Rolling Policy - archivedLogFilenamePattern: /tmp/application-%d{yyyy-MM-dd}.log.gz
# Logback's Size and Time Based Rolling Policy -  archivedLogFilenamePattern: /tmp/application-%d{yyyy-MM-dd}-%i.log.gz
# Logback's Fixed Window Rolling Policy -  archivedLogFilenamePattern: /tmp/application-%i.log.gz

  appenders:
    - type: console
    - type: file
      threshold: INFO
      logFormat: "%-6level [%d{HH:mm:ss.SSS}] [%t] %logger{5} - %X{code} %msg %n"
      currentLogFilename: /var/log/oxd-server/oxd-server.log
      archivedLogFilenamePattern: /var/log/oxd-server/oxd-server-%d{yyyy-MM-dd}-%i.log.gz
      archivedFileCount: 7
      timeZone: UTC
      maxFileSize: 10MB

defaultSiteConfig:
  op_host: ''
  op_discovery_path: ''
  response_types: ['code']
  grant_type: ['authorization_code']
  acr_values: ['']
  scope: ['openid', 'profile', 'email']
  ui_locales: ['en']
  claims_locales: ['en']
  contacts: []

```
### Server configuration fields descriptions

- **use_client_authentication_for_pat:** If set to `true`, client authentication is required. If `false`, user authentication requires `user_id` and `user_secret` to be specified during the `register_site` command

- **trust_all_certs:** `true` to trust all certificates, if `false` then `trust_store_path` must be specified to store with valid certificates

- **trust_store_path:** Path to Java `.jks` trust store to be used for an SSL connections

- **trust_store_password:** Password to access the trust store

- **crypt_provider_key_store_path:** Path to the cryptologic service provider's key store
  
- **crypt_provider_key_store_password:** Password to access the cryptologic service provider's key store
 
- **crypt_provider_dn_name:** Cryptologic service provider's domain name

- **support-google-logout:** Choose whether to support Google logout or not. Only use this if you are using Google as your OP

- **state_expiration_in_minutes:** Expiration time of `state` parameter in seconds

- **nonce_expiration_in_minutes:** Expiration time of `nonce` parameter in seconds

- **public_op_key_cache_expiration_in_minutes:** OP keys are put into cache after fetching. This value controls how long to keep it in cache (after expiration on first attempt keys are fetched again from OP)

- **protect_commands_with_access_token:** If only using the `oxd-server`, this value can be `false`. If using the `oxd-https-extension`, then this value MUST be `true` in order to protect communication between `oxd-https-extension` and the client application (RP)

- **uma2_auto_register_claims_gathering_endpoint_as_redirect_uri_of_client:** Notifies the `oxd-server` whether to automatically register the `Claims Gathering Endpoint` as the `claims_redirect_uri` for a given client. It is useful for UMA 2 clients that wish to force authorization against the Gluu Server. To provide custom `claims_redirect_uri`, set this property to `false`

- **add_client_credentials_grant_type_automatically_during_client_registration:** If set to `true` then `client_credentials` grant type is automatically added to clients registered by oxd. If `false`, then `client_credentials` will not be automatically added to clients, but user can still add this grant type while registering clients in AS.

- **migration_source_folder_path:** Migration from previous versions is built into the `oxd-server`. To migrate old JSON files from previous versions, specify the path to folder/directory that contains those JSON files in this property. Those files will be read and imported once (during restart `oxd-server`, will not import them again). If using Windows OS, don't forget to escape the path separator, e.g. `C:\\OXD_OLD\\oxd-server\\conf`

- **allowed_op_hosts:** Array containing a list of the `op_host` urls. oxd can only access the `op_hosts` from this list and all other calls will be rejected. If the list is empty then oxd is allowed to access any OpenID Connect Provider.

- **storage:** This value can either be `h2` or `redis`. If `redis` is set, then `storage_configuration` must be specified with redis configuration details

- **storage_configuration:** Storage configuration details. Required if the `redis` value is set for the `storage` key

Redis storage configuration sample:

```yaml
  storage_configuration
    host: localhost
    port: 6379  
```

H2 storage configuration sample:

```yaml
    storage_configuration
      dbFileLocation: /opt/oxd-server/data/oxd_db    
```


### defaultSiteConfig Field Descriptions

- **op_host:** Provide the URL of your OpenID Provider (OP). (Example : "op_host":"`https://idp.example.org`")

- **op_discovery_path:** Path to the OpenID Connect Provider's discovery document. For example, if it is `https://example.com/.well-known/openid-configuration` then the path is blank ` `. But if it is `https://example.com/oxauth/.well-known/openid-configuration` then the path is `/oxauth`  

- **post_logout_redirect_uri:** URL to which the RP is requesting that the End-User's User Agent be redirected after a logout has been performed

- **redirect_uris:** Additional URLs that the OpenID Connect Provider (OP) will use to redirect the person to after successful authentication

- **response_types:** JSON array containing a list of the OAuth 2.0 response_type values that the site is declaring that it will restrict itself to using

- **grant_type:** JSON array containing a list of the OAuth 2.0 Grant Types that the Client is declaring that it will restrict itself to using

- **acr_values:** Preferred authentication method the client will receive from the OP (e.g. basic, Duo, U2F). The specified acr value must be enabled at the OP. If no value is specified, the client will receive the default authentication mechanism specified by the OP. Learn more about how Gluu Server uses acr's in [the docs](https://gluu.org/docs/ce/4.0/authn-guide/intro/). 

- **scope:** JSON array containing a list of the scopes that the Client is declaring that it will restrict itself to using

- **ui_locales:** End-User's preferred languages and scripts for the user interface, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference

- **claims_locales:** End-User's preferred languages and scripts for Claims being returned, represented as a space-separated list of BCP47 [RFC5646] language tag values, ordered by preference

- **contacts:** Array of e-mail addresses for people responsible for this client
