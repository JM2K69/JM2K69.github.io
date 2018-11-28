---
layout: post
title: "2018-11-28-PowerShell and Group Policy Object"
date: 2018-11-28
tags: [PowerShell,GPO, Active Directory]
---

* TOC
{:toc}


Today we are working with `Powershell` and `Group Policy Objects{} to create from end to end a **GPO** in a ADDS Domain.

# 1. Cmdlets

what Powershell cmdlets do we need to manage `GPO`.
![ComputerSection](/img/04EI67N.PNG)

Whe you use `New-GPO`, you create an empty Group Policy Object. In order to Add some registry keys you need to create the empty Object :

```powershell
New-GPO -Name "Windows_Update" -Comment "First GPO Powershell"

DisplayName      : Windows Update
DomainName       : JM2K69.pwsh
Owner            : JM2K69\Domain Admins
Id               : b3a1da54-3e4b-4942-86c5-a6940c588fbb
GpoStatus        : AllSettingsEnabled
Description      : First GPO Powershell
CreationTime     : 11/28/2018 12:50:39 PM
ModificationTime : 11/28/2018 12:50:40 PM
UserVersion      : AD Version: 0, SysVol Version: 0
ComputerVersion  : AD Version: 0, SysVol Version: 0
```

Now we need to add all **regsitry** key into the object with the command `Set-GPRegistryValue` but we need to know wich Regsitry Key to add. A web site create by a [MVP] Roger Zander permit to know wich parameter is configured wich each `regsitry key`, the web site is available [here](https://pspeditor.azurewebsites.net/)


# 2. WebSite

With this site you browse all GPO settings for **Computers** and **Users** configurations it based on **ADMX File 1803** whe are waiting an Update ping `@roger_zander`

![ComputerSection](/img/04EI76dN.PNG)

# 3. Create the parameter

You need to choose the **Computers seeting**, in my case i show how to create a GPO for setting **WSUS** frequency saerch update

![ComputerSection](/img/04EI76eN.PNG)

```powershell
PS C:\Users\Administrator> Set-GPRegistryValue -Name "Windows Update" -Key "HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU" -ValueName "DetectionFrequencyEnabled" -Value 1 -Type DWord


DisplayName      : Windows Update
DomainName       : JM2K69.pwsh
Owner            : JM2K69\Domain Admins
Id               : b3a1da54-3e4b-4942-86c5-a6940c588fbb
GpoStatus        : AllSettingsEnabled
Description      : First GPO Powershell
CreationTime     : 11/28/2018 12:50:39 PM
ModificationTime : 11/28/2018 1:04:16 PM
UserVersion      : AD Version: 0, SysVol Version: 0
ComputerVersion  : AD Version: 1, SysVol Version: 1
WmiFilter        :
PS C:\Users\Administrator> Set-GPRegistryValue -Name "Windows Update" -Key "HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU"
 -ValueName "DetectionFrequency" -Value 22 -Type DWord


DisplayName      : Windows Update
DomainName       : JM2K69.pwsh
Owner            : JM2K69\Domain Admins
Id               : b3a1da54-3e4b-4942-86c5-a6940c588fbb
GpoStatus        : AllSettingsEnabled
Description      : First GPO Powershell
CreationTime     : 11/28/2018 12:50:39 PM
ModificationTime : 11/28/2018 1:06:44 PM
UserVersion      : AD Version: 0, SysVol Version: 0
ComputerVersion  : AD Version: 2, SysVol Version: 2
WmiFilter        :
```

Now how to verify the setting with the Cmdlet `Get-GPRegistryValue`

```powershell
PS C:\Users\Administrator> Get-GPRegistryValue -Name "Windows Update" -key "HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU"

KeyPath     : Software\Policies\Microsoft\Windows\WindowsUpdate\AU
FullKeyPath : HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\WindowsUpdate\AU
Hive        : LocalMachine
PolicyState : Set
Value       : 1
Type        : DWord
ValueName   : DetectionFrequencyEnabled
HasValue    : True

KeyPath     : Software\Policies\Microsoft\Windows\WindowsUpdate\AU
FullKeyPath : HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\WindowsUpdate\AU
Hive        : LocalMachine
PolicyState : Set
Value       : 22
Type        : DWord
ValueName   : DetectionFrequency
HasValue    : True

```

And the latest Cmdlets to link the GPO :

```powershell
PS C:\Users\Administrator> Get-GPO -Name "Windows Update" | New-GPLink -Target "dc=JMK269,dc=JMK269" -LinkEnabled Yes


GpoId       : b3a1da54-3e4b-4942-86c5-a6940c588fbb
DisplayName : Windows Update
Enabled     : True
Enforced    : False
Target      : DC=JMK269,DC=JMK269
Order       : 2
```
Thanks for reading.

Written by Jérôme Bezet-Torres @JM2K69.
