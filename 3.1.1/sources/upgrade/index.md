## Overview

If you are upgrading oxd-server to the latest version `3.1.1` we have included an auto-migration script to easily transfer your data files.  oxd-server `3.1.1` now uses configurable data storage (`h2`, `redis`, etc.) opposed to JSON files.   


## OpenID Connect 
Follow these simple steps to migrate your JSON files:
- Open `oxd-conf.json` 
- Modify `migration_source_folder_path` to point to the folder or directory that contains the JSON files

!!! Note: 
    If you are using Windows OS don't forget to include the escape path separator (e.g. `C:\\OXD_OLD\\oxd-server\\conf`)

- Restart `oxd-server` to import the files
- Data migration will only happen once and will not initiate for subsequent oxd-server restarts.  

## UMA 
Auto-migration between UMA `1.0.1` and UMA `2` is not supported because of major changes between specifications. To view the UMA `2` specifications follow this [link](https://docs.kantarainitiative.org/uma/ed/uma-core-2.0-01.html#without-rpt).
 
