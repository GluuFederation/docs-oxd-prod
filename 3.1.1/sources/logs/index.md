# Logs

Logs are written into the working directory in `oxd Server` by default. 
Custom log locations can be specified in `log4j.xml.` The file is 
loaded using the `log4j.configuration` system property. The following 
is an example for running the `oxd Server` with `log4j` with the file 
located in `C:\tmp\test\log4j.xml`.

```
# java -cp resteasy-jaxrs-2.3.4.Final.jar;oxd-server-3.1.1-SNAPSHOT-jar-with-dependencies.jar org.xdi.oxd.server.ServerLauncher -Doxd.server.config=C:\tmp\oxd.json -Dlog4j.configuration=C:\tmp\test\log4j.xml
```

The following is an example of `log4j.xml` file:

```
<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
 
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/" debug="false">
 
    <appender name="CONSOLE" class="org.apache.log4j.ConsoleAppender">
        <layout class="org.apache.log4j.PatternLayout">
            <!-- The default pattern: Date Priority [Category] Message\n -->
            <param name="ConversionPattern" value="%d %-5p [%c] %m%n"/>
        </layout>
    </appender>
 
    <appender name="FILE" class="org.apache.log4j.DailyRollingFileAppender">
        <param name="File" value="C:\\tmp\\test\\oxd-server.log"/>
        <param name="DatePattern" value="'.'yyyy-MM-dd"/>
        <param name="BufferSize" value="1000"/>
        <layout class="org.apache.log4j.PatternLayout">
            <!-- The default pattern: Date Priority [Category] Message\n -->
            <param name="ConversionPattern" value="%d %-5p [%c] %m%n"/>
        </layout>
    </appender>
 
    <category name="org.xdi">
        <priority value="TRACE"/>
    </category>
 
    <root>
        <priority value="INFO"/>
        <appender-ref ref="FILE"/>
        <appender-ref ref="CONSOLE"/>
    </root>
 
</log4j:configuration>
```
