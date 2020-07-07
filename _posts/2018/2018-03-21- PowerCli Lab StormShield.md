---
layout: single
title: "PowerCli Lab StormShield"
date: 2018-03-21
tags: 
  - Powershell
  - StormShield
  - Mahapps
categories:
  - Powershell
  - 'PowerCli'
  - WPF
  - VMware
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


 [Preview on Youtbe]( https://youtu.be/ApKuclem_rQ?t=31s)
 
In order use PowerCli Lab StormShield you need install some things like `PowerCli`  and we must have vSphere 6.x or higher environnement.

* TOC
{:toc}

# 1. PowerCli

With the recent release of PowerCli for update your PowerCli version form PowerShell Gallery you can use this command :
```powershell
PS C:\> Update-Module VMware.PowerCli
```

And if you want to install PowerCli In a PowerShell prompt you can install.

``` powershell 
PS C:\> Install-Module VMware.PowerCli -Scope CurrentUser
```

When you use PowerCli in environnement if you don't personnalize your certificat you must configure `PowerCli` setting to find your active configuration you can use the **PowerCli** command `get-PowerCLIConfiguration`
![ComputerSection](/img/Powercli1.PNG)

Now to configure you must use  `set-PowerCLIConfiguration`

```powershell
PS C:\> Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
```

![ComputerSection](/img/Powercli2.PNG)

# 1. StormShield

I am an IT teacher and with our academy we followed a training on StormShield products to discover and deepen StormShield products. We took the CSNA and CSNE training.
To allow other IT teachers in the Lyon Academy to set up workshops on these technologies, I decided to create a PowerCli script to deploy our workshops on our vSphere farm.

I imagined two scenarios :

* The first scenario deploys a complete StormShield kit for each student or group of students. The Kit is composed of several VMs a company A, a Company B and a firewall trainer. Each company consists of an SNS Firewall and a Debian Client machine. Here is the network diagram. All firewalls are interconnected by a management network in red. Each of the firewalls has a DMZ and they are all connected to each other in a LAN FWs. And only the VM trainer have access to the Internet.

![ComputerSection](/img/StormShield1.png)

* The second scenario deploys a business for each student and everyone works together, the goal being that all students can communicate with each other, according to rules and the teacher has the couple VM Trainer to give them access to the Internet.


## 1.1How implement in vSphere

For the ***First*** scenario we create a lot of **VLAN** and all VM are deploy with a prefix **G1** for the firts student or group of student.

For the ***Second*** scenario we create a lot af VLAN also and all VM are deploy a suffix **_A** for the compagny **A**.



# 2.The Script

The features of the script :

![ComputerSection](/img/StromShield_PowerCli.gif)

* Connected to VMware vSphere 
* Save Credential
* Auto-Login
* Deploy Scenario 1
* Deploy Scenario 2
* Clean Scenario 1
* Clean Scenario 2
* All VM and PortGroup with Tagged with Scenario1 or 2 in the Category StormShield more easy to find and Clean after.
* In the OVA Directory you must put all OVA not included
* The Script Generate a config file for create **VLAN** in physical switch cisco

![ComputerSection](/img/Cisco1.PNG)

* The Script Generate a config file for remove **VLAN** in physical switch cisco

![ComputerSection](/img/Cisco2.PNG)

* Calculate the ressources need for the scenario.

  ![ComputerSection](/img/StromShield_PowerCli1.gif)

  [Download Script]( https://github.com/JM2K69/PowerCLI-Lab-StormShield)

Written by Jérôme Bezet-Torres @JM2K69.
