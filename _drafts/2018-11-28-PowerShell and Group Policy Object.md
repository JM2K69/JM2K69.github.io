---
layout: post
title: "2018-11-28-PowerShell and Group Policy Object"
date: 2018-11-28
tags: [PowerShell,GPO, Active Directory]
---

<!-- TOC -->autoauto- [1. Cmdlets](#1-cmdlets)auto- [2. Summary Part](#2-summary-part)auto- [3. How to download my Application](#3-how-to-download-my-application)auto- [4. Update 1809 ADK Problem](#4-update-1809-adk-problem)autoauto<!-- /TOC -->

Today we are working with `Powershell` and `Group Policy Objects{} to create from end to end a **GPO** in a ADDS Domain.

# 1. Cmdlets

what Powershell cmdlets do we need to manage `GPO`.

```powershell
PS C:\Users\Administrateur> Get-Command -Module GroupPolicy

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
Whe you use `New-GPO`, you create an empty Group Policy Object.

# 2. Summary Part

In this summary you can observe all the changes you have made with the Custom Hydration Wizard.

![ComputerSection](/img/CWHUpdate.gif)

# 3. How to download my Application

The script is available in my github repository [Here](https://github.com/JM2K69/Custom-Wizard-Hydration/releases/download/1.3/CWH.zip).

**Please don't forget to Unblock Files** 

To use it you can first `Unblock File ` or you use the Apps from **[MVP] Damien Van Robaeys** [here](http://www.systanddeploy.com/2018/08/unblock-your-downloaded-files-with.html) ;-) ...

```powershell
get-ChildItem -recurse | Unblock-File
```
# 4. Update 1809 ADK Problem

Be carefull if you use the latest ADK 1809 there is some problem with `WPF` in `WinPe` there is some workaround :

* [@SeguraOSD](https://twitter.com/SeguraOSD) Apply October CU to your WinPe 1809 only : [Here](https://twitter.com/SeguraOSD/status/1050596674807054336)
* [@SeguraOSD](https://twitter.com/ferozekhan267oa)Copy 1 file from ADK 1803 into your Boot.wim [Here](https://twitter.com/ferozekhan267oa/status/1049169276656074753)

Thanks to [@SeguraOSD](https://twitter.com/SeguraOSD) he make a very good job to find the real problem, it missing 2 Dll Files. You can find the blog post from is web site [here](https://www.osdeploy.com/blog/winpe-10-1809-wpf-dramarama). 

You can use a `PowerShell` script created by Nickolaj Andersen [MVP]. You can read the blog post a catch the `PowerShell Script` [Here](http://www.scconfigmgr.com/2018/11/23/fix-windows-adk-version-1809-issue-with-crashing-wpf-applications/)

![ComputerSection](/img/Fix.PNG)



Written by Jérôme Bezet-Torres @JM2K69.
