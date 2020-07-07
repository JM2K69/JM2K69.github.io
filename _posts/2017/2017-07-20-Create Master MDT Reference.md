---
layout: single
title: "Create Master MDT Reference"
date: 2017-07-20
tags: 
  - Powershell
  - Sapien
categories:
  - Powershell
  - MDT
  -  Forms

published: true
comments: true
author_profile: true
header:
  teaserlogo:
  teaser: ''
 # image: img/headers/Code01_1920x500.jpg
  caption:
gallery:

  - image_path: ''
    url: ''
    title: ''
toc: true
toc_sticky: true
toc_label: "Table of content"
---



For all IT admins when you want to create a reference image with `MDT` you have to import the operating system (Install.wim) and download it to the site of the microsoft Update site the updates that will correspond. With this new tool fully written in `PowerShell`, you create your reference image with always the latest update available.



![ComputerSection](/img/MDT01.PNG)



The link for the download is [here]( https://github.com/JM2K69/Create-Master-MDT/blob/master/Create%20Reference%20Image%20MDT.exe)


# The Interface

The interface is composed of several parts :

* Computers Informations

* Download Updates

* Options

* Server

* Clients

* Actions



![ComputerSection](/img/MDT01.PNG)



### 1. Computer Informations



![ComputerSection](/img/MDTCI.PNG)

The tool allows you to retrieve the information necessary for its proper operation (RAM, Free Disk, OS Culture, ADK)

For each component a powershell function for example : 


```powershell
function Get-OsCulture
{
	[CmdletBinding()]
	param ()
	
	$oscode = Get-WmiObject Win32_OperatingSystem -ComputerName localhost -ErrorAction continue | foreach { $_.oslanguage }
	$Culture = switch ($oscode) `
	{
		1033 { "English" };
		1036 { "French" };
		default { "Unknown" }
	}
	
	switch ($Culture)
	{
		'French' {
			
			$global:OsCulture = "French"
			$global:OsCultureValue = "FR-FR"
			return $global:OsCulture, $global:OsCultureValue
			
			
		}
		{ 'English' } {
			
			$global:OsCulture = "English"
			$global:OsCultureValue = "EN-US"
			return $global:OsCulture, $global:OsCultureValue
		}
		
		Default { }
	}
}

```

For **ADK** :

```powershell
function Found-ADK
{
	[CmdletBinding()]
	param
	(
		[Parameter(Mandatory = $true)]
		$OsCulture = 'FR-FR'
	)
	switch ($OsCulture)
	{
		'EN-US'
		{
			$ADK = Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Where-Object { $_.DisplayName -eq "Windows Assessment and Deployment Kit - Windows 10" }
			if ($ADK -eq $null)
			{
				$global:ADKpresent = 'Not Found'
				return $global:ADKpresent
			}
			else
			{
				$global:ADKpresent = 'Present'
				$global:ADKv = $ADK.DisplayVersion
				return $global:ADKpresent, $global:ADKv
			}
		}
		'FR-FR'
		{
			$ADK = Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Where-Object { $_.DisplayName -like "Kit* Windows 10" }
			if ($ADK -eq $null)
			{
				$global:ADKpresent = 'Not Found'
				$global:ADKv = $ADK.DisplayVersion
				return $global:ADKpresent, $global:ADKv
			}
			else
			{
				$global:ADKpresent = 'Present'
				$global:ADKv = $ADK.DisplayVersion
				return $global:ADKpresent, $global:ADKv
			}
		}
		
		Default { }
	}
	
}
```

### 2. Download Update

![ComputerSection](/img/MDTDU.PNG)

To download the latest updates depending on the version of the Builds of Windows I reuse the function of Keith Garner @keithga1 `Get-LastestUpdate`. All Update will be transfert with BITS in a job and we can stop the action. 

* To begin must make a choice for `Windows Builds`
* Then Check Cumulative available

  * The tool return the ***name*** of the ***KB*** and the ***size*** of the future download
* Then start the `BITS download`


All updates are download on the directory **c:\Updates**

### 3.Options

![ComputerSection](/img/MDTOP.PNG)

By default for all reference images the `Framework 3.5.1` is installed and the `SMB Protocol v1 features` are disabled by default (Ransomwares ...). For OS clients to reduce the sizes of wim images, the command dism.exe / Image: c: \ mount / Cleanup-Image / StartComponentCleanup / ResetBase is applied.

At first I had a problem because this command is not contained in the **module PowerShell DISM**, so it must go through the executable but all this happens in a window cmd. Looking for a bit I was able to create a function to launch a command back or any other command silently.

```powershell
function Launch-Hide ($Command, $arguments)
{
	
	$startinfo = new-object System.Diagnostics.ProcessStartInfo
	$startinfo.FileName = $Command
	$startinfo.Arguments = $arguments
	$startinfo.CreateNoWindow = $true
	$startinfo.WindowStyle = [System.Diagnostics.ProcessWindowStyle]::hidden
	$startinfo.UseShellExecute = $false
	[System.Diagnostics.Process]::Start($startinfo)
}

```

**For example :**  

```powershell
Launh-Hide -Command ping.exe -arguments "127.0.0.1 -t"
```

This command is used to `ping` the local loop for an infinite time because of the `"-t"` argument and especially without any windows.

### 4.Server

![ComputerSection](/img/MDTS.PNG)

You have to select the image you want to use.



### 5.Clients

![ComputerSection](/img/MDTCL.PNG)

Like the Server you must select the image you want to use.



### 6.Actions

![ComputerSection](/img/MDTCL.PNG)

Before you start you have to check if everything is OK.

------



## 2. This PowerShell Function write a log during process 

During the process is at each moment an event is logging into a `.log` file in the execution directory of the tool is in the application window.

```powershell

function Write-log
{
	param
	(
		[Parameter(Mandatory = $true)]
		[String]$message
	)
	
	$timeStamp = Get-Date -Format "[dd/MM/yy - %H:mm:ss]"
	$VerboseLogFile = "$ScriptDirectory\CreateReferenceImageMDT.log"
	$global:Log = Write-Output "$timestamp $message"
	$Logmessage = "$timestamp $message"
	$logMessage | Out-File -Append -LiteralPath $verboseLogFile
}
```


![ComputerSection](/img/MDTLOG.PNG)




Written by Jérôme Bezet-Torres @JM2K69.