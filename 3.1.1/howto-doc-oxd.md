# Howto Write OXD Docs

All oxd libraries should have a uniform structure. We also want to re-use this document for the Github Readme.

![Follow this outline for oxd docs...](https://raw.githubusercontent.com/GluuFederation/docs-oxd/master/sources/img/oxd-library-docs.png)

## Section 1 - Name / oxd info

This section is very short. Simply provide your project name,
and use the following text

```
For information about oxd, visit http://oxd.gluu.org

```

We like this short, because we won't have a lot of copy to update!

## Section 2 - Install / Platform

In this section, you should provide as much information as possible about how
your platform works. Make sure to include links to Github, including source,
tests, and sample application.

What is the easiest way to install? Are there any language specific distributions?
Linux package support? How to install from source? 

Any requirements to run this library? What language versions are supported? 
Is there any language specific auto-generated html API documentation?

This is a good document any more information about this library. 


## Section 3 - Config

Developers should be able to persist configuration and registration properties 
on the file system. Optional DB or other storage is also ok. These would be configuration
for your library--not oxd configuration which is documented in the oxd docs.

## Section 4 - Sample Code

You need sample code corresponding to every API in the oxd protocol. They should be listed in 
this order:

1. client_register
2. get_authz_url
3. get_tokens
4. get_user_info
5. logout
6. update_client_register
7. uma_rs_protect
8. uma_rs_check_access
9. uma_rp_get_rpt
10. uma_rp_authorize_rpt
11. gat_rp_get_rpt

Use [code block formatting](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#code) so it's
 easier to read.
 
 Show the minimum code required to make it work. A link to test code is also good. This should not
  be the place for library documentation, because we want to keep this short. 






