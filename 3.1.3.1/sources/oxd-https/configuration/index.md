# oxd-https-extension Configuration

`oxd-https-extension` uses the `dropwizard` framework. 

To configure `oxd-https-extension`, edit the following parameters, found in the `oxd-https.yml` file: 

## Parameters

- oxdHost: oxd-server host
- oxdPort: oxd-server port
- server: HTTP server configuration parameters. For a complete list of server related parameters click [here](http://www.dropwizard.io/0.9.1/docs/manual/configuration.html)

## Example

```
oxdHost: localhost
oxdPort: 8099

server:
  applicationConnectors:
    - type: https
      port: 8443
      keyStorePath: oxd-https.keystore
      keyStorePassword: example
      validateCerts: false
  adminConnectors:
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
