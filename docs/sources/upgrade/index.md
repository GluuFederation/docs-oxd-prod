## Overview

If you are upgrading `oxd-server` to the latest version, we have included auto-migration functionality to easily transfer your data files. `oxd-server` now uses configurable data storage (`h2`, `redis`, etc.) instead of JSON files.

## Legacy Compatibility
Before moving forward with an upgrade to oxd 3.1.4, review the following legacy compatibility notes:

- UMA 2.0: Supported in oxd 3.1.4 and Gluu Server 3.1.4      
- UMA 1.0.1: **Not** supported in oxd 3.1.4 or Gluu Server 3.1.4    
- OpenID Connect: Supported in all versions of oxd and Gluu Server         

## OpenID Connect 
Follow these simple steps to migrate your JSON files:

- Open `oxd-conf.json` 
- Modify `migration_source_folder_path` to point to the folder or directory that contains the JSON files

!!! Note 
    If you are using Windows OS, don't forget to include the escape path separator (e.g. `C:\\OXD_OLD\\oxd-server\\conf`)

- Restart `oxd-server` to import the files
- Data migration will only happen once and will not initiate for subsequent oxd-server restarts  

## UMA 
Auto-migration between UMA `1.0.1` and UMA `2` is not supported because of major changes between the specifications. To view the UMA `2` specifications follow this [link](https://docs.kantarainitiative.org/uma/ed/uma-core-2.0-01.html#without-rpt).

## Using the Upgrade Script

Download the upgrade script and yaml template:

```
# wget https://raw.githubusercontent.com/GluuFederation/oxd/version_4.0/upgrade/oxd-server.yml.temp
# wget https://raw.githubusercontent.com/GluuFederation/oxd/version_4.0/upgrade/oxd_updater.py
```

Install the python yaml module:

```
# pip install pyyaml
```

Run theupgrade script:

```
# python oxd_updater.py
```

The `oxd_updater.py` script:
  1. moves json data to `/opt/oxd-server/json_data_backup` and sets 
    `migration_source_folder_path: /opt/oxd-server/json_data_backup` in `oxd-server.yml`
    so that oxd-server migrates to h2 database
  1. merges `oxd-conf.json`, `oxd-default-site-config.json`, and `log4j.xml` into new configuration file `oxd-server.yml`
  
