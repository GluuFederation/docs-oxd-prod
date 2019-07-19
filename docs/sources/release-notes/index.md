## What's New in Version 4.0

oxd 4.0 includes architectural changes as well as different bug fixes and improvements:

 - introduced swagger 2.0 to oxd (we are planning to move to 3.0 when swagger codegen is ready)
 - socket transport is removed from oxd-server
 - https transport is made as main transport for oxd-server
 - oxd-https-extension module is completely removed
 - all configuration is now in one single yaml file, [see sample oxd-server.yml](https://github.com/GluuFederation/oxd/blob/version_4.0.beta/oxd-server/src/main/resources/oxd-server.yml)
 - new tests set-up and tear-down based on dropwizard
 - Upgraded dropwizard to latest stable 1.3.1 version
 - Changed oxd commands runner to avoid additional serialization/deserialization which improves performance.

### New Features

### Fixes / Enhancements

- [#347](https://github.com/GluuFederation/oxd/issues/347) Do not use PAT to request introspection (avoid exception related to missed `uma_protection` )

- [#342](https://github.com/GluuFederation/oxd/issues/342) Bug : we got `post_logout_redirect_uris` included into `redirect_uris`

- [#338](https://github.com/GluuFederation/oxd/issues/338) add `idTokenSignedResponseAlg` to `/register-site` command

- [#337](https://github.com/GluuFederation/oxd/issues/337) oxd has to use newest `setScope` oxauth-client method otherwise oxauth falls back to all default scopes

- [#334](https://github.com/GluuFederation/oxd/issues/334) Add `params` map to `/get-authorization-url` api

- [#332](https://github.com/GluuFederation/oxd/issues/332) oxd-4.0: Remove redundant  Logger-specific levels (`Trace`) from oxd-server.yml

- [#331](https://github.com/GluuFederation/oxd/issues/331) post_logout_redirect_uri is left empty when registering client via oxd

- [#329](https://github.com/GluuFederation/oxd/issues/329) oxd: Getting Forbidden : 403 while using lsox script

- [#323](https://github.com/GluuFederation/oxd/issues/323) Fix and enable back two tests which are failing against ce-dev5.

- [#320](https://github.com/GluuFederation/oxd/issues/320) Removed confusing PAT abbr from code and docs

- [#317](https://github.com/GluuFederation/oxd/issues/317) Fix oxd after oxauth is migrated to `org.json` from jettison. Build failed right now.

- [#311](https://github.com/GluuFederation/oxd/issues/311) Gluu-3.1.6-oxd-server fails to start after restarting VM

- [#310](https://github.com/GluuFederation/oxd/issues/310) Add `client_credentials` grant_type automatically to clients registered by oxd

- [#308](https://github.com/GluuFederation/oxd/issues/308) GG request : add `authorization_redirect_uri` parameter support to `/get-authorization-url` command

- [#307](https://github.com/GluuFederation/oxd/issues/307) NEW : Swagger spec must reflect error json returned by oxd

- [#306](https://github.com/GluuFederation/oxd/issues/306) Switch oxd 4.0 to oxauth-client 4.0 (from current 3.1.5)

- [#305](https://github.com/GluuFederation/oxd/issues/305) Return id_token's claims as is in id_token_claims

- [#292](https://github.com/GluuFederation/oxd/issues/292) oxd 4.0 fails if introspection response is customized on CE by interception script

- [#279](https://github.com/GluuFederation/oxd/issues/279) Internal Server Error (500) while making get-tokens-by-code call

- [#276](https://github.com/GluuFederation/oxd/issues/276) ValueError in output from 3.1.4-4.0beta upgrade

- [#274](https://github.com/GluuFederation/oxd/issues/274) UnknownHostException at get-client-token call

- [#272](https://github.com/GluuFederation/oxd/issues/272) Improve log message if certificate is not imported

- [#269](https://github.com/GluuFederation/oxd/issues/269) BUG: Getting error in introspect-access-token

- [#262](https://github.com/GluuFederation/oxd/issues/262) Create swagger based test for access_token as JWT

- [#261](https://github.com/GluuFederation/oxd/issues/261) Return client_name in register_site response

- [#258](https://github.com/GluuFederation/oxd/issues/258) Create new `verify-jwt` command (required by GG)

- [#245](https://github.com/GluuFederation/oxd/issues/245) invalid_id_token_unknown in get-tokens-by-code oxd command

- [#244](https://github.com/GluuFederation/oxd/issues/244) Change package name to `oxd-server-4.0` with upgrade script

- [#236](https://github.com/GluuFederation/oxd/issues/236) Change exception message for introspect unmatched client from ValidationService

- [#233](https://github.com/GluuFederation/oxd/issues/233) Provide swagger based tests (copy of existing tests but based on swagger generated client)

- [#228](https://github.com/GluuFederation/oxd/issues/228) Bug : Swagger client returns relative timestamps instead of number of seconds 
since January 1 1970 UTC

- [#225](https://github.com/GluuFederation/oxd/issues/225) Drop "status" from protocol for all commands that was used by sockets. In REST it is covered by HTTP status.

- [#224](https://github.com/GluuFederation/oxd/issues/224) If wrong oxd_id is provided REST service should return 404

- [#222](https://github.com/GluuFederation/oxd/issues/222) Remove license protection in all oxd released branches up to 2.4.4

- [#220](https://github.com/GluuFederation/oxd/issues/220) Revise /register-site and /setup-client and check whether we can stick with one single command for registration.

- [#219](https://github.com/GluuFederation/oxd/issues/219) Add `client_secret_expires_at` property to `setup_client` and `register_site` commands

- [#217](https://github.com/GluuFederation/oxd/issues/217) During registration we should pass list of post_logout_redirect_uris instead of single value

- [#209](https://github.com/GluuFederation/oxd/issues/209) oxd has to catch invalid scope expressions during resource registration

- [#198](https://github.com/GluuFederation/oxd/issues/198) Add auto extend client expiration to oxd-server

- [#196](https://github.com/GluuFederation/oxd/issues/196) Deploy beta oxd-server 3.2.0 and swagger-ui to gluu.org server.

- [#183](https://github.com/GluuFederation/oxd/issues/183) Drop jackson 1.x from oxd when oxauth is migrated to jackson 2.x

- [#181](https://github.com/GluuFederation/oxd/issues/181) Introduce swagger 2.0 to oxd

- [#180](https://github.com/GluuFederation/oxd/issues/180) Client gets deleted from oxd-server after update_site command

- [#176](https://github.com/GluuFederation/oxd/issues/176) Investigate front-end API with GraphQL

- [#155](https://github.com/GluuFederation/oxd/issues/155) added better error handling if pre-registered client is added without client_secret

- [#152](https://github.com/GluuFederation/oxd/issues/152) it should be possible to uma-rp-check-access with any oxd_id

- [#141](https://github.com/GluuFederation/oxd/issues/141) Remove oxd_id from setup_client and keep setup_client just for protection access token

- [#136](https://github.com/GluuFederation/oxd/issues/136) CLI to print configuration

- [#130](https://github.com/GluuFederation/oxd/issues/130) Enable client to set custom state value

- [#122](https://github.com/GluuFederation/oxd/issues/122) oxd-https resources / packaging

- [#118](https://github.com/GluuFederation/oxd/issues/118) dropwizard fails with jdk9

- [#117](https://github.com/GluuFederation/oxd/issues/117) Connect : add explicit introspection operation to validate access_token

- [#112](https://github.com/GluuFederation/oxd/issues/112) Change org.xdi -> org.gluu

- [#108](https://github.com/GluuFederation/oxd/issues/108) Java Exception when Client is expired

- [#95](https://github.com/GluuFederation/oxd/issues/95) oxd-https-extension : provide automatic test triggered by jenkins for the project

- [#76](https://github.com/GluuFederation/oxd/issues/76) Sometimes if license details are changed license is not correctly update
