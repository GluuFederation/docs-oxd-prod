# oxd-spring <!-- intro -->

In this tutorial we demonstrate how a java spring web application can leverage oxd 4.0 for SSO using the authorization code flow.

## Prerequisites

1. Java 8+

    Install [Java Standard Edition](http://www.oracle.com/technetwork/java/javase/downloads/2133151) version 8 or higher.

1. Maven 3+

    Download [maven](https://maven.apache.org/download.cgi) and follow the simple installation instructions. Ensure the `bin` directory is added to your PATH.

1. An OpenID Connect Provider (OP), like the Gluu Server

    Learn how to deploy Gluu [in the docs](https://gluu.org/docs/ce/installation-guide/).

1. oxd-server 4.0

    Download and install [oxd-server 4.0](https://gluu.org/docs/oxd/4.0/). For the purposes of this demo app, built-in default configuration files will work.
    
## Run

1. Ensure that Gluu server and oxd-server are running and accessible.

1. Clone `oxd-spring` project from [Github](https://github.com/GluuFederation/oxd-spring.git) to your local disk

    If you have `git` installed, just open a console and run below command to clone the project.

    ```
    git clone https://github.com/GluuFederation/oxd-spring.git
    ```

    After the project is cloned switch to version_4.0 branch using below command.

    ```
    git checkout version_4.0
    ```
    
1. In the cloned `oxd-spring` project modify `${OXD_SPRING_HOME}\src\main\resources\application.properties` file to change the below configuration parameters:

    S.No. | Property Name | Property Description | Example
    ------|---------------|----------------------|---------
    1 | server.port | The port of running `oxd-spring` application. | server.port=8080
    2 | oxd.server.op-host | Provide the URL of your OpenID Provider (OP). | oxd.server.op-host=https://www.your-ophost.com
    3 | oxd.server.host | Hostname of oxd-server | oxd.server.host=www.your-oxd-server.com
    4 | oxd.server.port | Port of oxd-server | oxd.server.port=8443
    5 | oxd.server.acr-values | Comma separated preferred authentication methods the client will receive from the OP. | oxd.server.acr- values=basic
    6 | oxd.server.scopes | Comma separated scopes the Client is declaring that it will restrict itself to using. | oxd.server.scopes=openid,profile,uma_protection,oxd
    7 | oxd.server.grant-types | Comma separated Grant Types the Client is declaring that it will restrict itself to using. | oxd.server.grant-types=authorization_code,client_credentials

1. Change directory to the cloned `oxd-spring` project (${OXD_SPRING_HOME}) and run maven command to build the executable jar:

    ```
    cd oxd-spring 
    mvn clean package -Dmaven.test.skip=true
    ```

    Depending on connection speed and computer performance, it may take a couple of minutes to complete. It is downloading all required     dependencies and will the executable jar file.
    
1. Now you can run the executable jar using below command:

    ```
    java -jar target/oxd-spring-4.0-SNAPSHOT.jar
    ```

    The first time you run the application, it tries to register site using the parameters specified in [application.properties](https://github.com/GluuFederation/oxd-spring/blob/version_4.0/src/main/resources/application.properties). If registration was successful, then [oxd.server.op-host](https://github.com/GluuFederation/oxd-spring/blob/version_4.0/src/main/resources/application.properties#L19) and received from oxd server `oxdId` are stored in the H2 database (which is embedded in oxd-spring-4.0-SNAPSHOT.jar). Next time you run the application with the same [oxd.server.op-host](https://github.com/GluuFederation/oxd-spring/blob/version_4.0/src/main/resources/application.properties#L19), it will obtain `oxdId` from database.
    
1. Open a browser and point the browser to https://localhost:8080/. This will display Home Page of oxd-spring application with `Login To Gluu` button. Click on the button to log into application using OAuth 2.0 security. 
