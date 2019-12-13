## What's New in Version 4.0

oxd 4.0 includes architectural changes as well as different bug fixes and improvements:

### New Features

### Fixes / Enhancements

- [#347](https://github.com/GluuFederation/oxd/issues/347) Do not use PAT to request introspection (avoid exception related to missed `uma_protection` )

- [#342](https://github.com/GluuFederation/oxd/issues/342) Bug : we got `post_logout_redirect_uris` included into `redirect_uris`

- [#338](https://github.com/GluuFederation/oxd/issues/338) add `idTokenSignedResponseAlg` and other params to `/register-site` command

- [#337](https://github.com/GluuFederation/oxd/issues/337) oxd has to use newest `setScope` oxauth-client method otherwise oxauth falls back to all default scopes

- [#334](https://github.com/GluuFederation/oxd/issues/334) Add `params` map to `/get-authorization-url` api

- [#331](https://github.com/GluuFederation/oxd/issues/331) post_logout_redirect_uri is left empty when registering client via oxd

- [#329](https://github.com/GluuFederation/oxd/issues/329) oxd: Getting Forbidden : 403 while using lsox script

- [#310](https://github.com/GluuFederation/oxd/issues/310) Add `client_credentials` grant_type automatically to clients registered by oxd

- [#308](https://github.com/GluuFederation/oxd/issues/308) GG request : add `authorization_redirect_uri` parameter support to `/get-authorization-url` command

- [#305](https://github.com/GluuFederation/oxd/issues/305) Return id_token's claims as is in id_token_claims

- [#269](https://github.com/GluuFederation/oxd/issues/269) BUG: Getting error in introspect-access-token

- [#261](https://github.com/GluuFederation/oxd/issues/261) Return client_name in register_site response

- [#233](https://github.com/GluuFederation/oxd/issues/233) Provide swagger based tests (copy of existing tests but based on swagger generated client)

- [#228](https://github.com/GluuFederation/oxd/issues/228) Bug : Swagger client returns relative timestamps instead of number of seconds 
since January 1 1970 UTC

- [#225](https://github.com/GluuFederation/oxd/issues/225) Drop "status" from protocol for all commands that was used by sockets. In REST it is covered by HTTP status.

- [#222](https://github.com/GluuFederation/oxd/issues/222) Remove license protection in all oxd released branches up to 2.4.4

- [#220](https://github.com/GluuFederation/oxd/issues/220) Revise /register-site and /setup-client and check whether we can stick with one single command for registration.

- [#217](https://github.com/GluuFederation/oxd/issues/217) During registration we should pass list of post_logout_redirect_uris instead of single value

- [#209](https://github.com/GluuFederation/oxd/issues/209) oxd has to catch invalid scope expressions during resource registration

- [#196](https://github.com/GluuFederation/oxd/issues/196) Deploy beta oxd-server 3.2.0 and swagger-ui to gluu.org server.

- [#183](https://github.com/GluuFederation/oxd/issues/183) Drop jackson 1.x from oxd when oxauth is migrated to jackson 2.x

- [#181](https://github.com/GluuFederation/oxd/issues/181) Introduce swagger 2.0 to oxd

- [#180](https://github.com/GluuFederation/oxd/issues/180) Client gets deleted from oxd-server after update_site command

- [#155](https://github.com/GluuFederation/oxd/issues/155) added better error handling if pre-registered client is added without client_secret

- [#141](https://github.com/GluuFederation/oxd/issues/141) Remove oxd_id from setup_client and keep setup_client just for protection access token

- [#130](https://github.com/GluuFederation/oxd/issues/130) Enable client to set custom state value
