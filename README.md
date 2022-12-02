### Application logging

This example shows a way to do application logging with an ASNA Visual RPG Windows application. 


https://github.com/ASNA/nlog-web-example

#### What is and why do it

Application logging 


Show example log

Trace statements, timing statement and exception

[A good free log viewer](https://kittyfisto.github.io/Tailviewer/)



#### Ways to log

You could use classes in the System.IO namespace to roll your own logger--and for simple uses this may be OK. But you'll quickly need to do things like specific log levels, manage log files, and you may even need to log to something more sophisticated than a text file (eg, SQL Server, email, console, CSV files)

There are many very useful open source logging packages available for .NET. For this example we're going to use NLog (on [Github](https://github.com/NLog/NLog) and on [Nuget.org](https://www.nuget.org/packages?q=nlog)). 

#### How most logs work

Loggers generally log to many log targets, such as SQL Server, email, or CSV files), but this article focuses on what is probably the most frequently-used target, a text file.

Most loggers are implemented with two primary concepts: 

1. A place for log entries (in this example a text file). NLog defines where to log entries with "targets." 
  
2. A way to define what to write to the log (in this case, through classes that NLog provides). NLog defines what to write to a log with "rules." 

With NLog, there can be many targets and many rules. 


#### How to use NLog in an ASNA Visual RPG windows application

This section explains how to use NLog with an ASNA Visual RPG Windows application. [Using NLog with ASP.NET Web](#).

These steps are covered in the video for this example:

1. Add NLog to your project.  
2. Add an NLog config file
   -  Be sure to set the config file's `Build Action` property to `content` and the `Copy to Output Directory` property to `Copy always`. This ensures NLog can find the config file at runtime. If these properties aren't set NLog won't work! 
3. Define at least one target
4. Define at least one rule
5. Write log entries in your code


#### Very simple NLog example

This very simple example creates a log file in the application's `Bin` folder. 

**Very basic NLog configuration**

```
<?xml version="1.0" encoding="utf-8" ?>
<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <targets>
        <target name="logfile" xsi:type="File" fileName="warren-zevon.txt" />
    </targets>

    <rules>
        <logger name="MyLogger" minlevel="Trace" writeTo="logfile" />
    </rules>
</nlog>
```

> Be sure to set the config file's `Build Action` property to `content` and the `Copy to Output Directory` property to `Copy always`. This ensures NLog can find the config file at runtime. If these properties aren't set NLog won't work! 

![config-file-properties.png](https://asna.com/filebin/marketing/article-figures/nlog/config-file-properties.png)

<small>Setting the config file's properties</small>
 

**Basic AVR code to write a log entry.**

```
Using System
Using NLog

BegClass Form1 Extends(System.Windows.Forms.Form) +
               Access(*Public) Partial(*Yes)

   DclFld log Type(NLog.Logger)

    BegSr Form1_Load Access(*Private) Event(*this.Load)
        DclSrParm sender *Object
        DclSrParm e System.EventArgs
        
        log = LogManager.GetLogger("MyLogger")
    EndSr
 
    BegSr button1_Click Access(*Private) Event(*this.button1.Click)
        DclSrParm sender Type(*Object)
        DclSrParm e Type(System.EventArgs)

        log.Trace("Hello, world') 
    EndSr
    
EndClass
```

Very basic NLog configuration

```
<?xml version="1.0" encoding="utf-8" ?>
<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <targets>
        <target name="logfile" xsi:type="File" fileName="warren-zevon.txt" />
    </targets>

    <rules>
        <logger name="MyLogger" minlevel="Trace" writeTo="logfile" />
    </rules>
</nlog>
```







#### Using NLog internal log file 




### Helpful links

* [NLog on GitHub](https://github.com/NLog/NLog)
* [NLog on GitHub Wiki](https://github.com/NLog/NLog/wiki)
* [NLog Web site and additional docs](https://nlog-project.org/)

* [TailViewer: A good, open source log viewer](https://kittyfisto.github.io/Tailviewer/). A good, basic log viewer that is easy to use.
* [LogViewPlus: A good commercial log viewer ($45-$95 US per seat)](https://www.logviewplus.com/). A more complex and capable log viewer--probably overkill unless you investigate logs a lot.
