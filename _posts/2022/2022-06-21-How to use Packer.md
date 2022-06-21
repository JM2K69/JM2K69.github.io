---
layout: single
title: "Packer how to use it"
date: 2022-06-21
tags: 
  - Automation
  - PowerShell
  - IAC
  - Packer
categories:
  - Iac
published: true
comments: true
author_profile: true
header:
  teaserlogo:
  teaser: ''
  image: img/headers/terraform-1500x500.jpg
  caption:
gallery:

  - image_path: ''
    url: ''
    title: ''
toc: true
toc_sticky: true
toc_label: "Table of content"
---

# 1.Introduction

**Packer** is an `open-source tool` that allows the **standardization** of our images for several platforms with a single `template`. It allows us a simple and declarative *automation* to create our reference images or our **golden images**.

![IAC](/img/Iac.png){: .align-center}

# 2. How to install

It comes in the form of a single executable available on several platforms.

|Platforms  |Architecture  |
|---------|---------|
|MacOs     |  `Amd64` `Arm64`       |  
|Windows    |   `386` `Amd64`      |
|Linux     |      `386` `Amd64` `Arm` `Arm64`    |  

{: .notice--info}
A small particularity for the Windows world it will be necessary to add an environment variable to specify the path of the binary

# 3.Which Format

For the format to use it is recommended to use the new **HCL** format for `Hashicorp Configuration Language`. It is **highly** recommended to use it, however the **JSON** format remains available but for how long.

* An HCL sample

```terraform
source "amazon-ebs" "main" {
  ami_name = "main-ami"
}

<BLOCK TYPE> "<BLOCK LABEL>" "<BLOCK LABEL>" {
  # Block body
  <IDENTIFIER> = <EXPRESSION> # Argument
}

```

* An JSON Sample

```json
{
  "type": "amazon-ebs",
  "access_key": "...",
  "secret_key": "..."
}
```

The advantage of using the **HCL** language which is supported by packer since version 1.6 is the fact that `HCP Packer` uses the **HCl** format to be subsequently interpreted by **Terraform** which uses this same format.

# 4.Teminology

There are a handful of terms used throughout the Packer. Luckily, there are relatively few. 

* **Artifacts** are the results of a single build, and are usually a set of IDs or files to represent a machine image. Every builder produces a single artifact. As an example, in the case of the **Amazon EC2 builder**, the artifact is a set of AMI IDs (one per region). For the **VMware builder**, the artifact is a directory of files comprising the created virtual machine.

* **Builds** are a single task that eventually produces an image for a single platform. `Multiple builds run in parallel`. Example usage in a sentence: "The Packer build produced an AMI to run our web application." Or: "Packer is running the builds now for **VMware**, **AWS**, and **VirtualBox**."

* **Builders** are components of Packer that are able to create a machine image for a single platform. Builders read in some configuration and use that to run and generate a machine image. A builder is invoked as part of a build in order to create the actual resulting images. Example builders include **VirtualBox**, **VMware**, and **Amazon EC2**.

* **Data Sources** are components of Packer that fetch data from outside Packer and make it available to use within the template. Example of data sources include Amazon AMI, and Amazon Secrets Manager.

* **Post-processors** are components of Packer that take the result of a builder or another post-processor and process that to create a new artifact. Examples of post-processors are compress to compress artifacts, upload to upload artifacts, etc.

* **Provisioners** are components of Packer that install and configure software within a running machine prior to that machine being turned into a static image. They perform the major work of making the image contain useful software. Example provisioners include shell scripts, **Chef**, **Puppet**,**PowerShell**, **Command Line**, **Scripts**. etc.

* **Templates** are either `HCL` or `JSON` files which define one or more builds by configuring the various components of Packer. Packer is able to read a template and use that information to create multiple machine images in parallel.

# 3.Packer and Windows

Create your own VMware vSphere Template with Packer.

With packer we have to separate as much as possible our information in several files that will contain the necessary information, connected to our private or public cloud environment.

We will have the following files:
 * **vSphere.pkvars.hcl** => credential for my private cloud and settings.
 * **build.pkvars.hcl** => the default user for all my builds
 * **common.pkvars.hcl** => the default setting to export my **artifacts** , content Library , Template, OVA or OVF File

* vSphere.pkvars.hcl

```terraform
/*
    DESCRIPTION:
    VMware vSphere variables used for all builds.
    - Variables are use by the source blocks.
*/

// vSphere Credentials
vsphere_endpoint            = "SCO-LABO-VCSA"
vsphere_username            = "packer_vsphere@domain.loc"
vsphere_password            = "P@$$w0rd!VMware123!."
vsphere_insecure_connection = false

// vSphere Settings
vsphere_datacenter = "Datacenter"
vsphere_cluster    = "SCO_LABO"
vsphere_datastore  = "FREENAS1_VOL1"
vsphere_network    = "Packer_DHCP"
vsphere_folder     = "Packer Sample"
```
* build.pkvars.hcl
```terraform
/*
    DESCRIPTION:
    Build account variables used for all builds.
    - Variables are passed to and used by guest operating system configuration files (e.g., ks.cfg, autounattend.xml).
    - Variables are passed to and used by configuration scripts.
*/

// Default Account Credentials
build_username           = "3ICSAdmin"
build_password           = "3Ics@dmin!."
build_password_encrypted = "$6$jqoxlQyW2ROxsLK4$P3gGp2Bf0FCSLAZTNaE/LZBqSsvVAYZNPNwTehXZbMAhRJdKOjNqqyUWVUTwql9CnhHUlpiluWzHlR2DpUKD4/"
```
* common.pkvars.hcl
```terraform
/*
    DESCRIPTION:
    Common variables used for all builds.
    - Variables are use by the source blocks.
*/

// Virtual Machine Settings according to your environnement
common_vm_version           = 16
common_tools_upgrade_policy = true
common_remove_cdrom         = true

// Template and Content Library Settings
common_template_conversion         = true
common_content_library_name        = "3ICS_Library"
common_content_library_ovf         = true
common_content_library_destroy     = true
common_content_library_skip_export = false

// Removable Media Settings
common_iso_datastore = "FREENAS2_Vol1"

// Boot and Provisioning Settings
common_data_source      = "http"
common_http_ip          = null
common_http_port_min    = 8000
common_http_port_max    = 8099
common_ip_wait_timeout  = "20m"
common_shutdown_timeout = "15m"
```
And now one sample for Windows Server 2022

```terraform
/*
    DESCRIPTION:
    Microsoft Windows Server 2022 template using the Packer Builder for VMware vSphere (vsphere-iso).
*/

//  BLOCK: packer
//  The Packer configuration.

packer {
  required_version = ">= 1.8.0"
  required_plugins {
    vsphere = {
      version = ">= v1.0.4"
      source  = "github.com/hashicorp/vsphere"
    }
  }
  required_plugins {
    windows-update = {
      version = ">= 0.14.1"
      source  = "github.com/rgl/windows-update"
    }
  }
}

//  BLOCK: locals
//  Defines the local variables.

locals {
  build_by      = "Built by: HashiCorp Packer ${packer.version}"
  build_date    = formatdate("YYYY-MM-DD hh:mm ZZZ", timestamp())
  build_version = formatdate("YY.MM", timestamp())
  manifest_date = formatdate("YYYY-MM-DD hh:mm:ss", timestamp())
  manifest_path = "${path.cwd}/manifests/"
}

//  BLOCK: source
//  Defines the builder configuration blocks.

source "vsphere-iso" "windows-server-standard-core" {

  // vCenter Server Endpoint Settings and Credentials
  vcenter_server      = var.vsphere_endpoint
  username            = var.vsphere_username
  password            = var.vsphere_password
  insecure_connection = var.vsphere_insecure_connection

  // vSphere Settings
  datacenter = var.vsphere_datacenter
  cluster    = var.vsphere_cluster
  datastore  = var.vsphere_datastore
  folder     = var.vsphere_folder

  // Virtual Machine Settings
  guest_os_type        = var.vm_guest_os_type
  vm_name              = "${var.vm_guest_os_family}-${var.vm_guest_os_name}-${var.vm_guest_os_version}-${var.vm_guest_os_edition_standard}-${var.vm_guest_os_experience_core}-v${local.build_version}"
  firmware             = var.vm_firmware
  CPUs                 = var.vm_cpu_sockets
  cpu_cores            = var.vm_cpu_cores
  CPU_hot_plug         = var.vm_cpu_hot_add
  RAM                  = var.vm_mem_size
  RAM_hot_plug         = var.vm_mem_hot_add
  cdrom_type           = var.vm_cdrom_type
  disk_controller_type = var.vm_disk_controller_type
  storage {
    disk_size             = var.vm_disk_size
    disk_thin_provisioned = var.vm_disk_thin_provisioned
  }
  network_adapters {
    network      = var.vsphere_network
    network_card = var.vm_network_card
  }
  vm_version           = var.common_vm_version
  remove_cdrom         = var.common_remove_cdrom
  tools_upgrade_policy = var.common_tools_upgrade_policy
  notes                = "Version: v${local.build_version}\nBuilt on: ${local.build_date}\n${local.build_by}"

  // Removable Media Settings
  iso_paths    = ["[${var.common_iso_datastore}] ${var.iso_path}/${var.iso_file}", "[] /vmimages/tools-isoimages/${var.vm_guest_os_family}.iso"]
  iso_checksum = "${var.iso_checksum_type}:${var.iso_checksum_value}"
  cd_files = [
    "${path.cwd}/scripts/${var.vm_guest_os_family}/",
    "${path.cwd}/certificates/"
  ]
  cd_content = {
    "autounattend.xml" = templatefile("${abspath(path.root)}/data/autounattend.pkrtpl.hcl", {
      build_username       = var.build_username
      build_password       = var.build_password
      vm_inst_os_language  = var.vm_inst_os_language
      vm_inst_os_keyboard  = var.vm_inst_os_keyboard
      vm_inst_os_image     = var.vm_inst_os_image_standard_core
      vm_inst_os_kms_key   = var.vm_inst_os_kms_key_standard
      vm_guest_os_language = var.vm_guest_os_language
      vm_guest_os_keyboard = var.vm_guest_os_keyboard
      vm_guest_os_timezone = var.vm_guest_os_timezone
    })
  }

  // Boot and Provisioning Settings
  http_port_min    = var.common_http_port_min
  http_port_max    = var.common_http_port_max
  boot_order       = var.vm_boot_order
  boot_wait        = var.vm_boot_wait
  boot_command     = var.vm_boot_command
  ip_wait_timeout  = var.common_ip_wait_timeout
  shutdown_command = var.vm_shutdown_command
  shutdown_timeout = var.common_shutdown_timeout

  // Communicator Settings and Credentials
  communicator   = "winrm"
  winrm_username = var.build_username
  winrm_password = var.build_password
  winrm_port     = var.communicator_port
  winrm_timeout  = var.communicator_timeout

  // Template and Content Library Settings
  convert_to_template = var.common_template_conversion
  dynamic "content_library_destination" {
    for_each = var.common_content_library_name != null ? [1] : []
    content {
      library     = var.common_content_library_name
      description = "Version: v${local.build_version}\nBuilt on: ${local.build_date}\n${local.build_by}"
      ovf         = var.common_content_library_ovf
      destroy     = var.common_content_library_destroy
      skip_import = var.common_content_library_skip_export
    }
  }
}

//  BLOCK: build
//  Defines the builders to run, provisioners, and post-processors.

build {
  sources = [
    "source.vsphere-iso.windows-server-standard-core",
  ]

  provisioner "powershell" {
    environment_vars = [
      "BUILD_USERNAME=${var.build_username}"
    ]
    elevated_user     = var.build_username
    elevated_password = var.build_password
    scripts           = formatlist("${path.cwd}/%s", var.scripts)
  }

  provisioner "powershell" {
    elevated_user     = var.build_username
    elevated_password = var.build_password
    inline            = var.inline
  }

  provisioner "windows-update" {
    pause_before    = "30s"
    search_criteria = "IsInstalled=0"
    filters = [
      "exclude:$_.Title -like '*VMware*'",
      "exclude:$_.Title -like '*Preview*'",
      "exclude:$_.Title -like '*Defender*'",
      "exclude:$_.InstallationBehavior.CanRequestUserInput",
      "include:$true"
    ]
    restart_timeout = "120m"
  }

  post-processor "manifest" {
    output     = "${local.manifest_path}${local.manifest_date}.json"
    strip_path = true
    strip_time = true
    custom_data = {
      build_username           = var.build_username
      build_date               = local.build_date
      build_version            = local.build_version
      common_data_source       = var.common_data_source
      common_vm_version        = var.common_vm_version
      vm_cpu_cores             = var.vm_cpu_cores
      vm_cpu_sockets           = var.vm_cpu_sockets
      vm_disk_size             = var.vm_disk_size
      vm_disk_thin_provisioned = var.vm_disk_thin_provisioned
      vm_firmware              = var.vm_firmware
      vm_guest_os_type         = var.vm_guest_os_type
      vm_mem_size              = var.vm_mem_size
      vm_network_card          = var.vm_network_card
      vsphere_cluster          = var.vsphere_cluster
      vsphere_datacenter       = var.vsphere_datacenter
      vsphere_datastore        = var.vsphere_datastore
      vsphere_endpoint         = var.vsphere_endpoint
      vsphere_folder           = var.vsphere_folder
      vsphere_iso_path         = "[${var.common_iso_datastore}] ${var.iso_path}/${var.iso_file}"
    }
  }
}
```

{: .notice--info}
you will notice that in the HCL file above all the values are variables and are stored in a separate file. you also need a file that will describe all the variables with their types.

Finally it is now possible to automate the creation of your templates within your private or public cloud environment

On the next part I will speak about Packer and how to use it.

Thank's for reading.🤗

Written by Jérôme Bezet-Torres @JM2K69.
