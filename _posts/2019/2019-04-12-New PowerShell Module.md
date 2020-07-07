---
layout: single
title: "New PowerShell Module for update your Powershell modules"
date: 2019-04-12
tags: 
  - Powershell
  - Modules
  - Updates
categories:
  - Powershell
  - 'Powershell Gallery'
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

I decide ton convert  an `WPF` application [here](https://jm2k69.github.io/2018-07-24-Manage-PowerShell-Module-Update/) into a powershell module for update your powershell module installed in your local machine.

# 1.How to install it 

For install the module you can use this powershell command :
```powershell
PS> Install-Module -name UpdatePwshModule
```
> This release  works with `Powershell 5.1`  


## 1.2 Function available

```powershell
PS C:\Users\JM2K69> Get-Command -Module FreeNas

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        Start-UpdatePwshModule                             1.4        UpdatePwshModule
```

## 1.2 Parameter available

If you want to update all powershell for the `Current User` you must run :

```powershell
PS C:\Users\JM2K69> Start-UpdatePwshModule -Scope CurrentUser
```

If you want to update all powershell for the `AllUsers` you must run because we need Administrative right on the **PC** to update module in the path `C:\Program Files\WindowsPowerShell\Modules`:

```powershell

PS C:\Users\Jay> Start-UpdatePwshModule -Scope AllUsers
```
`This command run the WPF application with Admin Right ! !

# 2. The Application

* Installed Part

![Installed Part](/img/Img1.PNG) 

* Update Part

![Powershellv6.1.3](/img/Img2.PNG)

* Install Powershell module Part

![Powershellv6.1.3](/img/Img3.PNG)


Thanks for reading.

Written by Jérôme Bezet-Torres @JM2K69.
