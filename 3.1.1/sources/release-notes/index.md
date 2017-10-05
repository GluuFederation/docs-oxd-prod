# Notice

This document, also known as the Gluu Release Note, 
relates to the Gluu Release versioned 3.1.1 The work is licensed under “The MIT License” 
allowing the use, copy, modify, merge, publish, distribute, sub-license and sale without 
limitation and liability. This document extends only to the aforementioned release version 
in the heading.

UNLESS IT HAS BEEN EXPRESSLY AGREED UPON BY ANY WRITTEN AGREEMENT BEFOREHAND, 
THE WORK/RELEASE IS PROVIDED “AS IS”, WITHOUT ANY WARRANTY OR GUARANTEE OF ANY KIND 
EXPRESS OR IMPLIED. UNDER NO CIRCUMSTANCE, THE AUTHOR, OR GLUU SHALL BE LIABLE FOR ANY 
CLAIMS OR DAMAGES CAUSED DIRECTLY OR INDIRECTLY TO ANY PROPERTY OR LIFE WHILE INSTALLING 
OR USING THE RELEASE.

## Overview

## Purpose

The document is released with the Version 3.1.1 of the Gluu Software. 
The purpose of this document is to provide the changes made/new features included in this 
release of the Gluu Software. The list is not exhaustive and there might be some omission 
of negligible issues, but the noteworthy features, enhancements and fixes are covered. 

## Background

The Gluu Server is a free open source identity and access management (IAM) platform. 
The Gluu Server is a container distribution composed of software written by Gluu and incorporated 
from other open source projects. 

The most common use cases for the Gluu Server include single sign-on (SSO), mobile authentication, API access management, two-factor authentication, customer identity and access management (CIAM) and identity federation.

## Documentation

Please visit the [oxd Documentation Page](http://www.gluu.org/docs/oxd) for the complete 
documentation and administrative guide. 

## Components included in Gluu oxd 3.1.1


## What's new in version 3.1.1

### New Features
- [#49](https://github.com/GluuFederation/oxd/issues/49) Introduced UMA 2 support
- Introduced h2 as persistence for oxd data
- [#89](https://github.com/GluuFederation/oxd/issues/89) Introduced redis as persistence for oxd data
    - Added support to different type of connection to redis: standalone (standalone redis server), sharded (client sharding), cluster (for redis cluster deployment)
- [#87](https://github.com/GluuFederation/oxd/issues/87) Auto-upgrade functionality. If run oxd 3.1.1 on old oxd data (3.0, 2.4.4) it automatically migrates it into new storage (via `migration_source_folder_path` in `oxd-conf.json`).
- [#29](https://github.com/GluuFederation/oxd/issues/29) Added ability to pass custom parameters in `get_authorization_url` command.
- [#64](https://github.com/GluuFederation/oxd/issues/64) Added new `get_access_token_by_refresh_token` command
- [#75](https://github.com/GluuFederation/oxd/issues/75) Introduced `oxd-https-extension` plugin which allows to work with `oxd-server` over HTTPS.
- Upgraded oxd 3.1.1 to oxauth 3.1.1

### Fixes
- [#61](https://github.com/GluuFederation/oxd/issues/61) Invalid setting throws generic error
- [#86](https://github.com/GluuFederation/oxd/issues/86) Authorization_redirect_uri not being updated during update_site_registration command
- [#81](https://github.com/GluuFederation/oxd/issues/81) Provided check for registration client url. If it is blank returned appropriate error code with verbose explanation.
- [#84](https://github.com/GluuFederation/oxd/issues/84) Fix double logging messages which appears after using latest oxauth-client which is upgraded to log4j2
- [#83](https://github.com/GluuFederation/oxd/issues/83) `server_name` is mandatory parameter in `oxd-conf.json`.
- [#78](https://github.com/GluuFederation/oxd/issues/78) Cannot register client scopes
- [#71](https://github.com/GluuFederation/oxd/issues/71) Client session doesn't logout as client_logout_uri parameter is ignored in v3.0.1 


### Deprecated Features
- [#49](https://github.com/GluuFederation/oxd/issues/49) Deprecated - UMA 1.0.1 removed 