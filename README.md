# NLog #

Get GitHub address!

Outline

### Application logging

#### What is and why do it
Show example log

Trace statements, timing statement and exception

[A good free log viewer](https://kittyfisto.github.io/Tailviewer/)

#### Ways to log

You could use classes in the System.IO namespace to roll your own logger--and for simple uses this may be OK. But you'll quickly need to do things like specific log levels, manage log files, and you may even need to log to something more sophisticated than a text file (eg, SQL Server, email, console, CSV files)

There are many very useful open source logging packages available for .NET. For this example we're going to use NLog (on [Github](https://github.com/NLog/NLog) and on [Nuget.org](https://www.nuget.org/packages?q=nlog)). 

#### How most logs work

Loggers generally can log to many log targets, such as SQL Server, email, or CSV files), but this article focuses on what is probably the most frequently-used target, a text file.

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


#### Using NLog internal log file 





### Use PowerShell to manage Windows Event Log

Register a new Event Log and Event Log source with the Windows Event Log

```
New-EventLog -LogName CoolWebApp -Source "Production"
```

Unregister an Event Log

```
Remove-Eventlog -logname "CoolWebApp"
```

Show ten most recent entries in a log

```
get-eventlog -logname CoolWebApp -newest 10
```