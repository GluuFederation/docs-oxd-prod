# oxd-server Upgrade
 
Previous versions of oxd-server (`3.0.1`, `2.4.4`) used json files for storing data. 
In `3.1.1` version `oxd-server` has configurable storage (e.g. `h2`, `redis`). 
If you have previous version of `oxd-server` and wish to move those data to `3.1.1` data you can activate auto-migration.
 
 
## OpenID Connect data migration  
`oxd-server` has built-in migration from older version of `oxd-server`. 
To migrate old json files from previous versions please specify path to folder/directory that contains those json files
 in `migration_source_folder_path` property of `oxd-conf.json` file. 
 Those files will be read and imported one time (during restart `oxd-server` will not import them again). 
Note, if you are under Windows OS don't forget to escape path separator, e.g. `C:\\OXD_OLD\\oxd-server\\conf`


## UMA Migration

`3.1.1` version supports UMA 2.0. In earlier version of `oxd-server` we had UMA `1.0.1`. 
It means that auto-migration is not going to help here.  


 
 
