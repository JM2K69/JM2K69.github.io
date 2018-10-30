---
layout: post
title: "Custom Hydration Wizard WPF"
date: 2018-10-30
tags: [PowerShell,Forms, WPF,XAML,Mahapps]
---
<!-- TOC -->

- [1. Remember Hydration Kit](#1-remember-hydration-kit)
- [2. Windows Form Application](#2-windows-form-application)
- [3. WPF Application](#3-wpf-application)
    - [3.1  Part VM Info](#31--part-vm-info)
    - [3.2 Part Computer](#32-part-computer)
    - [3.3 Part Networking](#33-part-networking)
    - [3.4 Part Active Directory](#34-part-active-directory)
    - [3.5 Part DHCP](#35-part-dhcp)
- [4. How to use my Script](#4-how-to-use-my-script)
    - [4.1 Download the script](#41-download-the-script)
    - [4.2 Use with MDT](#42-use-with-mdt)
- [5. Demo Time](#5-demo-time)

<!-- /TOC -->

I decide to refresh all my `Windows Forms` project into `WPF Application`, I start with my first Application **Custom Hydration Wizard**

# 1. Remember Hydration Kit

In all Hydration Kit made by **_Johan Arwidmark_**  you must change all customsetting.ini for all Task Sequence. For each virtual machine, there is an INI file that populates the machine-specific information, they are located in the `Deploy\Control\ directory` of the DVD or USB bootable they are named in the following way :

* CustomSettings_"**ID Tasksequence**".ini

![ComputerSection](/img/HowTo2.PNG)

With This Application you can personnalize all the Value during the `Tasksequence`. Be carefull if you want to make this all the MDT application can use this modificiation, they must be fully variable.

# 2. Windows Form Application

![ComputerSection](/img/Full.PNG)

To create the application I use `PowerShell Studio`. I decide to modernize the Interface with Mahapps and more easy to maintain.
If you don't have the Software you don't maintain it.

# 3. WPF Application

I use Mahapps to create this modern Application with XAML. In this version I remove the countdown, now you can modify value when you want.

## 3.1  Part VM Info

![ComputerSection](/img/CWHN1.PNG)

## 3.2 Part Computer

![ComputerSection](/img/CWHN2.PNG)

## 3.3 Part Networking

![ComputerSection](/img/CWHN3.PNG)

## 3.4 Part Active Directory

![ComputerSection](/img/CWHN4.PNG)

## 3.5 Part DHCP

![ComputerSection](/img/CWHN5.PNG)

# 4. How to use my Script

## 4.1 Download the script

The script is available in my github repository [Here](https://github.com/JM2K69/Custom-Wizard-Hydration/releases/download/1.3/CWH.zip).

**Please don't forget to Unblock Files** 

To use it you can first `Unblock File ` or you use the Apps from **[MVP] Damien Van Robaeys** [here](http://www.systanddeploy.com/2018/08/unblock-your-downloaded-files-with.html) ;-) ...

```powershell
get-ChildItem -recurse | Unblock-File
```
Be carefull if you use the latest ADK 1809 there is some problem with `WPF` in `WinPe` there is some workaround : 
* Apply October CU to your WinPe 1809 only : [Here](https://twitter.com/SeguraOSD/status/1050596674807054336)
* Copy 1 file from ADK 1803 into your Boot.wim [Here](https://twitter.com/ferozekhan267oa/status/1049169276656074753)

## 4.2 Use with MDT

* **First** you need to extract the Archive copy the content of the Folder **WPF Application** to your `Script Folder`

![ComputerSection](/img/Folder_CWH.PNG)


* **Second** For all your Taskquence you need to add `One` **Action**.

    * Add Powershell Run Script
        * %SCRIPTROOT%\CWH\Run_CWH.ps1

    **_The content of the script_** :

```powershell
powershell.exe -sta -executionpolicy Bypass -file CWH.ps1
```

![ComputerSection](/img/CWHN_TS.PNG)


# 5. Demo Time

![ComputerSection](/img/CWH.gif)


Written by Jérôme Bezet-Torres @JM2K69.
