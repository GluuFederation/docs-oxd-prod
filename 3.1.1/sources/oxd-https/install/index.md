#oxd-https-extension Installation

## Manual installation

* Download `oxd-https-extension` jar file from [maven repo](http://ox.gluu.org/maven/org/xdi/oxd-https-extension/3.1.1-SNAPSHOT/)
* Configure the server in `oxd-https.yml` file in the same directory. Configuration explained [here](https://gluu.org/docs/oxd/3.1.1/oxd-https/configuration/)
* Make sure `oxd-https.keystore` file exists and properly referenced in `oxd-https.yml` file.
* Run application with following command

```
java -jar oxd-https-extension-3.1.1-SNAPSHOT.jar server oxd-https.yml
```