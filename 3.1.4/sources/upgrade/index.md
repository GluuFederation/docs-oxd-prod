## Overview

The latest version of oxd includes auto-migration functionality to easily transfer your data files from an old server to a new server. 

Instead of JSON files, oxd 3.1.4 now uses configurable data stores such as `h2`, `redis`, etc. You can configure a new instance of oxd 3.1.4 to use storage with data filled by previous installation (e.g. `h2`, `redis`). 

If your existing oxd server uses JSON files for storage, point the `migration_source_folder_path` property in the configuration file of the new installation to the location where the files are located. Then start the new `oxd-server`. 

## Legacy Compatibility
Before moving forward with an upgrade to oxd 3.1.4, review the following legacy compatibility notes:

- UMA 2.0: Supported in oxd 3.1.4 and Gluu Server 3.1.4      
- UMA 1.0.1: **Not** supported in oxd 3.1.4 or Gluu Server 3.1.4    
- OpenID Connect: Supported in all versions of oxd and Gluu Server         

## Upgrade

Follow these steps to upgrade oxd server:

1. Back up `oxd-server` with data 

    - if `oxd-server` version is up to `3.0.2` (inclusively) - back up json files located in working directory of `oxd-server`
    - if `oxd-server` version is `3.1.0` or later - back up h2 database file `oxd_db.mv.db` or in case of redis follow redis instructions how to back up data.
  
2. Stop your current `oxd-server` and uninstall it.
3. Install `oxd-server` 3.1.4   
4. Open `/etc/oxd/oxd-server/oxd-conf.json`  
5. Modify it:

    - if `oxd-server` version is up to `3.0.2` (inclusively) - Modify `migration_source_folder_path` to point to the folder or directory that contains the JSON files
    - if `oxd-server` version is `3.1.0` or later : configure h2 or redis as shown below.
  
6. Start `oxd-server` as usual 

**Snippet from oxd-conf.json for H2**     
```json    
    "storage":"h2",
    "storage_configuration": {
        "dbFileLocation":"/opt/oxd-server/data/oxd_db"
    }    
```

**Snippet from oxd-conf.json for redis**
```json
  "storage":"redis",
  "storage_configuration": {
    "host":"localhost",
    "port":6379
  }
```


!!! Note    
    If you are using Windows OS, don't forget to include the escape path separator (e.g. `C:\\OXD_OLD\\oxd-server\\conf`)

- Restart `oxd-server` to import the files
- Data migration will only happen once and will not initiate for subsequent oxd-server restarts  

## UMA 
Auto-migration between UMA `1.0.1` and UMA `2` is **not** supported due to major changes between specifications. To view the UMA `2` specifications follow this [link](https://docs.kantarainitiative.org/uma/ed/uma-core-2.0-01.html#without-rpt).
