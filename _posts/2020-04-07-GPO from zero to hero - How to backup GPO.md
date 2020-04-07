---
layout: post
title: "GPO from zero to hero - How to backup GPO"
date: 2020-04-07
tags: [PowerShell,GPO, Active, Directory, Backup ]
published : True
---


During my last [post](https://jm2k69.github.io/2020-02-07-GPO-from-zero-to-hero-GPO-and-PowerShell/) we talked about GPO in an Active Directory domain and how to create and link GPO with `PowerShell`.

The backup of computer data is one of the most sensitive elements of the training system. Information that is considered invaluable candies appear in a very short period. For various reasons such as bad manipulations, property damage or malfunctions from outside the network.
In order to preserve the integrity of data regarding the configuration of group strategies, the GPMC offers the ability to save and restore GPO.

Today we will focus on the Backup of GPO and how to restore them in Active Directory.

# 1. How to backup GPO with GUI

The first thing is to open the MMC console of GPO management and go to the `Container` Group Policy Objects and open the Menu and click on **Back Up All..**

![Auhtors_img](/img/GPOBack1.PNG)

After must enter an empty folder to Backup GPO list this for example.

![Auhtors_img](/img/GPOBack2.PNG)

![Auhtors_img](/img/GPOBack3.PNG)

Finnaly your are backup all your GPO in the directory you're the `Boss` or not yet 😉.

But if you go into the directory in which you perform the backup and very difficult to interpret with the GUID right??

![Auhtors_img](/img/GPOBack4.PNG)

So I'll see with the console MMC I must have more luck to find my GPO..

![Auhtors_img](/img/GPOBack5.PNG)

And Yes I found them....

![Auhtors_img](/img/GPOBack6.PNG)

But how to make this better with `PowerShell`

# 2. Powershell Module

What are the powershell commands to do these operations...

```powershell
Get-Command -Module GroupPolicy

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-GPO                                         1.0.0.0    GroupPolicy
Cmdlet          Get-GPO                                            1.0.0.0    GroupPolicy
Cmdlet          Restore-GPO                                        1.0.0.0    GroupPolicy
```
With yes 3 cmdlets if I count correctly ;-))

# 3. Actions

## 3.1 Backup-GPO

We have the option to complete or individually back up the GPO of an Active Directory domain with the PowerShell **Backup-GPO** command.
*The details of the command:*

**Backup-GPO** {**-All** ou **[-Name]** <string> ou **-Guid** <Guid>} **-Path**  
<string> [**-Comment** <string>] [**-Domain** <string>] [**-Server** <string>]

* The **-All** setting is used to specify the backup of all the GPO in the domain.
* The **-Path** setting is used to inform the location of the backup.
* The **-Comment** setting allows you to add a comment.
* The **-Name** setting allows you to specify the name of the object to be saved.
* The setting **-Guid** allows you to specify the GUID of the object to be saved.

One example to how to use the Backup-GPO cmdlet.

```powershell
PS C:\Users\JM2K69\> Backup-GPO -Path c:\backupGPO -All


DisplayName     : Default Domain Policy
GpoId           : 31b2f340-016d-11d2-945f-00c04fb984f9
Id              : 6c7f7ce0-7b80-46e3-92ac-56bbd7ede04d
BackupDirectory : c:\backupGPO
CreationTime    : 07/04/2020 09:54:16
DomainName      : PwSh.loc
Comment         :

DisplayName     : Secure_computer
GpoId           : 4ca77301-6c21-4820-acb4-2938b4488453
Id              : d588e94e-78c2-4e79-9d5c-3ef80f4fef8e
BackupDirectory : c:\backupGPO
CreationTime    : 07/04/2020 09:54:17
DomainName      : PwSh.loc
Comment         :


DisplayName     : Default Domain Controllers Policy
GpoId           : 6ac1786c-016f-11d2-945f-00c04fb984f9
Id              : f61fb31d-8288-4e44-b013-5d8581d17018
BackupDirectory : c:\backupGPO
CreationTime    : 07/04/2020 09:54:17
DomainName      : PwSh.loc
Comment         :

DisplayName     : Blank_GPO
GpoId           : f9b91603-9642-4327-b07a-8699e776c907
Id              : 3c723e31-fffd-4220-810e-135c9528900f
BackupDirectory : c:\backupGPO
CreationTime    : 07/04/2020 09:54:18
DomainName      : PwSh.loc
Comment         :
```
Yep the result is the same when I browse the folder **BackupGPO**

![Auhtors_img](/img/GPOBack7.PNG)

I must be able to do better with **PowerShell**

With a Powershell script I'll be able to have a history of GPO.

```powershell
$AllGPOs = Get-GPO -All
foreach ($GPO in $AllGPOs) {
  $pattern = '[^a-zA-Z0-9\s]'
  $DisplayName = $GPO.DisplayName -replace $pattern,''
  $ModificationTime = $GPO.ModificationTime -replace '[/:]','_'
  $ModificationTime = $ModificationTime -replace ' ','_'
  $BackupDestination = 'c:\GPO_Backup\' + $DisplayName + '\' + $ModificationTime + '\'
  if (-Not (Test-Path $BackupDestination)) {
      New-Item -Path $BackupDestination -ItemType directory | Out-Null
      Backup-GPO -Name $GPO.DisplayName -Path $BackupDestination

      }
  }
```
If I launch the script here look is awesome...

```powershell
DisplayName     : Default Domain Policy
GpoId           : 31b2f340-016d-11d2-945f-00c04fb984f9
Id              : 8e8aa0c1-8df6-4034-ac7a-bf42d24e1a04
BackupDirectory : c:\GPO_Backup\Default Domain Policy\07_04_2020_10_04_10\
CreationTime    : 07/04/2020 10:04:28
DomainName      : PwSh.loc
Comment         :
```
Yes today I just modify my **Default Domain Policy** but in the folder **GPO_Backup** What does it look like?

![Auhtors_img](/img/GPOBack8.PNG)

More details let's look at the default domain policy.

![Auhtors_img](/img/GPOBack8b.PNG)

In this folder we view that the Default Domain Policy has been modified Three times Today the **07/04/2020** and the **27/01/2020** two times in one hour the same day.

## 3.1 Restore-GPO

The **Restore-GPO** command allows us to perform a unit restoration or a complete restoration of all the group policy objects saved.
Restore-GPO **-All** **-Path** <string> **[-Domain <string>]** **[-Server <string>]** 

* The **-All** setting is used to specify the backup of all the GPO in the domain.
* The **-Path** setting is used to inform the location of the backup.
* The **-Name** setting allows you to specify the name of the object to be saved.
* The setting **-Guid** allows you to specify the GUID of the object to be saved.

Now how to restore my Default Domain Policy to the **27/01/2020** very simple.

```powershell
PS C:\Users\JM2K69\Downloads> Restore-GPO "Default Domain Policy" -Path 'C:\GPO_Backup\Default Domain Policy\27_01_2020_15_36_26\'


DisplayName      : Default Domain Policy
DomainName       : PwSh.loc
Owner            : PWSH\Domain Admins
Id               : 31b2f340-016d-11d2-945f-00c04fb984f9
GpoStatus        : AllSettingsEnabled
Description      :
CreationTime     : 08/10/2019 13:23:27
ModificationTime : 07/04/2020 10:39:03
UserVersion      : AD Version: 1, SysVol Version: 1
ComputerVersion  : AD Version: 9, SysVol Version: 9
WmiFilter        :

```
Unbelievable, Powershell is really awesome 😉 ! !

In the next part, I will speak about how to compare GPO and how to verify if a GPO is apply on computers ;).

Thank's for reading.

Written by Jérôme Bezet-Torres @JM2K69.
