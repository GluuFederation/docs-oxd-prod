# oxd-.net

Use oxd's .Net library to send users from a .Net application to your Gluu Server OpenID Connect Provider (OP) for dynamic enrollment, single sign-on (SSO), strong authentication, and access management policy enforcement. 

!!! Note
    Refer to the [oxd-csharp library docs](../../languages/csharp/index.md) for more details on c# classes.


## Installation Guides

- [Github oxd-csharp](https://github.com/GluuFederation/oxd-csharp)
- [Gluu Server](https://gluu.org/docs/ce/3.1.1/installation-guide/install/)
- [oxd-server](../../../install/index.md)


## Prerequisites


- Microsoft Visual Studio 2012 or higher
- Windows Server 2008 or higher
- Gluu.Oxd.OxdCSharp
- .Net Framework 4.5 or higher

To use the oxd-csharp library, you will need:

- A valid OpenID Connect Provider (OP), like the [Gluu Server](https://gluu.org/gluu-server) or Google.    
- An active installation of the [oxd-server](../../../install/index.md) running on the same server as the client application.
- An active installation of the [oxd-https-extension](../../../install/index.md) if oxd-https-extension connection is used. In this case, client applications can be on different servers but will be able to access oxd-https-extension.
- A Windows server or Windows installed machine / Linux server or Linux installed machine.



## Configuring oxd-server

- Edit the file `/opt/oxd-server/conf/oxd-conf.json` 

    Update the following fields `"server_name"`, `"license_id"`, `"public_key"` and `"public_password"`

- Edit the file `/opt/oxd-server/conf/oxd-default-site-config.json`

    Change the OP HOST name to your OpenID Provider domain at the line `"op_host": "https://<idp-hostname>"`

    Change the `response_types` line to `"response_types": ["code"]`

- To start oxd-server, run the following command or [click here](../../../install/index.md) for more detailed instructions:

```bash
> cd <path to oxd-server directory>/bin
> oxd-start-console.bat
```


## Demosite Deployment

- Your client application must have a valid SSL certification, so the URL includes: `https://`    
- The client hostname should be a valid `hostname` (FQDN), not a localhost or an IP Address. 
- You can configure the `hostname` by adding `127.0.0.1  client.example.com` to the file  `C:\Windows\System32\drivers\etc\host`
- Open the downloaded [Sample Project](https://github.com/GluuFederation/oxd-csharp/archive/3.1.1.zip) specific to this oxd-csharp library in Visual Studio.


- Enable SSL using the following instructions:

    - Open Visual Studio in administrator mode
    - Open the client application in Visual Studio
    - Go to client application properties
    - Navigate to `Development Server` and set `SSL Enabled` to `True`

- Change the `hostname` in the project using the following instructions:

     - Make hidden folders visible in windows explorer
     - Navigate to `vs/config` folder in the root of the project in windows explorer
     - Open the `applicationhost.config` file
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
        <binding protocol="https" bindingInformation="*:<portno>:client.example.com" />
    </bindings>
</site>
```
      
- With the oxd-server running, navigate to the URL's below to run the sample client application. To register a client in the oxd-server use the Setup Client URL. Upon successful registration of the client application, an oxd ID will be displayed in the UI. Next, navigate to the Login URL for authentication.
    - Setup Client URL: https://client.example.com:<portno>/Home/Setting
    - Login URL: https://client.example.com:<portno>
    - UMA URL: https://client.example.com:<portno>/Home/UMA

- The input values used during Setup Client are stored in a configuration file (oxd_config.json). Therefore, the configuration file needs to be writable by the client application.
