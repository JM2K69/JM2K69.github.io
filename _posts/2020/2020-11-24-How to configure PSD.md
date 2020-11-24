---
layout: single
title: "Cloud Deployment with PSD"
date: 2020-11-24
tags: 
  - Powershell
  - VMware
  - 'Microsoft Deployment Toolkit'
  - PSD
categories:
  - Powershell
  - Operating System
published: true
comments: true
author_profile: true
header:
  teaserlogo:
  teaser: ''
  image: img/headers/turbo.jpg
  caption:
gallery:

  - image_path: ''
    url: ''
    title: ''
toc: true
toc_sticky: true
toc_label: "Table of content"
---

In my the first post I describe the infrastructure now let go with the configuration.

![FriendsofMDT](/img/PSD1.png)

 
# 1.Configuration PSD

## 1.1 TaskSequence

* You need ton import you Operating System in your MDT server.

![FriendsofMDT](/img/PSD9.PNG)

{: .notice--warning} Your OS and your ADK must use the same version like 19041 for example. if not there is a problemn to activate BITS in WinPe and in your Image. Your image must be Entreprise or Pro version to support **BITS**

* **Create** 2PintSoftware Group
* **Create** the Variable **BranchCacheEnabled**  

![FriendsofMDT](/img/PSD22.PNG)

* **Enable** BranchCache
```csharp
BCEnabler.exe Enable %_SMSTSMDataPath%\BCCache 2 1337
```

* **Create** the Variable **SMSTSDownloadProgram**  

![FriendsofMDT](/img/PSD23.PNG)

{: .notice--info} The BITSACP program is the heart, it is the one that will initiate the downloads of the sources from your server using **BITS**.

* **Create** another 2PintSoftware Group after the Step configure to `Move` the BranchChache to the OS volume
```csharp
BCEnabler.exe Move %OSVolume%:
```

![FriendsofMDT](/img/PSD21.PNG)


## 1.2 Add Drivers

My environement is a POC only so I decide to add some driver aproximativly 600 Mo

I make this structure for my drivers.

![FriendsofMDT](/img/PSD10.PNG)

And I all your fake or not driver 😁.

![FriendsofMDT](/img/PSD11.PNG)

![FriendsofMDT](/img/PSD12.PNG)

the script create a Archive and it copy it in the **PSDResources\DriverPackages** like this.

![FriendsofMDT](/img/PSD13.PNG)

## 1.3 OSDToolKit

you need to download the free Tool form the 2pintSoftware website [Link](https://2pintsoftware.com/products/osd-toolkit/)

You need to creat a account after you can download the software.

![FriendsofMDT](/img/PSD14.PNG)

To Activate th full power for OSDToolkits you need to buy a licence and support, just the free look's very good.

Now we need to configure your DeploymentShare :

* Extract your OSDToolkit archive
* Copy to the folder

![FriendsofMDT](/img/PSD15.PNG)

* Modify a the powershell file **PSDUpdateExit.ps1**, you need to uncomment the OSDToolkit part.

![FriendsofMDT](/img/PSD16.PNG)

>Like this 😉.

![FriendsofMDT](/img/PSD17.PNG)

>this script was call by MDT to enable **BITS** in your WinPe and for OS.

## 1.4 Config CS.ini and BootStrap

You need to make some change in your Files

```ini
[Settings]
Priority=Default
Properties=MyCustomProperty

[Default]
OSInstall=Y
SkipCapture=YES
SkipAdminPassword=NO
SkipProductKey=YES
BranchCacheEnabled=YES
SMSTSDownloadProgram=BITSACP.EXE
OSDToolkitImageName=Windows 10 Enterprise x64 2004
```

{: .notice--success} In the CS.ini you must add **BranchCacheEnabled**, **SMSTSDownloadProgram** and **OSDToolkitImageName** variables.

Becarefull with the **OSDToolkitImageName** you need to add the value for your OS. In my case *Windows 10 Enterprise x64 2004*.

```ini
[Settings]
Priority=Default

[Default]
DeployRoot=https://mdt01.jm2k69.loc/mdtproduction
UserId=BuildAccount
UserPassword=P@ssw0rd
UserDomain=MDT01
```

## 1.5 Now Update your deploymentShare

For troobleshooting if your config is ok in the folder **E:\MDTProduction\PSDResources\Plugins\OSDToolkit** a log file must be present. 

![FriendsofMDT](/img/PSD18.PNG)

If you have a PXE server WDS or 2PXE or with the ISO you can start 2 VM for me to test it.

Before the First deployment you can publish BanchCache Content with this line :

```powershell
# Disable Windows Defender real-time scanning
Set-MpPreference -DisableRealtimeMonitoring $true

# Create BC hashes for the MDT Production share
Publish-BCWebContent -Path E:\MDTProduction -Recurse -Verbose
```

# 2.0 Test

In my Lab I created 2 VM on Workstation 16
* 2 vCPU
* 8 Gb RAM
* 60 Go DD on SSD Nvme
* 1 vNic 

{: .notice--warning} BranchCache will be limited by the environnement because I use VM with real PC the result must be greatter.

* First VM
![FriendsofMDT](/img/PSD19.PNG)

* Second VM
![FriendsofMDT](/img/PSD20.PNG)

I will make a short video for OSDToolkit in VMware Workstation soon. 🤗

Thank's for reading.

Written by Jérôme Bezet-Torres @JM2K69.
