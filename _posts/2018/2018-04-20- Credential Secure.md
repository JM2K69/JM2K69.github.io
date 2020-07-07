---
layout: single
title: "Credential Secure"
date: 2018-04-20
tags: 
  - Powershell
  - AES
categories:
  - Powershell
  - 'Mahapps'
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


How to Secure Credential to use it during Scripting **Powershell** or **PowerCli**. I use two functions `Export-CliXml` and `Import-CliXml`

![ComputerSection](/img/AES.gif)

# 1.  Export-CliXml

The Export-CliXml cmdlet creates an XML-based representation of an object or objects and stores it in a file. You can then use the Import-Clixml cmdlet to re-create the saved object based on the contents of that file

The Export-CliXml cmdlet encrypts credential objects by using the Windows Data Protection [API](http://msdn.microsoft.com/library/windows/apps/xaml/hh464970.aspx) . This ensures that only your user account can decrypt the contents of the credential object.

# 2.  Import-CliXml

The Import-CliXml cmdlet imports a CLIXML file with data that represents Microsoft .NET Framework objects and creates the objects in Windows PowerShell.

A valuable use of Import-CliXml is to import credentials and secure strings that have been exported as secure XML by running the Export-Clixml cmdlet

# 3. Increase security with AES 

During the process I decide to create an AES Key for encrypt password and it's export with `Export-CliXml`. How to create an AES Key with PowerShell.
```powershell
$Key = New-Object Byte[] 16   # You can use 16, 24, or 32 for AES
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($Key)
$Key
```

# 4. Secure Password AES

This Powershell application generate two Files the first file is the `AES.key` and the second `account_Creds.xml` contain the credential exported and the password is encrypted by the `AES.key`

![ComputerSection](/img/AES.png)

In the Object User I combined the username and the Host seperate with `#`.

To be sure that no information has been changed since their creation I recover their Hash and it is stored in the system registry in the location `Computer\HKEY_CURRENT_USER\Software\JM2K69\StormShield`


|Author  |Products  |
|---------|---------|
|JM2K69     | StormShield        |
|JM2K69     | vSphereLab        |
|JM2K69 |PhotonOS (DockerLab)|

![ComputerSection](/img/Hash.png)

If you want to use the source is on my [Github](https://github.com/JM2K69/Powershell_WPF_GUI/tree/master/Projects/AES)

Written by Jérôme Bezet-Torres @JM2K69.