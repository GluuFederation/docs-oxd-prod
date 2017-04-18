# oxd-spring
The following documentation shows how to configure Java Spring apps to use oxd for authentication. 

## Requirements
The oxd-spring requires the oxD Server. Please use the following link to install it

* [oxd Server Installation Guide](https://oxd.gluu.org/docs/install/)


## Install oxd-spring
Clone oxd-spring from Github repo and run maven command to install it:
```
git clone https://github.com/GluuFederation/oxd-spring.git
cd oxd-spring 
mvn clean package -Dmaven.test.skip=true
```

Now you can run the executable jar:
```
java -jar target/oxd-spring-0.0.1-SNAPSHOT.jar
```

Point browser to `https://127.0.0.1:8443/`. And log in into ce-dev2.gluu.org using test credentials: test_user/test_user_password 

***Note:*** oxd-server must run on *localhost* and be bound to port: *8099*, otherwise you'll need to configure `oxd-spring/src/main/resources/application.properties` file.

## Customize oxd-spring
To use your own server as openid provider you need to modify `oxd.server.op-host` property from `oxd-spring/src/main/resources/application.properties`, e.g:

```
oxd.server.op-host=https://gluu.localhost.info
```

Make sure the server already installed on your machine, or you can follow 
[this](https://gluu.org/docs/2.4.4/installation-guide/install/) guide to install it.
