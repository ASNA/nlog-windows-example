### Application logging

This example shows a way to do application logging with an ASNA Visual RPG Windows application. 

#### Ways to log

You could use classes in the System.IO namespace to roll your own logger--and for simple uses this may be OK. But you'll quickly need to do things like specific log levels, manage log files, and you may even need to log to something more sophisticated than a text file (eg, SQL Server, email, console, CSV files)

There are many very useful open source logging packages available for .NET. For this example we're going to use NLog (on [Github](https://github.com/NLog/NLog) and on [Nuget.org](https://www.nuget.org/packages?q=nlog)). 

#### How most logging components work

Loggers generally log to many log targets, such as SQL Server, email, or CSV files), but this article focuses on what is probably the most frequently-used target, a text file.

Most loggers are implemented with two primary concepts: 

1. A place for log entries (in this example a text file). NLog defines where to log entries with "targets." 
  
2. A way to define what to write to the log (in this case, through classes that NLog provides). NLog defines what to write to a log with "rules." 

With NLog, there can be many targets and many rules. 

#### How to use NLog in an ASNA Visual RPG windows application

This section explains how to use NLog with an ASNA Visual RPG Windows application. [Using NLog with ASP.NET Web](https://github.com/ASNA/nlog-web-example).

These steps are covered in the video for this example:

1. Add NLog to your project.  
2. Add an NLog config file
   -  Be sure to set the config file's `Build Action` property to `Content` and the `Copy to Output Directory` property to `Copy always`. This ensures NLog can find the config file at runtime. If these properties aren't set NLog won't work! 
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

> Be sure to set the config file's `Build Action` property to `Content` and the `Copy to Output Directory` property to `Copy always`. This ensures NLog can find the config file at runtime. If these properties aren't set NLog won't work! 

![config-file-properties.png](https://asna.com/filebin/marketing/article-figures/nlog/config-file-properties.png)

<small>Setting the config file's properties</small>

**Basic AVR code to write a log entry.**

```
Using System
Using NLog

BegClass Form1 Extends(System.Windows.Forms.Form) +
               Access(*Public) Partial(*Yes)

    DclFld log Type(NLog.Logger)

    ... Constructor here

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


#### A real-world NLOG example


Add a `StartUp.vr` class as the application's entry point. This provides global exception handling for the Windows application. Change the primary form name as needed. 

```
Using System
Using System.Text
Using System.Collections.Generic

BegClass Startup Access(*Public)

    BegSr Main Access( *Public ) Shared( *Yes ) 
        DclSrParm CommandLineArguments Type( *String ) Rank( 1 ) 
        
        DclFld f1 Type( Form1 ) New()
        DclFld logger Type(NLog.Logger) 
       
        Try 
            f1.ShowDialog()
        Catch ex Type(Exception) 
            logger = NLog.LogManager.GetLogger("MyLogger")
            logger.Fatal(ex, ex.Message)

            // Needed if your have the NLog config keep the log 
            // file open (keepFileOpen="True").
            NLog.LogManager.Shutdown()

            MsgBox Icon(*Stop) Title("Fatal error") Msg(ex.Message) 
        Finally
            f1.Dispose() 
        EndTry 
    EndSr

EndClass
```

![startup-object.png](https://asna.com/filebin/marketing/article-figures/nlog/startup-object.png)

<small>Be sure to change the application's Start object after adding `Startup.vr`</small>

NLog configuration with rolling log archives and exception logging.
```
<?xml version="1.0" encoding="utf-8" ?>

<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.nlog-project.org/schemas/NLog.xsd NLog.xsd"
      autoReload="false"
      throwExceptions="false"
      throwConfigExceptions="true"
      internalLogLevel="Off"
      internalLogFile="c:\temp\nlog-internal.log">

    <variable name="logLevel" value="Trace" />

    <targets>
        <target
            xsi:type="File"
            name="logfile"
            fileName="${specialfolder:folder=ApplicationData}/winapp/logs/${shortdate}.log"
            keepFileOpen="False"
            layout="${longdate} ${uppercase:${level}} ${message} ${exception} [${stacktrace}] ${onexception:${newline}EXCEPTION OCCURRED\:${newline}${exception:format=type,message,stacktrace,method:maxInnerExceptionLevel=5:innerFormat=type,message,method}}"

            
            archiveFileName="${specialfolder:folder=ApplicationData}/winapp/logs/archive/${shortdate}-{#}.log"
            archiveAboveSize="5000000"
            archiveEvery="Day"
            archiveNumbering = "Rolling"
            maxArchiveFiles="14"  />
    </targets>

    <rules>
        <logger name="MyLogger" minlevel="${var:logLevel}" writeTo="logfile" />
    </rules>
</nlog>
```


```
Using System
Using NLog

BegClass Form1 Extends(System.Windows.Forms.Form) +
               Access(*Public) Partial(*Yes)

   ... Constructor here

    DclFld log Type(NLog.Logger)

    BegSr Form1_Load Access(*Private) Event(*this.Load)
        DclSrParm sender *Object
        DclSrParm e System.EventArgs

        log = LogManager.GetLogger("MyLogger")        
    EndSr
    
    BegSr button1_Click Access(*Private) Event(*this.button1.Click)
        DclSrParm sender Type(*Object)
        DclSrParm e Type(System.EventArgs)

        log.Trace("Button1 clicked") 
        log.Debug("Button1 clicked to do a debug log entry") 
    EndSr
    
    BegSr button2_Click Access(*Private) Event(*this.button2.Click)
        DclSrParm sender Type(*Object)
        DclSrParm e Type(System.EventArgs)

        DclFLd Zero Type(*Integer4) Inz(0)
        DclFld Result Type(*Integer4) 

        Result = 88 / Zero     
    EndSr
    
EndClass
```

### Helpful links

* [NLog on GitHub](https://github.com/NLog/NLog)
* [NLog on GitHub Wiki](https://github.com/NLog/NLog/wiki)
* [NLog Web site and additional docs](https://nlog-project.org/)

* [TailViewer: A good, open source log viewer](https://kittyfisto.github.io/Tailviewer/). A good, basic log viewer that is easy to use.
* [LogViewPlus: A good commercial log viewer ($45-$95 US per seat)](https://www.logviewplus.com/). A more complex and capable log viewer--probably overkill unless you investigate logs a lot.
