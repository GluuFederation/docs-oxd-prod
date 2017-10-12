# oxd-server Upgrade
 
 
 * migration_source_folder_path - `oxd-server` has built-in migration from older version of `oxd-server` (previously called `oxd-server`). To migrate old json files from previous versions please specify path to folder/directory that contains those json files in this property. Those files will be read and imported one time (during restart `oxd-server` will not import them again). Note, if you are under Windows OS don't forget to escape path separator, e.g. `C:\\OXD_OLD\\oxd-server\\conf`