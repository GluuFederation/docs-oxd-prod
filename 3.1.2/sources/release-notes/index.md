## What's New in Version 3.1.2

### New Features

- [#145](https://github.com/GluuFederation/oxd/issues/145) Backwards compatibility

- [#144](https://github.com/GluuFederation/oxd/issues/144) `get-client-token` command now supports `private_key_jwt` authentication method

- [#143](https://github.com/GluuFederation/oxd/issues/143) new token introspection command

- [#156](https://github.com/GluuFederation/oxd/issues/156) RPT introspection is now backwards compatible (same as for OAuth introspection)

- [#154](https://github.com/GluuFederation/oxd/issues/154) add client_id to RPT introspection (oxd-server, oxd-https-extension and java client)

- [#148](https://github.com/GluuFederation/oxd/issues/148) oxd has to detect that downloaded license does not correspond to license id in oxd-conf.json and clean up automatically

- [#138](https://github.com/GluuFederation/oxd/issues/138) UMA resource registration : http method must be mentioned one time within one path otherwise operation must fail

- [#137](https://github.com/GluuFederation/oxd/issues/137) if AS lost the PAT oxd has to recognize it and refresh the token

- [#120](https://github.com/GluuFederation/oxd/issues/120) oxLicense needs to return date and active/inactive

- [#119](https://github.com/GluuFederation/oxd/issues/119) Add ability to remove client from oxd

- [#116](https://github.com/GluuFederation/oxd/issues/116) UMA 2 : support "and", "or" logical operations for scopes (including nested)

### Fixes

- [#163](https://github.com/GluuFederation/oxd/issues/163) No log files are generated for oxd-server 3.1.2 and Windows OS

- [#161](https://github.com/GluuFederation/oxd/issues/161) There are many duplicate files created in oxd repo by Adrian. We must not produce duplicates in source code.

- [#153](https://github.com/GluuFederation/oxd/issues/153) Build oxd-server with structure mentioned in pakage issues

- [#149](https://github.com/GluuFederation/oxd/issues/149) oxd-server service should run oxd with user privileges instead of root

- [#147](https://github.com/GluuFederation/oxd/issues/147) Move config to /etc and pid to /var/run

- [#146](https://github.com/GluuFederation/oxd/issues/146) uma_rs_check_access throws invalid_scope error

- [#142](https://github.com/GluuFederation/oxd/issues/142) Bug - after oxd_id removing it is still operational

- [#139](https://github.com/GluuFederation/oxd/issues/139) Do not register claims interaction endpoint as redirect_uri if hosts are different

- [#135](https://github.com/GluuFederation/oxd/issues/135) https-extension must not listen http port

- [#132](https://github.com/GluuFederation/oxd/issues/132) Permission ticket registration fails

- [#131](https://github.com/GluuFederation/oxd/issues/131) create clear error message for the case when AS did not return back client_id on intorspection

- [#129](https://github.com/GluuFederation/oxd/issues/129) Unify `update_site_registration` and /update-site on https-extension

- [#127](https://github.com/GluuFederation/oxd/issues/127) `update_site` command sometimes fails if setup_client is using

- [#125](https://github.com/GluuFederation/oxd/issues/125) Remove values from oxd-default-site-config.json

- [#124](https://github.com/GluuFederation/oxd/issues/124) oxd (in manual install) trying to read default-config file from inexisting location

- [#123](https://github.com/GluuFederation/oxd/issues/123) oxAuthRedirectURI ignored after site update step

- [#98](https://github.com/GluuFederation/oxd/issues/98) Add tests for commands

