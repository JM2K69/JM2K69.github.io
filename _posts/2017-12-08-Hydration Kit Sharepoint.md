---
layout: post
title: "Hydration Kit Sharepoint 2016"
date: 2017-12-08
tags: [PowerShell, Active Directory, Scripting, MDT, Hydration Kit ]
---

Today I release the Hydration Kit Sharepoint Server 2016 with Windows Server 2016 fully variable for all applications



# Index

* TOC
{:toc}


# Hydration-Kits

All applications in your Microsoft Deployment Toolkit must be run on ever system and they must be autonomous.

------

**Hydration Kit Sharepoint 2016** with Server Windows Server 2016 and Windows 10 Entreprise for Client

 [Link to my Github Depot]( https://github.com/JM2K69/Hydrations-Kit/tree/master/HydraSharepoint)

------

With All my **Hydration Kit** the script include **Custom Wizard Hydration** project for more information [Project CWH.ps1]( https://jm2k69.github.io/2017-04-26-Custom-Wizard-Hydration-Release/) 

I'm working on the latest Hydration Kit with **Exchange 2016**.






# Virtual Machine



### Servers

This kit allows you to automatically deploy the below list of servers. The only server you really need to build first is DC01, since that’s the domain controller, and the **SHARE** server will join the domain. Turns out joining a domain is tricky without a domain controller :)

Main servers



- **DC01**. Windows Server 2016, DC, DHCP, CA and DNS

- **SHARE**. Windows Server 2016, SQL Server 2016 SP1, SharePoint Server 2016



### Client

- **PCA1**. Windows 10 Entreprise with Office 2016 Pro






Written by Jérôme Bezet-Torres @JM2K69.