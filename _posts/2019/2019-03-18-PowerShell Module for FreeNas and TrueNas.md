---
layout: single
title: "Powershell Module for FreeNas and TrueNas servers"
date: 2019-03-18
tags: 
  - Powershell
  - FreeNas
  - TrueNas
categories:
  - Powershell
  - Module
  - API
published: true
comments: true
author_profile: true
header:
  teaserlogo:
  teaser: ''
  #image: img/headers/Code01_1920x500.jpg
  caption:
gallery:

  - image_path: ''
    url: ''
    title: ''
toc: true
toc_sticky: true
toc_label: "Table of content"
---

At the end of February, I introduced my powershell module to automate the configuration of FreeNas and TrueNas servers using a Rest v1.0 API. The meetup **@FrPSUG** is available on [youtube](https://youtu.be/3OR143IPQ4o)

# 1.Module

This FreeNas powershell module covers for the moment the Storage part with the creation of volume as well as the part iSCSI. It is available on the PowerShellGallery.

> With this release It works with `Powershell 5.1`  and on `Powershell 6.x`

![Powershellv5.1](/img/FreeNas2.PNG) ![Powershellv6.1.3](/img/FreeNas1.PNG)

## 1.1 How to install

```powershell
PS> Install-Module -Name FreeNas
```

## 1.2 Function available

```powershell
PS C:\Users\Jay> Get-Command -Module FreeNas

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        Connect-FreeNasServer                              1.2        FreeNas
Function        Get-FreeNasDisk                                    1.2        FreeNas
Function        Get-FreeNasIscsiAssociat2Extent                    1.2        FreeNas
Function        Get-FreeNasIscsiConf                               1.2        FreeNas
Function        Get-FreeNasIscsiExtent                             1.2        FreeNas
Function        Get-FreeNasIscsiInitiator                          1.2        FreeNas
Function        Get-FreeNasIscsiPortal                             1.2        FreeNas
Function        Get-FreeNasIscsiSummary                            1.2        FreeNas
Function        Get-FreeNasIscsiTarget                             1.2        FreeNas
Function        Get-FreeNasIscsiTargetGroup                        1.2        FreeNas
Function        Get-FreeNasServer                                  1.2        FreeNas
Function        Get-FreeNasService                                 1.2        FreeNas
Function        Get-FreeNasStatus                                  1.2        FreeNas
Function        Get-FreeNasSystemAdvanced                          1.2        FreeNas
Function        Get-FreeNasSystemAlert                             1.2        FreeNas
Function        Get-FreeNasSystemEmail                             1.2        FreeNas
Function        Get-FreeNasSystemNTP                               1.2        FreeNas
Function        Get-FreeNasSystemUpdate                            1.2        FreeNas
Function        Get-FreeNasSystemVersion                           1.2        FreeNas
Function        Get-FreeNasVolume                                  1.2        FreeNas
Function        Get-FreeNasZvol                                    1.2        FreeNas
Function        Get-PowerShellVersion                              1.2        FreeNas
Function        New-FreeNasIscsiAssociat2Extent                    1.2        FreeNas
Function        New-FreeNasIscsiExtent                             1.2        FreeNas
Function        New-FreeNasIscsiInitiator                          1.2        FreeNas
Function        New-FreenasIscsiPortal                             1.2        FreeNas
Function        New-FreeNasIscsiTarget                             1.2        FreeNas
Function        New-FreeNasIscsiTargetGroup                        1.2        FreeNas
Function        New-FreeNasSystemNTP                               1.2        FreeNas
Function        New-FreeNasVolume                                  1.2        FreeNas
Function        New-FreeNasZvol                                    1.2        FreeNas
Function        Remove-FreeNasIscsiPortal                          1.2        FreeNas
Function        Reset-FreeNasSystemFactory                         1.2        FreeNas
Function        Restart-FreeNasServer                              1.2        FreeNas
Function        Set-FreeNasDedupZvol                               1.2        FreeNas
Function        Set-FreeNasIscsiConf                               1.2        FreeNas
Function        Set-FreeNasIscsiPortal                             1.2        FreeNas
Function        Set-FreeNasService                                 1.2        FreeNas
Function        Stop-FreeNasSystem                                 1.2        FreeNas
Function        Update-FreeNasSystem                               1.2        FreeNas
```

# 2.How to use

## 2.1 Connect to your server

```powershell
Connect-FreeNasServer -Server 10.0.10.0
  ______                               __  __           _       _             __  __
 |  ____|                             |  \/  |         | |     | |           /_ |/_ |
 | |__ _ __ ___  ___ _ __   __ _ ___  | \  / | ___   __| |_   _| | ___  __   __ | | |
 |  __| '__/ _ \/ _ \ '_ \ / _` / __| | |\/| |/ _ \ / _` | | | | |/ _ \ \ \ / / | | |
 | |  | | |  __/  __/ | | | (_| \__ \ | |  | | (_) | (_| | |_| | |  __/  \ V /| |_| |
 |_|  |_|  \___|\___|_| |_|\__,_|___/ |_|  |_|\___/ \__,_|\__,_|_|\___|   \_/ |_(_)_|

Your are already connect to 10.0.10.0
```

## 2.2 Some Function for find informations

```powershell
Get-Command -Module FreeNas -Verb Get
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        Get-FreeNasDisk                                    1.2        FreeNas
Function        Get-FreeNasIscsiAssociat2Extent                    1.2        FreeNas
Function        Get-FreeNasIscsiConf                               1.2        FreeNas
Function        Get-FreeNasIscsiExtent                             1.2        FreeNas
Function        Get-FreeNasIscsiInitiator                          1.2        FreeNas
Function        Get-FreeNasIscsiPortal                             1.2        FreeNas
Function        Get-FreeNasIscsiSummary                            1.2        FreeNas
Function        Get-FreeNasIscsiTarget                             1.2        FreeNas
Function        Get-FreeNasIscsiTargetGroup                        1.2        FreeNas
Function        Get-FreeNasServer                                  1.2        FreeNas
Function        Get-FreeNasService                                 1.2        FreeNas
Function        Get-FreeNasStatus                                  1.2        FreeNas
Function        Get-FreeNasSystemAdvanced                          1.2        FreeNas
Function        Get-FreeNasSystemAlert                             1.2        FreeNas
Function        Get-FreeNasSystemEmail                             1.2        FreeNas
Function        Get-FreeNasSystemNTP                               1.2        FreeNas
Function        Get-FreeNasSystemUpdate                            1.2        FreeNas
Function        Get-FreeNasSystemVersion                           1.2        FreeNas
Function        Get-FreeNasVolume                                  1.2        FreeNas
Function        Get-FreeNasZvol                                    1.2        FreeNas
Function        Get-PowerShellVersion                              1.2        FreeNas
```
For Example if you want to list which service are running on the `FreeNas Server` you can use the function
`Get-FreeNasService` the outpout will be like this.

```powershell
PS> Get-FreeNasService

id srv_enable srv_service      srv_state
-- ---------- -----------      ---------
 2      False afp              STOPPED
 4      False cifs             STOPPED
 5      False dynamicdns       STOPPED
 6      False ftp              STOPPED
 7      False iscsitarget      STOPPED
 9      False nfs              STOPPED
10      False snmp             STOPPED
11      False ssh              STOPPED
12      False tftp             STOPPED
14      False ups              STOPPED
16      False rsync            STOPPED
18       True smartd           STOPPED
20      False domaincontroller STOPPED
21      False lldp             STOPPED
22      False webdav           STOPPED
23      False s3               STOPPED
24      False netdata          STOPPED
```

For Example if you want to list Disk available on your `FreeNas Server` you can use the function
`Get-FreeNasDisk` the outpout will be like this.

```powershell
PS> Get-FreeNasDisk

Name Size_GB
---- -------
ada0      20
da0       30
da1       30
da2       30
da3       30
da4       30
da5       30
da6       30
```

# 3.Demo time

If you like virtualization and more particularly, you need to have a #HomeLab with VMware or Hyper-V, and if you need LUN you can use Freenas as NAS and with my powershell module you can now automate the creation of LUNs on your FreeNas and so with a little PowerCli automate the configuration of your ESXI.

> In this demo I use two VMs FreeNas with 7 disk of 30GB each with an ESXI NestedAppliance 6.7U1 Thx to William Lam([Nested Appliances](http://vmwa.re/nestedesxi)) `@Lamw` with 4 NIC

Here the [video](https://youtu.be/JpkEP9nkiN4) in my youtube Channel.
 
## 3.1 Description

* Connect to the server
* Create volumes on FreeNas
* Create **Zvol** volumes
* Configure deduplication on one **Zvol**
* Configure **iSCSI** sharing Block
    * Global configuration
    * Portail
    * Initiator
    * Extent
    * Association
* Starting **iSCSI** service
* **iSCSI** summary
* PowerCli
    * Connect ESXI
    * Create vSwitch with MTU 9000
    * Create vMkernel
        * Assign @IP
    * Enable **iSCSI** software component
    * Configure **iSCSI** Target
    * Create Datastore.

Thanks for reading.

Written by Jérôme Bezet-Torres @JM2K69.
