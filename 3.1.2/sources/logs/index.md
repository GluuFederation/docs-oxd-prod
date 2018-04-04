# Logs

Logs are written to `/var/log/oxd-server/oxd-server.log` (location is set inside `/etc/oxd/oxd-server/log4j.xml` file). 
Custom log locations can be specified in `log4j.xml`. 

Example of `log4j.xml` file with the log file 
located in `/var/log/oxd-server/oxd-server.log`:

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
        <param name="File" value="/var/log/oxd-server/oxd-server.log"/>
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
