## What's New in Version 4.2

oxd 4.2 includes architectural changes as well as different bug fixes and improvements:

### New Features

### Fixes / Enhancements

- [#464](https://github.com/GluuFederation/oxd/issues/464) Make `Bearer` case insensitive in oxd

- [#454](https://github.com/GluuFederation/oxd/issues/454) Verify the `at_hash` presence in the ID_token for "id_token token" (Implicit) and "code id_token token" (hybrid) flow.

- [#453](https://github.com/GluuFederation/oxd/issues/453) Verify the `c_hash` presence in the returned ID token for "code id_token" and "code id_token token" hybrid flow.

- [#451](https://github.com/GluuFederation/oxd/issues/451) Fix client registration request where response_types sent in ["code", "code id_token", "code token"] format instead of ["code", "id_token", "token"].

- [#449](https://github.com/GluuFederation/oxd/issues/449) Adding `nonce` request parameter to explicitly pass `nonce` value to Authentication Request.

- [#442](https://github.com/GluuFederation/oxd/issues/442) If kid is absent in ID_TOKEN header then use the matching key out of the Issuer's published set

- [#441](https://github.com/GluuFederation/oxd/issues/441) Identify the invalid 'sub' value and reject the UserInfo Response.

- [#440](https://github.com/GluuFederation/oxd/issues/440) Identify the missing 'sub' value and reject the ID Token.

- [#439](https://github.com/GluuFederation/oxd/issues/439) Accept the ID Token after doing ID Token validation when `id_token_signed_response_alg` is `none`

- [#438](https://github.com/GluuFederation/oxd/issues/438) If 'iat' value is missing from `ID_TOKEN` then `ID_TOKEN` should be rejected during token validation.

- [#430](https://github.com/GluuFederation/oxd/issues/430) Add support for JDBC connection to be able connect to any RDBMS

- [#423](https://github.com/GluuFederation/oxd/issues/423) Fix oxd after httpclient upgrade in oxauth

- [#422](https://github.com/GluuFederation/oxd/issues/422) Upgrade oxd to use gluu-core-bom (same as oxauth)

- [#409](https://github.com/GluuFederation/oxd/issues/409) Add spontaneous scopes to oxd

- [#403](https://github.com/GluuFederation/oxd/issues/403) Introduce `Builder` for `Validator` and remove `JwsSignerObject`

- [#402](https://github.com/GluuFederation/oxd/issues/402) Rename `site -> rp` except persistence.

- [#400](https://github.com/GluuFederation/oxd/issues/400) Check and add to validation missed steps if identified

- [#396](https://github.com/GluuFederation/oxd/issues/396) Upgrade `Dropwizard` dependency from version `1.3.1` to `2.0.0` in oxd

- [#390](https://github.com/GluuFederation/oxd/issues/390) Sync client from OP : Update oxd database by reading client

- [#389](https://github.com/GluuFederation/oxd/issues/389) HA : RpService should cache Rp object for configurable amount of time (not indefinitely).

- [#388](https://github.com/GluuFederation/oxd/issues/388) Make h2 database username/password connection details configurable in yml file

- [#387](https://github.com/GluuFederation/oxd/issues/387) `StateService` keeps state and nonce in-memory which prevents HA of oxd.

- [#384](https://github.com/GluuFederation/oxd/issues/384) Remove ability to set/update Pre-Authorization flag from oxd

- [#381](https://github.com/GluuFederation/oxd/issues/381) Refactor `/register-site` operation code

- [#379](https://github.com/GluuFederation/oxd/issues/379) Incorrect scopes are added when client is updated using `/update-site` command

- [#364](https://github.com/GluuFederation/oxd/issues/364) Add support for proxy configuration

- [#363](https://github.com/GluuFederation/oxd/issues/363) Introduce new `/uma-rs-modify` command to be able modify existing resource

- [#362](https://github.com/GluuFederation/oxd/issues/362) We need `scopes` explicitly passed into `/uma-rs-check-access` to have granular access handling

- [#210](https://github.com/GluuFederation/oxd/issues/210) Introduce ability to lock oxd to list of specific IDPs

- [#195](https://github.com/GluuFederation/oxd/issues/195) Migrate to swagger 3.0 once swagger-codegen has stable release

- [#182](https://github.com/GluuFederation/oxd/issues/182) Add tracing metrics to oxd server

- [#165](https://github.com/GluuFederation/oxd/issues/165) UMA : add creation and expiration resource support to oxd

- [#162](https://github.com/GluuFederation/oxd/issues/162) Add description and oxdID to client metadata

- [#158](https://github.com/GluuFederation/oxd/issues/158) change op_host config param to "op_discovery_uri"

- [#128](https://github.com/GluuFederation/oxd/issues/128) Windows setup file needed for oxd service

- [#114](https://github.com/GluuFederation/oxd/issues/114) Hybrid flow : add ability to set response_type directly during authorization url request

- [#91](https://github.com/GluuFederation/oxd/issues/91) UMA 2 : add custom redirect parameters to get_claims_gathering_url command
