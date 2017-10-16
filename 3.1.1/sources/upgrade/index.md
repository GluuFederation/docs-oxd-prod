## Overview

The latest release of oxd-server `3.1.1` uses configurable data storage (`h2`, `redis`, etc.) to store data files. If you are using a previous versions of oxd-server (`3.0.1` or `2.4.4`) the json files will have to be transfered to the new database.  To simplify the data transfer process we have included auto-migration capability in oxd-server `3.1.1`.  
 
 
## OpenID Connect 
To migrate json files from oxd-server (`3.0.1` or `2.4.4`) to oxd-server `3.1.1`, update the `oxd-conf.json` file by modifying the property `migration_source_folder_path`. The path should point to the folder/directory that contains the json files.  The files will be read and imported after you restart `oxd-server`. Data migration will only happen once and will not initiate for subsequent oxd-server restarts.  

!!! Note: 
    If you are using Windows OS don't forget to include the escape path separator (e.g. `C:\\OXD_OLD\\oxd-server\\conf`)

## UMA 

Auto-migration between UMA `1.0.1` and UMA `2.0` is not supported because of major changes between specifications. To view the UMA `2.0` specifications follow this [link](https://docs.kantarainitiative.org/uma/ed/uma-core-2.0-01.html#without-rpt).
 
