---
layout: post
title: "Hydration Kit ConfigMgr CB full variable"
date: 2017-04-27
tags: [PowerShell, Active Directory, Scripting, MDT, Hydration Kit ]
---

Today I release the Hydration Kit ConfigMgr CB with Server 2016 fully variable for all applications



# Index

* TOC
{:toc}


# Hydration-Kits

All applications in your Microsoft Deployment Toolkit must be run on ever system and they must be autonomous.

------

**Hydration Kit ConfigMgr Current Branch** with Server 2016

 [Link to my Github Depot]( https://github.com/JM2K69/Hydrations-Kit/tree/master/HydrationCM)

``` 
Update 28/04/07 : Some directory aren't in the github depot because the directory is empty thank's to Github. All directory are present now.
```


------



I'm working in two Hydration Kit **Sharepoint 2016** and **Exchange 2016**.




# Applications



All applications work for all Active Directory Domain Name here an example :

## PowerShell code in all Applications script

### 1. This PowerShell script return the Domain Name 

``` posh
$info = get-addomain
$infoC = $info.DNSRoot
function get-DN ($param1)
{
	$Rdomain = ""
	$split = $param1.split(".")
	$nb = $split.Count
	
	switch ($nb)
	{
		2{
			
			$domain1 = $split[0]
			$Ext1 = $split[1]
			$Rdomain = "dc=$domain1,dc=$ext1"
			
			return $Rdomain
		}
		3{
			$sdomain = $split[0]
			$domain = $split[1]
			$Ext = $split[2]
			
			$Rdomain = "dc=$sdomain,dc=$domain,dc=$ext"
			
			return $Rdomain
		}
	}
}

$dom = get-DN $infoC
```



### 2. This PowerShell Function write a log during process 

All log can be read with CMtrace during deployment in the directory C:\MININT\SMSOSD\OSDLOGS and after the deployment in C:\Windows\Temp\DeploymentLogs

```posh

Function Write-trace
{
	
	#Define and validate parameters
	[CmdletBinding()]
	Param (
		#Path to the log file
		[parameter(Mandatory = $True)]
		[String]$NewLog,
		#The information to log

		[parameter(Mandatory = $True)]
		[String]$Value,
		#The source of the error

		[parameter(Mandatory = $True)]
		[String]$Component,
		#The severity (1 - Information, 2- Warning, 3 - Error)

		[parameter(Mandatory = $True)]
		[ValidateRange(1, 3)]
		[Single]$Severity
	)
	
	
	#Obtain UTC offset
	$DateTime = New-Object -ComObject WbemScripting.SWbemDateTime
	$DateTime.SetVarDate($(Get-Date))
	$UtcValue = $DateTime.Value
	$UtcOffset = $UtcValue.Substring(21, $UtcValue.Length - 21)
	
	
	#Create the line to be logged
	$LogLine = "<![LOG[$Value]LOG]!>" +`
	"<time=`"$(Get-Date -Format HH:mm:ss.fff)$($UtcOffset)`" " +`
	"date=`"$(Get-Date -Format M-d-yyyy)`" " +`
	"component=`"$Component`" " +`
	"context=`"$([System.Security.Principal.WindowsIdentity]::GetCurrent().Name)`" " +`
	"type=`"$Severity`" " +`
	"thread=`"$([Threading.Thread]::CurrentThread.ManagedThreadId)`" " +`
	"file=`"`">"
	
	#Write the line to the passed log file
	Add-Content -Path $NewLog -Value $LogLine
	
}


```

Written by Jérôme Bezet-Torres @JM2K69.
