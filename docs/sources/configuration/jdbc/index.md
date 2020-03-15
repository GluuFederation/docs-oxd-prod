# Using RDBMS storage in oxd

oxd can be configured with any RDBMS for saving data in own persistence. The RDMS configuration in oxd is same for all relational database.  It has been successfully tested with `Mysql` and `Postgres` database and configuration of these two database will be discussed in this section.

## Mysql and Postgres configuration in oxd

To use `Mysql` or `Postgres` database as storage in oxd-server we need to configure following fields in `/opt/oxd-server/conf/oxd-server.yml` file of installed oxd-server:

- **storage** set this field `jdbc` to configure any relational database to oxd.

### storage_configuration Field Descriptions

- **driver** set driver class name of the choosen database. Example `com.mysql.jdbc.Driver` is the driver class for `Mysql` database.

- **jdbcUrl** set jdbc url of the database

- **username** set database username

- **password** set database password

Mysql storage configuration sample:

    ```
    storage: jdbc
    storage_configuration
      driver: com.mysql.jdbc.Driver
      jdbcUrl: jdbc:mysql://<hostname>:3306/<database_name>
      username: oxd_username
      password: oxd_password
    ```

Postgres storage configuration sample:

    ```
    driver: org.postgresql.Driver
    jdbcUrl: jdbc:postgresql://<hostname>:5432/<database_name>
    username: oxd_username
    password: oxd_password
    ```
