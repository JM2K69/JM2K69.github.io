---
layout: single
title: "Custom Wizard Hydration Release"
date: 2017-04-26
tags: 
  - Powershell
  - 'Active Directory'
categories:
  - Powershell
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

Today the script is available to allow the complete customization of the Hydration Kit SCCM CB and others.

This is post cover : 
* How to use the PowerShell Script 

# The prerequisites ?

All applications in your Microsoft Deployment Toolkit must be run on ever system and they must be autonomous.

Custom Wizard Hydration :  [CWH.ps1]( https://github.com/JM2K69/Custom-Wizard-Hydration/archive/1.2.zip)


**1. Insert Step**

You must insert a new step in your Task Sequence :

* Copy the PowerShell Script **CWH.PS1** in your DeployementShare Script Folder

* Click on **ADD**

  * General => Run PowerShell Script

  * PowerShell Script enter : **_%SCRIPTROOT%\CWH.ps1_**

  * Change the step Name to : **_Custom Wizard Hydration_** 

    ​

![ComputerSection](/img/HowTo1.PNG)

You must modify all task Sequence in your environement.

``` 
Update 24/04/07 : In order to run PowerShell script during WinPe phase,You must enable Features on your MDT Media or MDT settings to your DeploymentShare
```



![ComputerSection](/img/HowTo3.PNG)




# Customsetting.ini

In all Hydration Kit made by **_Johan Arwidmark_**  you must change all customsetting.ini for all Task Sequence. For each virtual machine, there is an INI file that populates the machine-specific information, they are located in the Deploy\Control\ directory of the DVD or USB bootable they are named in the following way :

*CustomSettings_"**ID Tasksequence**".ini



![ComputerSection](/img/HowTo2.PNG)



Here the link to download Hydration Kit For Windows Server 2016 an ConfigMgr Curent and Technical Preview Branch :[link](http://deploymentresearch.com/Research/Post/580/Hydration-Kit-For-Windows-Server-2016-and-ConfigMgr-Current-Technical-Preview-Branch)


The customsettings.ini file contains all the necessary rules and all the information for a task sequence. Therefore, modify the customsettings.ini file so that the **CWH.ps1** script to work properly.




## Sample Customsetting_DC01.ini

```ini
[Settings]
Priority=Default
Properties=HydrationOSDComputerName,HydraTimerSec,VMTOOLS

[Default]
HydraTimerSec = 20
VMTOOLS=NO
HydrationOSDComputerName=DC01
JoinWorkgroup=WORKGROUP
OSDAdapterCount=1
OSDAdapter0DNSServerList=127.0.0.1
OSDAdapter0Gateways=192.168.1.1
OSDAdapter0IPAddressList=192.168.1.200
OSDAdapter0SubnetMask=255.255.255.0
OSDAdapter0TCPIPNetBiosOptions=1

; Active Directory Configuration
ReplicaOrNewDomain=Domain
NewDomain=Forest 
NewDomainDNSName=corp.viamonstra.com
DomainNetBiosName=VIAMONSTRA 
ForestLevel=4 
DomainLevel=4 
AutoConfigDNS=Yes 
ConfirmGC=Yes 
CriticalReplicationOnly=No 
;DatabasePath=D:\NTDS 
;ADDSLogPath=D:\NTDS
;SysVolPath=D:\SYSVOL 
SafeModeAdminPassword=123+aze
SiteName=NewYork

; DHCP Configuration
DHCPServerOptionRouter=192.168.1.1 
DHCPServerOptionDNSServer=192.168.1.200 
DHCPServerOptionDNSDomainName=corp.viamonstra.com
DHCPScopes0SubnetMask=255.255.255.0 
DHCPScopes0IP=192.168.1.0 
DHCPScopes0Name=192.168.1.0/24 
DHCPScopes0StartIP=192.168.1.100 
DHCPScopes0EndIP=192.168.1.199 
DHCPScopes0OptionLease=691200 
DHCPScopes=1 
```



The _HydraTimerSec_ **Property** corresponds to the time from which we have the opportunity to press the **_modify_** button. The property VMTOOLS is if you run the Hydration Kit in VMware products like vSphere or Workstation it install VMware Tools during the **TaskSequence**.




# Demo
Here a quick video with a MDT stanalone with the [Custom Wizard Hydration](https://youtu.be/d-ZSLJmL9Mg) script. 

