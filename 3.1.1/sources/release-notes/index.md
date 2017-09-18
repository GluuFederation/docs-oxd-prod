# Notice

This document, also known as the Gluu Release Note, 
relates to the Gluu Release versioned 3.1.0 The work is licensed under “The MIT License” 
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

The document is released with the Version 3.0.2 of the Gluu Software. The purpose of this document is to provide the changes made/new features included in this release of the Gluu Software. The list is not exhaustive and there might be some omission of negligible issues, but the noteworthy features, enhancements and fixes are covered. 

## Background

The Gluu Server is a free open source identity and access management (IAM) platform. The Gluu Server is a container distribution composed of software written by Gluu and incorporated from other open source projects. 

The most common use cases for the Gluu Server include single sign-on (SSO), mobile authentication, API access management, two-factor authentication, customer identity and access management (CIAM) and identity federation.

## Documentation

Please visit the [oxd Documentation Page](http://www.gluu.org/docs/oxd) for the complete 
documentation and administrative guide. 

## Components included in Gluu oxd 3.1.0


## What's new in version 3.1.0

### New Features

- Pass server metadata to license server (e.g. oxd server version)[#83](https://github.com/GluuFederation/oxd/issues/83)
- Update license to check number of oxd clients[#58](https://github.com/GluuFederation/oxd/issues/58)
- UMA 2 : Upgrade oxd [#49](https://github.com/GluuFederation/oxd/issues/49)
- GetAuthorizationUrl - allo to add custom parameters[#29](https://github.com/GluuFederation/oxd/issues/29)
- provide : Keys are published as a well-formed JWK Set[#7](https://github.com/GluuFederation/oxd/issues/7)
- Introduce server_name configuration [#82](https://github.com/GluuFederation/oxd/issues/82)
- Provide ability to have user authorization for UMA flow[#79](https://github.com/GluuFederation/oxd/issues/79)
- Cache / Retry OpenID Provider public signing key [#74](https://github.com/GluuFederation/oxd/issues/74)
- Persistance : introduce database persistence layer [#73](https://github.com/GluuFederation/oxd/issues/73)
- Introduce oxd-to-http security [#72](https://github.com/GluuFederation/oxd/issues/72)
- Support Hybrid Flow [#69](https://github.com/GluuFederation/oxd/issues/69)
- Support refresh token for get_user_info API call[#64](https://github.com/GluuFederation/oxd/issues/64)
- Support optional PATH for OpenID Connect Discovery[#59](https://github.com/GluuFederation/oxd/issues/59)
- Support connections via https [#67](https://github.com/GluuFederation/oxd/issues/67)

### Deprecated Features
Deprecated
- UMA 1.0.1 removed [#49](https://github.com/GluuFederation/oxd/issues/49) 

### Fixes
- Fix double logging messages which appears after using latest oxauth-client which is upgraded to log4j2[#84](https://github.com/GluuFederation/oxd/issues/84)
- Invalid setting throws generic error [#61](https://github.com/GluuFederation/oxd/issues/61)
- Check client update url for oxd 3.0.0 [#81](https://github.com/GluuFederation/oxd/issues/81)
- Associate register_site with access_token client [#80](https://github.com/GluuFederation/oxd/issues/80)
- Client session doesn't logout as client_logout_uri parameter is ignored in v3.0.1 [#71](https://github.com/GluuFederation/oxd/issues/71)
- Not acceptable error during UMA RS registration [#63](https://github.com/GluuFederation/oxd/issues/63)
- Authorization_redirect_uri not being updated during update_site_registration command[#86](https://github.com/GluuFederation/oxd/issues/86)
- Defective client registration when executing register site [#85](https://github.com/GluuFederation/oxd/issues/85)