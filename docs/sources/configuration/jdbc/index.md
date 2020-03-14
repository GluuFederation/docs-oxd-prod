# Using RDBMS storage in oxd

oxd can be configured with any RDBMS so that it can save data in own persistence. The RDMS storage configuration with `mysql` and `postgres` will be demonstrate in this section.

## Mysql and Postgres configuration in oxd

To use `Mysql` database as storage in oxd-server we need to follow below step:

- In `/opt/oxd-server/conf/oxd-server.yml` file of installed oxd-server set following parameters.

    ```
    storage: jdbc
    storage_configuration
      driver: com.mysql.jdbc.Driver
      jdbcUrl: jdbc:mysql://<hostname>:3306/oxd_database
      username: oxd_username
      password: oxd_password
    ```

