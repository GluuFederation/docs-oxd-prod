# oxd-node-demo

The following documentation demonstrates how to use Gluu's commercial OAuth 2.0 client software, [oxd](http://oxd.gluu.org), to send users from a Node app to an OpenID Connect Provider (OP) for login. You can send users to any standard OP for login, including Google. In these docs we use the [free open source Gluu Server](http://gluu.org/gluu-server) as the OP.

# Installation:

* [Github sources](https://github.com/GluuFederation/oxd-node)
* [Gluu Server](https://gluu.org/docs/ce/3.0.1/installation-guide/install/)
* [Oxd Server](../install/)

**Attention:**
```
Application will not be working if your host does not have https://.
```

# Configuration:

Once the library is installed, create a copy of the sample configuration file for your website in a server _writable_ location and edit the configuration. For example

```
Go to properties.js,
find exports.app_port=null and enter port no inplace of "null" which ever is free on your server.
```

# How to use:

1. Download source code for demo client application [oxd-node-demo]
2. Configure your port in `properties.js` file in root directory
3. From command line, move into demo client application and enter `npm update`, and run it [node index].
4. Go to web browser and access demo application with this url `https://localhost.com:{port}` (you can use any other port incase if 5053 port is busy in any other process)
5. Register your website with oxd, fill the site registration form and submit it.
6. Now your site user can login using oxd-server

# Demo Video:

```
Have a look into this demo video, a screen recording of this demo website’s features.
```
[Demo video](http://screencast.com/t/7BD1DzYi)
