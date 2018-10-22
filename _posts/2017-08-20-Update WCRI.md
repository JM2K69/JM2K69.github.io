---
layout: post
title: "Update WCRI"
date: 2017-08-20
tags: [PowerShell,Scripting, MDT, Sapien ]
---



What is new in this release

* New Interface
* Remove unwanted Windows 10 Apps
* Import Update to MDT DeploymentShare
* Import Reference Image to MDT DeploymentShare
* Now you can choose all work folder.
	* Folder for donwload updates
	* Folder for work on Offline WIM file
	* Folder for reference Image 

	
![ComputerSection](/img/WCRI_INT.PNG)


# Index

* TOC
{:toc}
# Download

The link for the download is [here]( https://github.com/JM2K69/WCRI/blob/master/Create%20Reference%20Image%20MDT.exe)


### 1. Remove unwanted Windows 10 Apps

We remove all unwanted apps for windows 10 but some apps are in a `whitelist`

* Microsoft.DesktopAppInstaller
* Microsoft.Messaging
* Microsoft.StorePurchaseApp
* Microsoft.WindowsCalculator
* Microsoft.WindowsCommunicationsApps
* Microsoft.WindowsSoundRecorder
* Microsoft.WindowsStore

During the process the log for removing Windows 10 unwanted Apps aren't log into the main windows log but into the log file in the directory.


![ComputerSection](/img/WCRI_W10.PNG)



### 2. MDT Import

Now you can Import your `Update`, Operating Systems like your reference image newly create into a MDT Folder. To make this the first thing is to choose your DeploymentShare$ folder into the `MDT Tabs Options`

![ComputerSection](/img/WCRI_MDT.PNG)


Written by Jérôme Bezet-Torres @JM2K69.