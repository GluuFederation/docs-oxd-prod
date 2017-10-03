#oxd-https-extension Configuration

`oxd-https-extension` is configured by `oxd-https.yml` file.

Example:

```
oxdHost: localhost
oxdPort: 8099

server:
  applicationConnectors:
    - type: http
      port: 8080
    - type: https
      port: 8443
      keyStorePath: oxd-https.keystore
      keyStorePassword: example
      validateCerts: false
  adminConnectors:
    - type: http
      port: 8081
    - type: https
      port: 8444
      keyStorePath: oxd-https.keystore
      keyStorePassword: example
      validateCerts: false

  # Logging settings.
logging:

  # The default level of all loggers. Can be OFF, ERROR, WARN, INFO, DEBUG, TRACE, or ALL.
  level: INFO

  # Logger-specific levels.
  loggers:
    org.gluu.oxd: DEBUG
    org.xdi.oxd: DEBUG

  # Logback's Time Based Rolling Policy - archivedLogFilenamePattern: /tmp/application-%d{yyyy-MM-dd}.log.gz
  # Logback's Size and Time Based Rolling Policy -  archivedLogFilenamePattern: /tmp/application-%d{yyyy-MM-dd}-%i.log.gz
  # Logback's Fixed Window Rolling Policy -  archivedLogFilenamePattern: /tmp/application-%i.log.gz

  appenders:
    - type: console
    - type: file
      threshold: INFO
      logFormat: "%-6level [%d{HH:mm:ss.SSS}] [%t] %logger{5} - %X{code} %msg %n"
      currentLogFilename: /tmp/oxd-https.log
      archivedLogFilenamePattern: /tmp/oxd-https-%d{yyyy-MM-dd}-%i.log.gz
      archivedFileCount: 7
      timeZone: UTC
      maxFileSize: 10MB
```

* oxdHost - oxd-server host
* oxdPort - oxd-server port
* server - described HTTP server configuration. `oxd-https-extension` use `dropwizard` framework under the hood. Therefore all servers dropwizard configuration is available for `oxd-https-extension`. Please find complete list of server related parameters [here](http://www.dropwizard.io/0.9.1/docs/manual/configuration.html)
* logging - logging is configured based on `dropwizard` logging configuration, please find details [here](http://www.dropwizard.io/0.9.1/docs/manual/configuration.html#logging)