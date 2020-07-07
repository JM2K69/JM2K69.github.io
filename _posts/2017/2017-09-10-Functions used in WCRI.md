---
layout: single
title: "Functions used in WCRI"
date: 2017-09-10
tags: 
  - Powershell
  - 'Sapien'
categories:
  - Powershell
  - MDT

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



In this post i talk about all function used in the Windows Creator Reference Image (WCRI)



### 1. Simple function

With this first basic function we create a `MessageBox` with text that is passed in `parameter`

The function look like this :

```powershell
function New-Message ($message)
{
	[void][System.Windows.Forms.MessageBox]::Show($message)
}
New-Message "This is the first Message "
```




![ComputerSection](/img/FunctionS1.PNG)

But the rseult is very simple now we can customize some objects , **Title**, **Icons**, **Types** of button and in this function we can use the **`mandatory option`**. 

### 2. Advanced function

```powershell
function New-Message
{
	param
	(
		[Parameter(Mandatory = $true)]
		$message,
		[Parameter(Mandatory = $true)]
		[ValidateSet('Asterisk', 'Error', 'Exclamation', 'Hand', 'Information', 'None', 'Question', 'Stop', 'Warning')]
		$Icons,
		[Parameter(Mandatory = $true)]
		[ValidateSet('AbortRetryIgnore', 'OK', 'OKCancel', 'RetryCancel', 'YesNo', 'None', 'YesNoCancel')]
		$Types,
		[Parameter(Mandatory = $true)]
		$Titles
	)
	
	$Button = [Windows.Forms.MessageBoxButtons]::$Types
	$Icon = [Windows.Forms.MessageBoxIcon]::$Icons
	
	[void][System.Windows.Forms.MessageBox]::Show($message, $Titles, $Button, $Icon)
}
```

**Some examples:**

* New-Message -message 'Asterisk' -Icons Asterisk -Types AbortRetryIgnore -Titles "Second message"

![ComputerSection](/img/FunctionS2.PNG)

* New-Message -message "Informations" -Icons Information -Types YesNo -Titles "Third message"

![ComputerSection](/img/FunctionS3.PNG)

### 3. Launch one process without any windows

In some cases we need to launch tasks in hidden ways. I create a `PowerShell`function for this task.

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

**For example** : when you call **DISM** or **OSCDIMG** with `PowerShell` a windows appear with this function all windows doesn't appear but we don't know when the process is terminated I create an other function to monitor.

If you want to disable SMB1Protocol silently you can use the function.

```powershell
$MountFolder = "C:\Mount"
Launch-Hide -Command "$env:SystemRoot\system32\Dism.exe" -arguments "/Image:$MountFolder /Disable-Feature /FeatureName:SMB1Protocol"

```

### 4. Monitor Process with heartbeat Message

With this function you can monitor a windows process an you can personalize the intervall Time in minute for the message.

```powershell
function Get-ProcessElapsedTime  ($proc, $intervallM, $FirstMessage, $HeartMessage) {
    $status_start = 0
    $status_after = 0  
    $debutAff = ((Get-Process -Name $proc).StartTime)
    $PPid = $(get-process -Name notepad).Id
    do {

        $currentAff = Get-Date 
        $duration = [math]::Round((New-TimeSpan -Start $debutAff -End  $currentAff).TotalMinutes, 2)
    
        if ($duration -lt $intervallM) {
       
            if ( $status_start -eq 0) { 
                Write-Host "$FirstMessage $debutAff"
                $status_start = $intervallM
       
            }
        }
        else { 
     
            if (($status_after + $intervallM) -eq $duration) {
                Write-Host "$HeartMessage $duration minutes with the PID $Ppid "
                $status_after = $duration
            }
        }
    }
    while (Get-Process -Name $proc -ErrorAction SilentlyContinue)
    
    Write-Host "The process $proc was working during $duration minutes"
}

```

You want to monitor a process  with a custom message every minutes :

```powershell
Get-processelapsedtime -proc DISM -intervallM 1 -FirstMessage "DISM is working the process is starting at" -HeartMessage "DISM is working since"
```



![ComputerSection](/img/DISMS1.PNG)

**Now with 5 minutes :**

 

```powershell
Get-processelapsedtime -proc DISM -intervallM 5 -FirstMessage "DISM is working the process is starting at" -HeartMessage "DISM is working since"
```



![ComputerSection](/img/DISMS2.PNG)

### 5. Remove Unwantted Windows 10 Apps 

During the deployment of Windows 10 in some cases administrators want to remove the embedded applications of Windows 10. Here is a function that allows to remove them.

**`Attention :`** in this function I use another function to write logs in a file and in a **ListBox**. It is possible to replace them with the `write-host` function

```powershell
function Remove-AppxPro ($MountFolder)
	{
		Write-log -message "=========Starting Step : Remove Win10 Apps========"
		Update-ListBox -Items $Log -Append -ListBox $listbox1
		[System.Windows.Forms.Application]::DoEvents()
		
		$apps = Get-AppxProvisionedPackage -Path $MountFolder
		$WhiteListedApps = @(
			"Microsoft.DesktopAppInstaller",
			"Microsoft.Messaging",
			"Microsoft.StorePurchaseApp"
			"Microsoft.WindowsCalculator",
			"Microsoft.WindowsCommunicationsApps", # Mail, Calendar etc
			"Microsoft.WindowsSoundRecorder",
			"Microsoft.WindowsStore"
		)
		[System.Windows.Forms.Application]::DoEvents()
		foreach ($i in $WhiteListedApps)
		{
			Write-log -message "The Application name $i is in the WhitelistedApps)"
			Update-ListBox -Items $Log -Append -ListBox $listbox1
			[System.Windows.Forms.Application]::DoEvents()
		}
		
		# Loop through the list of appx packages
		foreach ($App in $Apps)
		{
			# If application name not in appx package white list, remove AppxPackage and AppxProvisioningPackage
			if (($App.DisplayName -in $WhiteListedApps))
			{
				[System.Windows.Forms.Application]::DoEvents()
				Write-log -message "Skipping excluded application package: $($App.Displayname)"
				[System.Windows.Forms.Application]::DoEvents()
				
			}
			else
			{
				# Gather package names
				$AppPackageFullName = Get-AppxPackage -Name $App.DisplayName | Select-Object -ExpandProperty PackageFullName
				$AppProvisioningPackageName = Get-AppxProvisionedPackage -Path "$MountFolder" | Where-Object { $_.DisplayName -like $App.DisplayName } | Select-Object -ExpandProperty PackageName
				# Attempt to remove AppxPackage
				if ($AppPackageFullName -ne $null)
				{
					try
					{
						Write-log -message "Removing application package: $($App.Displayname)"
						[System.Windows.Forms.Application]::DoEvents()
						
						Remove-AppxProvisionedPackage -Path $MountFolder -PackageName $_.PackageName
						[System.Windows.Forms.Application]::DoEvents()
					}
					catch [System.Exception] {
						[System.Windows.Forms.Application]::DoEvents()
						#write-host  "Removing AppxPackage failed: $($_.Exception.Message)" -ForegroundColor DarkGreen
						Write-log -message "Removing AppxPackage failed: $($_.Exception.Message)"
						[System.Windows.Forms.Application]::DoEvents()
						
					}
				}
				else
				{
					[System.Windows.Forms.Application]::DoEvents()
					Write-log -message "Unable to locate AppxPackage for app: $($App.Displayname)"
					[System.Windows.Forms.Application]::DoEvents()
					
				}
				# Attempt to remove AppxProvisioningPackage
				if ($AppProvisioningPackageName -ne $null)
				{
					try
					{
						[System.Windows.Forms.Application]::DoEvents()
						Write-log -message "Removing application provisioning package: $($AppProvisioningPackageName)"
						[System.Windows.Forms.Application]::DoEvents()
						
						Remove-AppxProvisionedPackage -PackageName $AppProvisioningPackageName -Path $MountFolder -ErrorAction Stop | Out-Null
						[System.Windows.Forms.Application]::DoEvents()
					}
					catch [System.Exception] {
						[System.Windows.Forms.Application]::DoEvents()
						Write-log -message "Removing AppxProvisioningPackage failed: $($_.Exception.Message)"
						[System.Windows.Forms.Application]::DoEvents()
						
					}
				}
				else
				{
					[System.Windows.Forms.Application]::DoEvents()
					Write-log -message "Unable to locate AppxProvisioningPackage for app: $($App.Displayname)"
					
					[System.Windows.Forms.Application]::DoEvents()
				}
			}
		}
		# White list of Features On Demand V2 packages
		Write-log -message "Starting Features on Demand V2 removal process"
		Update-ListBox -Items $Log -Append -ListBox $listbox1
		
		$WhiteListOnDemand = "NetFX3|Tools.Graphics.DirectX|Tools.DeveloperMode.Core|Language|Browser.InternetExplorer"
		# Get Features On Demand that should be removed
		$OnDemandFeatures = Get-WindowsCapability -path $MountFolder | Where-Object { $_.Name -notmatch $WhiteListOnDemand -and $_.State -like "Installed" } | Select-Object -ExpandProperty Name
		foreach ($Feature in $OnDemandFeatures)
		{
			try
			{
				Write-log -message "Removing Feature on Demand V2 package: $($Feature)"
				[System.Windows.Forms.Application]::DoEvents()
				
				Get-WindowsCapability -Online -ErrorAction Stop | Where-Object { $_.Name -like $Feature } | Remove-WindowsCapability -Online -ErrorAction Stop | Out-Null
				[System.Windows.Forms.Application]::DoEvents()
			}
			catch [System.Exception] {
				[System.Windows.Forms.Application]::DoEvents()
				Write-log -message "Removing Feature on Demand V2 package failed: $($_.Exception.Message)"
				[System.Windows.Forms.Application]::DoEvents()
				
			}
		}
		
		Write-log -message "=========The step : Remove Win10 Apps is finished========"
		Update-ListBox -Items $Log -Append -ListBox $listbox1
	}
```



Written by Jérôme Bezet-Torres @JM2K69.