---
layout: post
title: "GPO from zero to hero - GPO Structure"
date: 2020-01-02
tags: [PowerShell,Active Directory, GPO ]
published : true
---


During my last [post](https://jm2k69.github.io/2019-11-06-GPO-from-zero-to-hero/) we talked about GPO in an Active Directory domain, today we will move on to concrete things to discover the structure of a GPO and why not create our first GPO.

# 1. What are the files that compose it?
In pre-Windows **Server** 2008/2008 R2 and Vista/7 versions, ADM files (administration models) were the origin at the configurable administration models in group policies. Changes in registry values made by `ADM files` had a syntax that was difficult to understand and change. If administrators wanted to customize additional registry values to those windows provided by default (standard ADM files), it was mandatory to create a custom ADM file, and this required learning the syntax of the programming language.

The ADM files were recorded in the directory of the group policy in which it was created, adding to the size of the group about 4MB. The replication of domain controllers was significantly compounded by this procedure resulting in a known failure called ***Sysvol bloat***.
From Windows Server 2008 and Windows Vista, a new feature in managing administration models appears: files in **ADMX** and **ADML** format.
**ADMX files** are successors to *ADM files* found in earlier versions of Windows.

ADMX files are designed to generate the registry changes required by setting administration models in group policies. For ADML files they represent the display of information and understandable text explanation of the action of a **GPO**. Clearly for a GPO we need an **ADMX file** that holds the `registry keys` has changed and an **AMDL file** that gives information or indication on are use and configuration.


## 1.1	The ADMX file

ADMX files are the elements that contain the policy settings to be set in the group policy management console under the node Computer and User – Administrative Template. 
Each ADMX file corresponds to a policy setting for a specific application.

ADMX files containing registry configuration settings are stored in the directory **%systemroot%\PolicyDefinitions** (C:\Windows\PolicyDefinitions) domain controllers and workstations.

![Auhtors_img](/img/GPO6.PNG)

Here the structure for and **ADMX file**:

![Auhtors_img](/img/GPOp2.PNG)


## 1.2	The ADML file 

Each ADMX file involves creating an ADML file. ADML files are stored in a sub-repertory of the **PolicyDefinitions directory** (C:\Windows\PolicyDefinitions), in the directory of the corresponding language, **en-US** (fr-FR for french).

The ADM files didn't take language into account. ADMX files benefit from their ADML file, which allows you to display the group policy setting in the desired language. Thus, by creating a sub-repertory in the directory **%systemroot%\PolicyDefinitions** and then copying the ADML files in French in this directory, if the French language is present on the operating system, the GPO settings can be viewed and modified in French.

![Auhtors_img](/img/GPOp3.PNG)


> An ADMX file is always accompanied by the associated ADML file. ADMX files define the policy settings to be changed based on the registry. The associated ADML file defines the language parameters of the ADMX file.
The group policy editor searches for the file.adml whose name is identical to that of the .admx file. The policy object editor displays an error message when one of the two items is missing.

There are several **ADMX files** available by default in Windows Server versions or in customer operating systems. It is possible to download ADMX files from the Internet.
The last option is to generate these files yourself if none of the default or downloadable files meet your expectations or needs.
The opportunity to generate your own ADMX files greatly expands the network's customization capabilities. Administrators can define what creative needs are for business models and thus do what is necessary to deploy them to network positions.

When creating ADMX files, you need to know the basics of XML file structures as well as the construction scheme for The ADMX and ADML files.
Once these files are created and tested, they must be integrated into the central store to increase the number of administration models available.

>Don't forget to create a custom core file when multiple custom administration templates are deployed within your business.

Before the Central store when you edit an GPO your are like this :

![Auhtors_img](/img/GPO8.PNG)


# 2.  The central store

So far the **ADMX** and **ADML** files are located on each domain controller and on each domain administrator position. Let's say that an administrator wants to create or customize an ADMX or ADML file, this setting can only be changed on the post on which the file or files were modeled.

The ***central store*** is one of the new features that appeared with Windows Server 2008 and 2008 R2, it allows a centralization of administration models in the **SYSVOL folder**. 

To benefit from the central store, it must be created. Microsoft recommends creating the central store on the domain controller that hosts the PDC emulator role. Additional domain controllers in the store will pick up the central store with Active Directory replication.

## 2.1	Creation of the central store

The creation of the central store must be done manually in the file explorer of the domain controller that hosts with the role **PDC**, if the masters of operations have not been moved on other domain controllers. You must connect to the first domain controller that hosts the three masters of operations in this case.

- Connect to the first domain controller with an account with domain administration rights and open the Windows Explorer.
- Create a new folder name with the name PolicyDefinitions in the folder  **C:\Windows\SYSVOL\sysvol\domain name\Policies**. For our example,  **C:\Windows\SYSVOL\sysvol\pwsh.lok\Policies.**
- ADML language files must be stored in a policy definitions sub-repertory. There is a naming agreement for each language.
- Go to the PolicyDefinitions folder and create a sub-repertory fr-FR.

## 2.2 Fill the central store

Once the central store is created, it can hold the ADMX files you have. You can use several methods to increment and update the central magasin.
However, we will present one of the simplest and most effective methods. It's simply a matter of copying the ADMX files directly into the central store's folder :

**\\DC\SYSVOL\nom du domain\Policies\PolicyDefinitions**

Whichever copy method is used, manual copying, scripted or otherwise, be sure to copy ADML files into the language directory corresponding to the ADMX file.

![Auhtors_img](/img/GPO9.PNG)


# 3.External sources of business models

**ADMX files** correspond to the administration models available in the group strategy management editor. The default delivery models with Windows Server 2019 are numerous but not comprehensive. You can download ADMX models from sources outside **Windows**.

## 3.1 Downloading ADMX files from outside

Many websites offer additional administrative models for managing your network. The sources of downloads are multiple, and it is difficult to ensure the integrity of the files. It is necessary to test them before integrating them into production.

## 3.2 Administration models for Microsoft Office 2019 and Office 365

Microsoft offers many administration templates to download in order to customize Microsoft Office for network users.
Once the administration models are downloaded to servers or administration stations, simply integrate these files into the central store of domain controllers to use them.
Donwload [Link](https://www.microsoft.com/en-us/download/details.aspx?id=49030 ) 

When we open the GPMC console and publish a group strategy, the source of the administration models has changed:

**For the Computer Section :**

![Auhtors_img](/img/GP11.PNG)

**For the User Section :**

![Auhtors_img](/img/GP12.PNG)

# 4. Create your first GPO with Powershell

## 4.1 Find powershell command

Easy with this powershell command

```powershell
Get-command -module GroupPolicy
```
![Auhtors_img](/img/GP13.PNG)

## 4.2 Create you first blank GPO

```powershell
New-GPO -Name "Blank_GPO" -Comment "First GPO Blank"
```
***Wohoo*** it's so easy

![Auhtors_img](/img/GP14.PNG)

If you want to verify you can open the console **gpmc.msc**

![Auhtors_img](/img/GP15.PNG)

Thank's for reading, in the next part I will talk about all **PowerShell** command to **managed** and **create** and **backup** GPO.

Written by Jérôme Bezet-Torres @JM2K69.
