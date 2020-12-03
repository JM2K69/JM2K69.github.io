---
layout: single
title: "Appliance as code aka AAC with Packer"
date: 2020-12-03
tags: 
  - VMware
  - HomeLab
categories:
  - Powershell
  - 'Editions ENI'
published: fasle
comments: true
author_profile: true
header:
  teaserlogo:
  teaser: ''
  image: img/headers/Code01_1920x5001.jpg
  caption:
gallery:

  - image_path: ''
    url: ''
    title: ''
toc: true
toc_sticky: true
toc_label: "Table of content"
---


For my tools VMware Lab, building your **VMware vSphere 7 Lab** in less than **40 min**.😀
Why a DNS server beacause if you want to learn DSC for `VMware` you need to have a DNS server to make this.

# 1 First Try

My first try was to build a photonOs appliance in version 3 to make a DNS server.

* Download the PhotonOs OVA from the github repository
* Connect to ssh and configure it
* Cleanup and export to OVF
* Convert to OVA

It work's but too heavy to build and maintain.

# 2 The automation with Packer

I decide to make my appliance as code with Packer.

## 2.1 Requirements

* Linux client I install Ubuntu 20.04
* PowerShell 7
* VMware.PowerCli powershell 
* vCenter Server or Standalone ESXi host 6.x or greater
* [VMware OVFTool 4.4.1 (x86)](https://my.vmware.com/group/vmware/downloads/details?downloadGroup=OVFTOOL441&productId=974)
* [Packer 1.6.4+ (x86)](https://www.packer.io/intro/getting-started/install.html)


## 2.2 Clone the git repository

```
git clone https://github.com/JM2K69/Photon-DNS-Appliance.git
```

## 2.3 Edit the `photon-builder.json` file to configure the vSphere endpoint for building the PhotonOS appliance

```
{
  "builder_host": "192.168.30.10",
  "builder_host_username": "root",
  "builder_host_password": "VMware1!",
  "builder_host_datastore": "vsanDatastore",
  "builder_host_portgroup": "VM Network"
}
```

**Note:** If you need to change the initial root password on the PhotonOS appliance, take a look at `photon-version.json` and `http/photon-kickstart.json`. When the OVA is produced, there is no default password, so this does not really matter other than for debugging purposes.

## 2.4 Start the build by running the build script which simply calls Packer and the respective build files

```bash
./build.sh or ./build_Photon4.sh 
``` 

{: .notice--success}
When you run the script ./build.sh or ./build_Photon4.sh we use the **provider vmware-iso** and not **vsphere-iso**, you need to enable SSH on your host ESXI.

{: .box--warning}
Enable **“Guest IP Hack”**
Run the following command on the ESXi host:

```bash
esxcli system settings advanced set -o /Net/GuestIPHack -i 1

```
if you want to try my appliances:

* PhontonOS 3.0 [Photon-DNS-appliance 0.1.1](https://mega.nz/file/zIcHlAjA#IYWH9Tq8QxcMXnoBVU1sawsUm3IIIDL6JHKa-sPdBiM)
*  PhontonOS 4.0 beta [Photon-DNS-appliance 0.2.0](https://mega.nz/file/zVcTHKJS#ImhNGHYPPZ4RUTIQQrRbmBMX6_Q6S9JCcfKeTUSkfaY)

![Auhtors_img](/img/Git1.png){: .align-center}

My repo is on Github [here](https://github.com/JM2K69/Photon-DNS-Appliance.git)


Thank's for reading.🤗

Written by Jérôme Bezet-Torres @JM2K69.
