# oxd-https-extension 
The `oxd-https-extension` is a RESTful, Jetty-based server which accepts `HTTPS` calls and redirects them to the `oxd-server`. With the https extension enabled, many apps across many servers can leverage one central oxd service over the web.  

## Start oxd-https-extension
After the oxd package has been installed, and the `oxd-server` has been configured and started, start the `oxd-https-extension` service by executing the following command:

```
/etc/init.d/oxd-https-extension start
```

To stop the `oxd-https-extension`, run:

```
/etc/init.d/oxd-https-extension stop
```

After starting `oxd-https-extension`, move on to [configuration](../configuration/index.md).  

## Manual Installation

For manual installation `oxd-server` and `oxd-https-extension` have to be installed separately.

Install `oxd-server` manually as described [here](https://gluu.org/docs/oxd/3.1.1/install/#manual-installation).

Install `oxd-https-extension` following these steps:

- Download `oxd-https-extension` jar file from [maven repo](http://ox.gluu.org/maven/org/xdi/oxd-https-extension/3.1.2.Final/)
- Configure the server in `oxd-https.yml` file in the same directory. Configuration explained [here](../configuration/index.md)
- Make sure `oxd-https.keystore` file exists and properly referenced in `oxd-https.yml` file.
- Run the application with following command:

```
java -jar oxd-https-extension-3.1.1.Final.jar server oxd-https.yml
```
