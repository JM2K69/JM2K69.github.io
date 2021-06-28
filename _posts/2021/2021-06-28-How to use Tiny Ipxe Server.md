---
layout: single
title: "How to user Tiny Ipxe Server"
date: 2021-06-28
tags: 
  - OSD
  - IPxe
  - MDT
  - 'Microsoft Deployment Toolkit'
  - OSDCLOUD
  - MEMCM
categories:
  - Powershell
  - OSD
published: true
comments: true
author_profile: true
header:
  teaserlogo:
  teaser: ''
  image: img/headers/mountain03_1920x500.jpg
  caption:
gallery:

  - image_path: ''
    url: ''
    title: ''
toc: true
toc_sticky: true
toc_label: "Table of content"
---


I'm back, today we are going to set up a PXE boot but using a Tiny PXEServer software that allows us to use and set up an Ipxe server on Windows and that quite easily. 

# How to Find the Project

You can find the original project [here](https://github.com/erwan2212/tinypxeserver). With this solution you can boot over what you want iSCSI,HTTP,HTTPS,SMB.... in this case I only Boot over HTTP.


# 2 How to setup to use it.

## 2.1 The **first step** is to expand the `Zip File`

![FriendsofMDT](/img/Ipxe.png){: .align-center}

* There is some important **Files**

1. the main executable file 🤣 of course
1. The `config` file
1. and a file filter.txt for security.


{: .notice--success}
This content is only alls files are necessary to setup my lab.

## 2.2 The main Application

The Tiny Pxe Server is in 1.0.0.23 at the begin the Server contain some services and deamon.
* DHCP server
* HTTP server
* DNS server

When it start i ttry to find some informations like the `@IP` of the server where it run and it populate all the configuration. you can **switch** his `Status` with the two buttons **Online** and **OffLine**.

![FriendsofMDT](/img/Ipxe2.png){: .align-center}

I use this solution on many environements :

1. Standalone MDT,
1. Windows Deployment Services with MDT,
1. PSD the PowerShell Extension for MDT ping `@mikael_nystrom` and `@jarwidmark`,
1. OSDCloud ping `@SeguraOSD`,
1. With MEMCM `@sandy_tsang` wrote and blog post how to use it with SCCM [link](https://sandyzeng.com/ipxe-sccm/)

## 2.2 The Config files

My `config.ini` File look like this:

```ini
[dhcp]
; for start at Launch
start=1
proxydhcp=0
; Start Http and DNS
httpd=1
bind=1
; The root directory
root=Files
; File to loads
filename=ipxe.pxe
altfilename=wimbootM.ipxe
binl=0
dnsd=0
smb=0
; DHCP configuration
opt1=255.255.255.0
opt3=10.0.0.254
opt6=10.0.0.200
opt15=JM2K69.loc
opt28=10.0.0.255
opt43=0
opt51=3600
opt54=10.0.0.200
next-server=10.0.0.200
poolstart=10.0.0.201
poolsize=10
;Depend on Client Architecture I send different files
[arch]
00006=ipxe-i386.efi
00007=ipxe-x64.efi
00009=ipxe-x64.efi
[frmDHCPServer]
top=164
left=1007
;Web Port 8080 because It's on my PSD webServer
[web]
port=8080
```

With **ipxe** you can create the rules here an example for my PSD server :
```bash
#!ipxe

set boot-url http://${next-server}:8080

 cpuid --ext 29 && set arch x64 || set arch x86
  kernel ${boot-url}/wimboot quiet
  initrd ${boot-url}/BOOT/BCD         BCD
  initrd ${boot-url}/BOOT/BOOT.SDI    BOOT.SDI
  initrd ${boot-url}/SOURCES/PSD/${buildarch}/BOOT.WIM BOOT.WIM
  boot

 :unknown
 echo Unknown platform ${platform}_${buildarch}
```
With this file **wimboot.ipxe** there is no menu and in my root directory we need to have some files. An other sample with a menu.

```ini
#!ipxe

set boot-url http://${next-server}:8080
set menu-timeout 5000
set submenu-timeout ${menu-timeout}

isset ${menu-default} || set menu-default exit
cpuid --ext 29 && set arch x64 || set arch x86

goto Konsole_${platform}_${buildarch} || goto unknown2

:Konsole_efi_x86_64
console --picture ${boot-url}/menu.png 

:Konsole_pcbios_i386
echo No console available now
console --picture ${boot-url}/menu.png 

:unknown2
goto start

######## MAIN MENU ###################
:start
menu IPXE @JM2K69 for PSD
item
item --gap -- ------------------------- PSD Boot ------------------------------------------
item wimboot Boot  HTTP for PSD x64
item wimboot2 Boot HTTP MDT x64
item wimboot3 Boot HTTP OSDCLOUD x64 ;)
item exit       Exit 
item --gap -- ------------------------------ Advanced -------------------------------------
item config       Configure settings
item shell      Enter iPXE shell
item reboot     Reboot
choose --default wimboot --timeout 30000 target && goto ${target}

:wimboot
 cpuid --ext 29 && set arch x64 || set arch x86
  kernel ${boot-url}/wimboot quiet
  initrd ${boot-url}/BOOT/BCD         BCD
  initrd ${boot-url}/BOOT/BOOT.SDI    BOOT.SDI
  initrd ${boot-url}/SOURCES/PSD/${buildarch}/BOOT.WIM BOOT.WIM
  boot
  
:wimboot2
 cpuid --ext 29 && set arch x64 || set arch x86
  kernel ${boot-url}/wimboot quiet
  initrd ${boot-url}/BOOT/BCD         BCD
  initrd ${boot-url}/BOOT/BOOT.SDI    BOOT.SDI
  initrd ${boot-url}/SOURCES/MDT/${buildarch}/BOOT.WIM BOOT.WIM
  boot

:wimboot3
 cpuid --ext 29 && set arch x64 || set arch x86
  kernel ${boot-url}/wimboot quiet
  initrd ${boot-url}/BOOT/BCD         BCD
  initrd ${boot-url}/BOOT/BOOT.SDI    BOOT.SDI
  initrd ${boot-url}/SOURCES/OSDCLOUD/${buildarch}/BOOT.WIM BOOT.WIM
  boot


:reboot
reboot

:config
config
goto start

:shell
echo Type exit to get the back to the menu
shell
set menu-timeout 0
goto start

```

### 2.2.1 Necessary files and directory

![FriendsofMDT](/img/Ipxe3.png){: .align-center}


* **Files**

1. **ipxe.pxe** is the file heart of Ixpe solution
1. **ipxe-386.efi** or **ipxe-x64.efi** is the loader with the OC Client architecture,
1. ipxe-undionly.kpxe, it will be use when you d'on't know the client and the drivers to boot up in iPXE.
1. **wimboot** it's the same file in **2pintSoftware** 2pxe server it allow to load wimFiles and boot on. this version is the latest is digitally signed.
1. **menu.png** my wallpaper.
1. One or many **ipxe file** for rules.
 
* **Folders** and **Files** 

1. On **boot** folder I need to copy form an ISO of Wnindows 10 BCD file and boot.sdi
1. On **efi** folder i create two subfolder **Microsoft/Boot** and i copy the same BCD file.
1. On **source** fodler you can organize your files.


![FriendsofMDT](/img/Ipxe4.png){: .align-center}
![Auhtors_img](/img/Git1.png){: .align-center}

My repo is on Github [here](https://github.com/JM2K69/Tiny_iPXE.git)


Thank's for reading.🤗

Written by Jérôme Bezet-Torres @JM2K69.
