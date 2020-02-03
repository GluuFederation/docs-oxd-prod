This tutorial covers the steps involved to generate the EXE file for the installation of oxd-server as service on Windows operating system. Here, we will see the procedure to install and uninstall oxd-server on windows OS using the EXE file.

## How to generate oxd windows executable installation file (`oxd-server.exe`)

The important point we should note here is that the EXE file can be only generated in windows OS. We are yet not successful to generate EXE in OS other than windows (eg: Linux). In this tutorial, we will also discuss the challenges we are facing to generate EXE on Linux. So let's see the oxd windows executable installation generation first.

#### Prerequisites

1. Windows OS

   We can generate EXE only on Windows OS.

1. JRE 8+

   Install [JRE](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) version 8 or higher. Set `JRE_HOME` environment variable on windows.

1. Inno Setup

   Download and install [Inno Setup](http://www.jrsoftware.org/isdl.php) on Windows OS. Set `INNOSETUP_HOME` environment variable pointing to the home folder of Inno Setup installation (eg: `C:\Program Files (x86)\Inno Setup 5`).

#### Steps

1. Clone 4.0 version of oxd project from [Github](https://github.com/GluuFederation/oxd)

   If you have `git` installed, just open a console and run below command to clone the project.
   ```
   git clone https://github.com/GluuFederation/oxd.git
   ```
1. Issue the below command to build oxd project
   ```
    mvn clean install -Dmaven.test.skip=true -P windows-build
   ```
   Depending on connection speed and computer performance, it may take a couple of minutes to download all required dependencies and build the project.

1. Extract `oxd-server-distribution.zip` generated in `${OXD_HOME}/oxd-server/target` folder. Change directory to the `bin` folder inside the extracted zip file.

1. Double click on `oxd-create-win-service-exe.bat` file and this will generate `oxd-server.exe` outside `bin` folder.

### OXD installation using EXE file

1. To install oxd double click on `oxd-server.exe`.

1. During installation first page that will appear will ask whether install oxd as windows service or not. Select the checkbox to install `oxd as service`.

1. The second page will have license agreement which you need to accept before moving forward.

1. The final page will allow you select installation directory of oxd server.

If installed as `windows service` then the oxd server will start automatically after the windows machine is started and there is no need to start oxd manually.

### OXD uninstall

1. The installed OXD can be uninstalled from `Control Pannel--> Programs-->Uninstall a program`.

2. We can also uninstall OXD using `unins000.exe` file inside oxd installation directory (eg: `D:\softwares\oxd-server\unins000.exe`).


### Issues found when EXE generation done on Linux OS

In Linux OS [Inno Setup](http://www.jrsoftware.org/isdl.php) can not installed directly as it is a Windows executable file. For oxd EXE generation on Linux OS, first, we need to extract `Inno Setup` EXE using [Innoextract](http://constexpr.org/innoextract/) then we need to run
`ISCC.exe` (inside the extracted  `Inno Setup`) using [Wine](https://www.winehq.org/). We are getting below error when we are running `ISCC.exe` with wine. Here we also need to provide the path of innosetup configuration file for oxd EXE generation (generate-oxd-exe.iss) in wine command. On windows, command is working properly.

-- Command Executed
```
wine /tmp/app/ISCC.exe /Foxd-server /tmp/oxd/oxd-server/src/main/bin/generate-oxd-exe.iss
```
-- Error

```
0009:fixme:process:SetProcessDEPPolicy (1): stub
0009:fixme:process:SetProcessDEPPolicy (1): stub
Inno Setup 5 Command-Line Compiler
Unknown option: /tmp/oxd/oxd-server/src/main/bin/generate-oxd-exe.iss
Copyright (C) 1997-2015 Jordan Russell. All rights reserved.
Portions Copyright (C) 2000-2015 Martijn Laan
Inno Setup Preprocessor
Copyright (C) 2001-2004 Alex Yackimoff. All rights reserved.

[ERROR] Command execution failed.
org.apache.commons.exec.ExecuteException: Process exited with an error: 1 (Exit value: 1)
    at org.apache.commons.exec.DefaultExecutor.executeInternal (DefaultExecutor.java:404)
    at org.apache.commons.exec.DefaultExecutor.execute (DefaultExecutor.java:166)
    at org.codehaus.mojo.exec.ExecMojo.executeCommandLine (ExecMojo.java:804)
    at org.codehaus.mojo.exec.ExecMojo.executeCommandLine (ExecMojo.java:751)
    at org.codehaus.mojo.exec.ExecMojo.execute (ExecMojo.java:313)
    at org.apache.maven.plugin.DefaultBuildPluginManager.executeMojo (DefaultBuildPluginManager.java:137)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:210)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:156)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:148)
    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:117)
    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:81)
    at org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder.build (SingleThreadedBuilder.java:56)
    at org.apache.maven.lifecycle.internal.LifecycleStarter.execute (LifecycleStarter.java:128)
    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:305)
    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:192)
    at org.apache.maven.DefaultMaven.execute (DefaultMaven.java:105)
    at org.apache.maven.cli.MavenCli.execute (MavenCli.java:956)
    at org.apache.maven.cli.MavenCli.doMain (MavenCli.java:288)
    at org.apache.maven.cli.MavenCli.main (MavenCli.java:192)
    at sun.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:62)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke (Method.java:498)
    at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:289)
    at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:229)
    at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode (Launcher.java:415)
    at org.codehaus.plexus.classworlds.launcher.Launcher.main (Launcher.java:356)
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  55.111 s
[INFO] Finished at: 2019-08-08T12:42:12Z
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.codehaus.mojo:exec-maven-plugin:1.6.0:exec (generate-oxd-installer) on project oxd-server: Command execution failed.: Process exited with an error: 1 (Exit value: 1) -> [Help 1]

```
