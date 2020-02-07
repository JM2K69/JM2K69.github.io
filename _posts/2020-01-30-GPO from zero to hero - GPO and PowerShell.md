---
layout: post
title: "GPO from zero to hero - Powershell and GPO"
date: 2020-02-07
tags: [PowerShell,Active Directory, GPO ]
published : True
---


During my last [post](https://jm2k69.github.io/2020-01-02-GPO-from-zero-to-hero-GPO-Structure/) we talked about GPO in an Active Directory domain, today we will move on to concrete things create an managed GPO with `PowerShell`

# 1. Powershell Module

And yes there is a powershell module for GPOs and nothing easier to list the commands ;-)
```powershell
Get-Command -Module GroupPolicy

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Get-GPPermissions                                  1.0.0.0    GroupPolicy
Alias           Set-GPPermissions                                  1.0.0.0    GroupPolicy
Cmdlet          Backup-GPO                                         1.0.0.0    GroupPolicy
Cmdlet          Copy-GPO                                           1.0.0.0    GroupPolicy
Cmdlet          Get-GPInheritance                                  1.0.0.0    GroupPolicy
Cmdlet          Get-GPO                                            1.0.0.0    GroupPolicy
Cmdlet          Get-GPOReport                                      1.0.0.0    GroupPolicy
Cmdlet          Get-GPPermission                                   1.0.0.0    GroupPolicy
Cmdlet          Get-GPPrefRegistryValue                            1.0.0.0    GroupPolicy
Cmdlet          Get-GPRegistryValue                                1.0.0.0    GroupPolicy
Cmdlet          Get-GPResultantSetOfPolicy                         1.0.0.0    GroupPolicy
Cmdlet          Get-GPStarterGPO                                   1.0.0.0    GroupPolicy
Cmdlet          Import-GPO                                         1.0.0.0    GroupPolicy
Cmdlet          Invoke-GPUpdate                                    1.0.0.0    GroupPolicy
Cmdlet          New-GPLink                                         1.0.0.0    GroupPolicy
Cmdlet          New-GPO                                            1.0.0.0    GroupPolicy
Cmdlet          New-GPStarterGPO                                   1.0.0.0    GroupPolicy
Cmdlet          Remove-GPLink                                      1.0.0.0    GroupPolicy
Cmdlet          Remove-GPO                                         1.0.0.0    GroupPolicy
Cmdlet          Remove-GPPrefRegistryValue                         1.0.0.0    GroupPolicy
Cmdlet          Remove-GPRegistryValue                             1.0.0.0    GroupPolicy
Cmdlet          Rename-GPO                                         1.0.0.0    GroupPolicy
Cmdlet          Restore-GPO                                        1.0.0.0    GroupPolicy
Cmdlet          Set-GPInheritance                                  1.0.0.0    GroupPolicy
Cmdlet          Set-GPLink                                         1.0.0.0    GroupPolicy
Cmdlet          Set-GPPermission                                   1.0.0.0    GroupPolicy
Cmdlet          Set-GPPrefRegistryValue                            1.0.0.0    GroupPolicy
Cmdlet          Set-GPRegistryValue                                1.0.0.0    GroupPolicy

```
With yes 28 cmdlets if I count correctly ;-))

# 1.1 List All GPO ?

For this example it's easy you can run the cmdlet `get-GPO` with the **parameter**  `-all`and you find all GPO in you `AD Forest` like this : 

```powershell
DisplayName      : Default Domain Policy
DomainName       : PwSh.loc
Owner            : PWSH\Domain Admins
Id               : 31b2f340-016d-11d2-945f-00c04fb984f9
GpoStatus        : AllSettingsEnabled
Description      :
CreationTime     : 08/10/2019 13:23:27
ModificationTime : 08/10/2019 12:29:30
UserVersion      : AD Version: 0, SysVol Version: 0
ComputerVersion  : AD Version: 3, SysVol Version: 3
WmiFilter        :

DisplayName      : Default Domain Controllers Policy
DomainName       : PwSh.loc
Owner            : PWSH\Domain Admins
Id               : 6ac1786c-016f-11d2-945f-00c04fb984f9
GpoStatus        : AllSettingsEnabled
Description      :
CreationTime     : 08/10/2019 13:23:27
ModificationTime : 09/10/2019 14:10:34
UserVersion      : AD Version: 0, SysVol Version: 0
ComputerVersion  : AD Version: 3, SysVol Version: 3
WmiFilter        :

DisplayName      : Blank_GPO
DomainName       : PwSh.loc
Owner            : PWSH\Domain Admins
Id               : f9b91603-9642-4327-b07a-8699e776c907
GpoStatus        : AllSettingsEnabled
Description      : First GPO Blank
CreationTime     : 28/12/2019 15:21:10
ModificationTime : 28/12/2019 15:21:10
UserVersion      : AD Version: 0, SysVol Version: 0
ComputerVersion  : AD Version: 0, SysVol Version: 0
WmiFilter        :
```
If you want you can search them by **Name** or by **GUID** 

# 1.2 Create GPO with Powershell

With the powershell `set-GPRegistryValue` command you had the option to set up the **registry** keys for your GPO. Ok but how!!
The settings of the **set-GPRegistryValue** cmdlet
* **1.**	The **[-Guid]** parameter corresponds to the `GUID` of the group strategy.
* **2.**	The **[-Key]** setting specifies the registry key for the focus policy setting based on the attention register:  The key must be in one of the following two registry hives:
* **3.**	**HKEY_LOCAL_MACHINE** (`HKLM`) for a registry-based policy setting in Computer Configuration.
* **4.**	**HKEY_CURRENT_USER** (`HKCU`) for a registry-based policy setting in the user's configuration.
* **5.**	The **[-ValueName]** setting specifies a value name or a value name table for the registry-based policy setting.
* **6.**	The **[-Server]** setting specifies the domain controller on which the code will be run.
* **7.**	The **[-Type]** setting specifies the type of data for the registry-based policy setting.
* **8.**	The acceptable values for this setting are: `Channel`, `ExpandString`, `Binary`, `DWord`, `MultiString`,  `QWord`

To facilitate the creation of group strategies with PowerShell an open source project created by **Roger Zander [MVP]** is available it is based on the ADMX administration files of the version of Windows 10 1803. The address is  https://pspeditor.azurewebsites.net/

![Auhtors_img](/img/PPE1.PNG)

We will create several GPOs:
-	Windows Update for WSUS
-	LAPS
-	Secure computer configuration

> CAUTION
> When you use the [site](https://pspeditor.azurewebsites.net/) becarefull all the registry Path contain HKLM:\Software\Policies.... you need to replace to HKLM\Software\Policies without the **:**.


Let's only detail the GPO for WSUS

# 2.0 Action GPO

## 2.1 Windows Update GPO

For example, we're going to create a Windows Update group policy and we're going to set up that group policy with PowerShell:
* **1.**	Open a Windows PowerShell command prompt as an administrator.
* **2.**	In the website cited above browse the following objects Computers Configuration, Windows Components,  Windows Update  then  Automatic Updates detection frequency.
* **3.**	Determine the options for settings, Enabled  and  DetectionFrequency with a value of 22. In the PowerShell section, we get the keys to the registry to be modified.
* **4.**	Create group strategy with New-GPO command

![Auhtors_img](/img/GPO16.PNG)

Now we can define the registry value with the command **set-GPRegistryValue** : 
```powershell
Set-GPRegistryValue -Name "Windows Update" -Key "HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU" -ValueName "DetectionFrequencyEnabled" -Value 1 -Type DWord
Set-GPRegistryValue -Name "Windows Update" -Key "HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU" -ValueName "De-tectionFrequency" -Value 22 -Type DWord

```
![Auhtors_img](/img/GPO17.PNG)

## 2.2 LAPS GPO

You need to install the Laps Admx file on your Domain Controler and becarefull if you have create a Central Store you need to copy **Admx** and **Adml** file the `Central store Folder` ;).

![Auhtors_img](/img/PPE2.PNG)

PowerShell Time : 
```powershell
New-GPO -Name "LAPS_IT"

# Enable local admin password management
Set-GPRegistryValue -Name "LAPS_IT" -Key "HKLM\Software\Policies\Microsoft Services\AdmPwd" -ValueName 'AdmPwdEnabled' -Value 1 -Type Dword

#Do not allow password expiration time longer than required by policy
Set-GPRegistryValue -Name "LAPS_IT" -Key "HKLM\Software\Policies\Microsoft Services\AdmPwd" -ValueName 'PwdExpirationProtectionEnabled' -Value 1 -Type Dword
```
![Auhtors_img](/img/GPO18.PNG)



## 2.2 Secure computer configuration

Now we add some example for configure a computer in my school
* **1.** **User Configuration** > *Control panel* > *personalization* > Prevent changing desktop background
* **2.** **User Configuration** > *Control panel* > *personalization* > Prevent changing desktop icons
* **3.** **User Configuration** > *Desktop* > *Desktop Wallpaper* > Desktop Wallpaper
* **4.** **User Configuration** > *Desktop* > *Desktop Wallpaper* > Remove Recycle Bin icon from desktop

PowerShell Time : 
```powershell
New-GPO -Name "Secure_computer"

# Prevent changing desktop background
Set-GPRegistryValue -Name "Secure_computer" -Key 'HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\ActiveDesktop'   -ValueName 'NoChangingWallPaper' -Value 1   -Type Dword

#Do Prevent changing desktop icons
Set-GPRegistryValue -Name "Secure_computer" -Key "HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\System" -ValueName 'NoDispBackgroundPage' -Value 1 -Type Dword

# Desktop Wallpaper
Set-GPRegistryValue -Name "Secure_computer" -Key "HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\System" -ValueName 'WallpaperStyle' -Value 0 -Type Dword
Set-GPregistryValue -Name "Secure_computer" -Key "HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\System" -ValueName 'Wallpaper' -Value '\\SRV1\Share1$\wallpaper.jpg' -Type ExpandString

#Disable Remove Recycle Bin icon from desktop
Set-GPregistryValue -Name "Secure_computer" -Key "HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\NonEnum" -ValueName '{645FF040-5081-101B-9F08-00AA002F954E}' -Value '0' -Type Binary

```
To verify with the Group Policy console : 

![Auhtors_img](/img/GPO19.PNG)

# 3.0 Find Registry value into a GPO

The cmdlet `Get-GPregistryValue` have the same paramter Like `Set-GPregistryValue` how to use it.
```powershell
Get-GPRegistryValue -Name "LAPS_IT" -Key "HKLM\Software\Policies\Microsoft Services\AdmPwd" -ValueName AdmPwdEnabled
````
![Auhtors_img](/img/GPO20.PNG)

But this poses us some problem we do not have the opportunity to discover the keys use in a GPO without knowing the final keys but voila everything is possible with powershell.

```powershell
function Recurse-PolicyKeys{
 [CmdletBinding()]
 param(
   [Parameter(Mandatory=$true)]
   [string]$GPOName,

   [Parameter(Mandatory=$true)]
   [string]$Key
 )
 $current = Get-GPRegistryValue -Name $gpoName -Key $key
 foreach ($item in $current){
   if ($item.ValueName -ne $null){
   [array]$returnVal += $item 
 }
   else{
     Recurse-PolicyKeys -Key $item.fullkeypath -gpoName $gpoName
   }
 }
 return $returnVal
}
```
Let's try this  :

```powershell
PS C:\Users\Administrator> Recurse-PolicyKeys -GPOName "Secure_computer" -Key "HKCU\Software"


KeyPath     : Software\Microsoft\Windows\CurrentVersion\Policies\ActiveDesktop
FullKeyPath : HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\ActiveDesktop
Hive        : CurrentUser
PolicyState : Set
Value       : 1
Type        : DWord
ValueName   : NoChangingWallPaper
HasValue    : True

KeyPath     : Software\Microsoft\Windows\CurrentVersion\Policies\NonEnum
FullKeyPath : HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\NonEnum
Hive        : CurrentUser
PolicyState : Set
Value       : {0}
Type        : Binary
ValueName   : {645FF040-5081-101B-9F08-00AA002F954E}
HasValue    : True

KeyPath     : Software\Microsoft\Windows\CurrentVersion\Policies\System
FullKeyPath : HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\System
Hive        : CurrentUser
PolicyState : Set
Value       : 1
Type        : DWord
ValueName   : NoDispBackgroundPage
HasValue    : True

KeyPath     : Software\Microsoft\Windows\CurrentVersion\Policies\System
FullKeyPath : HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\System
Hive        : CurrentUser
PolicyState : Set
Value       : 0
Type        : DWord
ValueName   : WallpaperStyle
HasValue    : True

KeyPath     : Software\Microsoft\Windows\CurrentVersion\Policies\System
FullKeyPath : HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\System
Hive        : CurrentUser
PolicyState : Set
Value       : \\SRV1\Share1$\wallpaper.jpg
Type        : ExpandString
ValueName   : Wallpaper
HasValue    : True
```
Unbelievable, Powershell is really awesome 😉 ! !

# 4.0 Linked them to an OU or not...

Of course all GPOs must be linked to an OU that contains either Computer objects or user objects. For this nothing very difficult there is a cmdlet for doing this: `New-GPLink`.

```powershell
Get-GPO -Name "Secure_computer" | New-GPLink -target "OU=Lyon,OU=Sites,DC=PwSh,DC=loc" -LinkEnabled Yes


GpoId       : 4ca77301-6c21-4820-acb4-2938b4488453
DisplayName : Secure_computer
Enabled     : True
Enforced    : False
Target      : OU=Lyon,OU=Sites,DC=PwSh,DC=loc
Order       : 1
```

In the next part, I will speak about Backup GPO and how to verify if a GPO is apply on computers ;).

Thank's for reading.


Written by Jérôme Bezet-Torres @JM2K69.
