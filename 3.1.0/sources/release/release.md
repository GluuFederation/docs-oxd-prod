# Release Notes

This document contains information on oxd server features, 
operating system support, installation considerations, known issues, and fixes.

## New Features in oxd
- UMA is upgraded to UMA2 for enhanced security features
## Changes to Existing Features (Enhancements)
- UMA RP has new commands
    - UMA RP Get RPT has been enhanced to accept `claims_token` and `claims_token_format`
    - UMA RP - Get Claims-Gathering URL, has new new parameter `ticket`, which is obtained from 
      `need_info` error
    - UMA Authorize RPT: is REMOVED,(uma_rp_authorize_rpt ) is no longer supported.
    - register_site and setup_client commands: These commands has new parameter `claims_redirect_uri`
    
## System Requirements
## Installation and upgrade considerations
## General Considerations
## Known Issues
## Defects Fixed in oxd 3.1.0
