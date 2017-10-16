# oxd .Net

The following documentation demonstrates how to use Gluu's commercial OAuth 2.0 client software, [oxd](http://oxd.gluu.org), to send users from a Node app to an OpenID Connect Provider (OP) for login. You can securely send users to any standard OP for login, including Google and the [free open source Gluu Server](http://gluu.org/gluu-server).

!!! Note
    Refer to the [oxd csharp library docs](../../libraries/csharp/index.md) for more details on c# classes.


## Installation:

- [Github sources](https://github.com/GluuFederation/oxd-csharp)
- [Gluu Server](https://gluu.org/docs/ce/3.1.1/installation-guide/install/)
- [oxd Server](../install/)


## Configuring the oxd-server <!-- Configuration -->

- Edit the file `<path to oxd-server>/conf/oxd-conf.json`
    
    Change the OP HOST name to your OpenID Provider domain at the line `"op_host": "https://<idp-hostname>"`

- Edit the file `<path to oxd-server>/conf/oxd-default-site-config.json`
    
    Change the `response_types` line to `"response_types": ["code"]`


**Start oxd-server**

To Start oxd-server, run the following commands in command window:

```bash
> cd <path to oxd-server directory>/bin
> oxd-start-console.bat
```


## Demosite deployment <!-- Sample -->

- Your client application must have a valid SSL certification, so the URL includes: `https://`    
- The client hostname should be a valid `hostname`(FQDN), not a localhost or an IP Address. 
You can configure the `hostname` by adding the following 
entry in `C:\Windows\System32\drivers\etc\host` file:

    `127.0.0.1  client.example.com`

- Open the downloaded [Sample Project](https://github.com/GluuFederation/oxd-csharp/archive/3.1.1.zip) specific to this oxd-csharp library in Visual Studio.


- Enable SSL using the following instructions:

    - Open Visual Studio in administrator mode.
    - Open the Client application in Visual Studio.
    - Go to Client Application Properties.
    - Navigate to `Development Server` and set `SSL Enabled` to `True`.

- Change the `hostname` in the project using the following instructions:

     - Make hidden folders visible in the windows explorer.(If already done then ignore it).
     - Navigate to `vs/config` folder in the root of the project in the windows explorer.
     - Open the `applicationhost.config` file.
     - Add the following lines to `bindings` section of the project:

```code
<binding protocol="https" bindingInformation="*:portno:client.example.com" />

```
- After adding the aforementioned lines the binding section will look like this:
     
```code
<site name="GluuDemoWebsite" id="2">
    <application path="/" applicationPool="Clr4IntegratedAppPool">
        <virtualDirectory path="/" physicalPath="<path of the project>" />
    </application>
    <bindings>
        <binding protocol="https" bindingInformation="*:portno:localhost" />
        <binding protocol="https" bindingInformation="*:portno:client.example.com" />
    </bindings>
</site>
```
      
- With the oxd-server running, navigate to the URL's below to run the sample client application. To register a client in the oxd-server use the Setup Client URL. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, navigate to the Login URL for authentication.
    - Setup Client URL: https://client.example.com:portno/setupClient
    - Login URL: https://client.example.com:portno
    - UMA URL: https://client.example.com:portno/uma

- The input values used during Setup Client are stored in a configuration file (oxd_config.json). Therefore, the configuration file needs to be writable by the Client application.