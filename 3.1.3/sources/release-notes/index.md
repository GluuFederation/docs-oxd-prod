## What's New in Version 3.1.3

### New Features

- [#177](https://github.com/GluuFederation/oxd/issues/177) Stopped update-site from resetting scopes if new ones not provided

- [#175](https://github.com/GluuFederation/oxd/issues/175) Added expired client clean-up

- [#174](https://github.com/GluuFederation/oxd/issues/174) Provides verbose error message if oxd fails to send statistic to license server

- [#169](https://github.com/GluuFederation/oxd/issues/169) Added Swagger HTTPS extension

- [#167](https://github.com/GluuFederation/oxd/issues/167) Added ability to update existing UMA resources

- [#166](https://github.com/GluuFederation/oxd/issues/166) setup_client command now returns oxd_id client_id for use with gateway

- [#164](https://github.com/GluuFederation/oxd/issues/164) Expanded init.d script for HTTPS extensions to increase functionality

- [#159](https://github.com/GluuFederation/oxd/issues/159) Moved database files to /db folder

### Fixes

- [#173](https://github.com/GluuFederation/oxd/issues/173) bad UmaPermission object leads to deserialization error

- [#172](https://github.com/GluuFederation/oxd/issues/172) uma_rs_protect does not properly clean up local storage on second run

- [#171](https://github.com/GluuFederation/oxd/issues/171) Internal error for incorrect command parameters with HTTPS extensions

- [#170](https://github.com/GluuFederation/oxd/issues/170) Deleted UMA resources still accessible

- [#168](https://github.com/GluuFederation/oxd/issues/168) oxd server works with non-zero exit code



