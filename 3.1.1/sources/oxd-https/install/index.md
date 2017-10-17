# oxd-https-extension Installation

Please install oxd package as described [here](https://gluu.org/docs/oxd/3.1.1/install/).

After package is successfully installed please run `oxd-https-extension` with following command

```
/etc/init.d/oxd-https-extension start
```

To stop `oxd-https-extension` run

```
/etc/init.d/oxd-https-extension stop
```

## Manual installation

For manual installation `oxd-server` and `oxd-https-extension` have to be installed separately.

Install `oxd-server` manually as described [here](https://gluu.org/docs/oxd/3.1.1/install/#manual-installation).

Install `oxd-https-extension` following these steps:

* Download `oxd-https-extension` jar file from [maven repo](http://ox.gluu.org/maven/org/xdi/oxd-https-extension/3.1.1.Final/)
* Configure the server in `oxd-https.yml` file in the same directory. Configuration explained [here](https://gluu.org/docs/oxd/3.1.1/oxd-https/configuration/)
* Make sure `oxd-https.keystore` file exists and properly referenced in `oxd-https.yml` file.
* Run application with following command

```
java -jar oxd-https-extension-3.1.1.Final.jar server oxd-https.yml
```
