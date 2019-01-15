## Overview

The latest version of oxd includes auto-migration functionality to easily transfer your data files from an old server to a new server. 

Instead of JSON files, oxd 3.1.4 now uses configurable data stores such as `h2`, `redis`, etc. You can configure a new instance of oxd 3.1.4 to use storage with data filled by previous installation (e.g. `h2`, `redis`). 

If your existing oxd server uses JSON files for storage, point the `migration_source_folder_path` property in the configuration file of the new installation to the location where the files are located. Then start the new `oxd-server`. 

## Legacy Compatibility
Before moving forward with an upgrade to oxd 3.1.4, review the following legacy compatibility notes:

- UMA 2.0: Supported in oxd 3.1.4 and Gluu Server 3.1.4      
- UMA 1.0.1: **Not** supported in oxd 3.1.4 or Gluu Server 3.1.4    
- OpenID Connect: Supported in all versions of oxd and Gluu Server         

## OpenID Connect 
Follow these steps to migrate your JSON files:

- Open `oxd-conf.json` 
- Modify `migration_source_folder_path` to point to the folder or directory that contains the JSON files

!!! Note: 
    If you are using Windows OS, don't forget to include the escape path separator (e.g. `C:\\OXD_OLD\\oxd-server\\conf`)

- Restart `oxd-server` to import the files
- Data migration will only happen once and will not initiate for subsequent oxd-server restarts  

## UMA 
Auto-migration between UMA `1.0.1` and UMA `2` is **not** supported due to major changes between specifications. To view the UMA `2` specifications follow this [link](https://docs.kantarainitiative.org/uma/ed/uma-core-2.0-01.html#without-rpt).
