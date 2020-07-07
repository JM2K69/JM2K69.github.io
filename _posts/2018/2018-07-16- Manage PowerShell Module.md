---
layout: single
title: "Manage PowerShell Module"
date: 2018-07-16
tags: 
  - Powershell
  - 'Modules Powershell'
  - XAML
categories:
  - Powershell
  - 'Material Design'
  - WPF
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



There are PowerShell modules that ship with Windows 10 that weren’t installed from the PowerShell Gallery using PowerShellGet so they can’t be updated using the Update-Module cmdlet. This also applies for any modules that you’ve installed manually yourself.

This tool build with XAML in WPF is a Powershell script with a GUI. You can easy update module for different scope `AllUsers` or `CurrentUser`. 

* TOC
{:toc}


# 1.  The Application

The application use an HamburgerMenu with 3 sections.

![ComputerSection](/img/WUM1.PNG)

1. Installed
2. Updated
3. Install Pwsh Module

## 1.1 Installed Part

In this part you can find all `Powershell Module` installed on your host, by click on the button `Find-InstallModule` and we can find the number of the module install.

![ComputerSection](/img/WUM2.PNG)


## 1.2 Update Part

In this part by **Default** when you Click on `Find-Module` the script search for All installed module if an update is available on PowerShell Gallery and it compare all version for the Scope `CurrentUser`.

![ComputerSection](/img/WUM3.PNG)

If you want to Update the module for `AllUsers` you must change the setting with the Toggle Button. We must launch again the Application with Administrator right!

![ComputerSection](/img/WUM4.PNG)
![ComputerSection](/img/WUM5.PNG)

And for `AllUsers` the result i here :

![ComputerSection](/img/WUM6.PNG)

In this `Update Part` you can actually update all module in future release you will be able to update only selected module.

## 1.3 Installed Part

In this part you have the possibility to install modules powershell with the desired version for the Scope `CurrentUser`. You can find The Author and the description for the module. There is one restriction in the `Module Name` you can't use wilcard characters.

![ComputerSection](/img/WUM7.PNG)

The project is available on my github profile.The link for the download is [here]( https://github.com/JM2K69/Powershell_WPF_GUI/tree/master/Projects/Module%20Pwsh%20Management).

Written by Jérôme Bezet-Torres @JM2K69.