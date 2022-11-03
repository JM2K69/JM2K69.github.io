---
layout: single
title: "vSphereLab 8 and Terraform"
date: 2022-11-03
tags: 
  - Automation
  - PowerShell
  - IAC
  - Packer
  - Terraform
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

The objective in this post is simply to use Terraform to deploy learning environments for VMware vSphere **7.x** and **8** soon. To deploy VMware **vSphere** **Lab** with ESXI servers, a Vyos-based router that will serve as a DHCP server on both LANs and of course an internet access. For the DNS server I created an appliance based on **PhotonOs** and for the *iSCSI* Storage I used my **TrueNas Core** based appliance to make my iSCSI targets for my ESXI.

![IAC](/img/TerraformLab.PNG){: .align-center}

# 1. How to Find sources
It comes in the form of a single executable available on several platforms.

|Name  |Type  |Links|
|---------|---------|--|
|*VCSA*     |  `Appliance OVA`       |  `VMWare Link`|
|*Nested ESXi*    |   `Appliance OVA`       | [William Lam nested appliance](http://vmwa.re/nestedesxi)|
|*TrueNas Core Appliance*     |       `Appliance OVA`    | `From the Powershell Module TrueNas` |
|*Vyos*     |       `Appliance OVA`    | `From the Powershell Module VMwareHomeLab` *Available on PowerShell Gallery* |
|*DNS*     |       `Appliance OVA`    | `From the Powershell Module VMwareHomeLab` *Available on PowerShell Gallery*  |


![TrueNasAppliance](/img/TrueNas.gif){: .align-center}


{: .notice--warning}
**Disclaimer**: This is not officially supported by VMware, use at your own risk


# 2.Appliances in the Lab

1. **Vyos Appliance** I use th github `vyos` repository **vyos-vm-images** [here](https://github.com/vyos/vyos-vm-images) it use Ansible to create an OVA from the latest Rolling ISO. This appliance provide Internet Access to the lab and have two DHCP range one for the Lan et the other for the iSCSI network.
1. **PhotonOs_DNS_Appliance**, I create the appliance with **Packer** based actually on PhotonOs version 3, with th ***unbound DNS Server***. it will be fully variables.
1. **TrueNas Core Appliance**, I create an OVA with Packer too in and CI/CD today the latest build is available the **TrueNas Core 13U3**. it contain seven disk.
1. **Nested ESXi**, big thank's to William Lam to create them.
1.  **VCSA** the vCenter appliance available from the **VMware trial web site**.

# 3.How to use It ?

{: .notice--info}
With Terraform, you have to be careful with the management of secrets, so I decided to use Hashicorp's Vault solution to manage access to your host (vCenter or ESXi).

## 3.1 Vault 

It is necessary to understand that it is not to use a production the way I will use it but simply in development mode.
1. Ensure you have Vault install
1. Launch the script `start_server.sh`
1. Modify the credential in the script `add_credential.sh`
1. Launch the script `add_credential.sh`

* File ***start_server.sh***

```bash
#!/bin/bash
vault server -dev -dev-root-token-id="education"
```

* File ***add_credential.sh***

```bash
#!/bin/bash
export VAULT_ADDR='http://127.0.0.1:8200'
export VAULT_TOKEN='education'

vault kv put secret/VCSA login=Administrator mdp=MyP@$$w0rdVmware123 >/dev/null
```
![TrueNasAppliance](/img/vault1.png){: .align-center}

With Terraform there is a file that will contain all the variables and this will be the only file to modify.

## 3.2 Variables.tf 

{: .notice--info}Only use network with CIDR /24.

*. **ESXI** max 8 `ESxI` the four octet must be in 80..89 range

```terraform
variable "vm_names" {
    description = "ESXI name with the four Octet"
    default = {
        "ESXI1" = 81
        "ESXI2" = 82
        "ESXI3" = 83
        "ESXI4" = 84
        "ESXI5" = 85
        "ESXI6" = 86

    }
}
variable "groupe" {
    description = "Name for the Folder and ressource pool"
    type = string
    default = "G1"
}
variable "datacenter" {
    description = "Name for your Datacenter Object in your VMware vpShere Inventory"
    type = string
    default = "Datacenter"
}
variable "cluster" {
    description = "Name for your Cluster Object in your VMware vpShere Inventory"
    type = string
    default = "CLUSTER"
}
variable "datastore" {
    description = "Name for your DataStore Object in your VMware vpShere Inventory"
    type = string
    default = "TRUENAS1_VOL2"
}
variable "network" {
    description = "Name for your Network Object in your VMware vpShere Inventory: web Access"
    type = string
    default ="VM Network"
}
variable "trunk" {
    description = "Name for your Network Object in your VMware vpShere Inventory: Trunk "
    type = string
    default ="VM Trunk"
}
variable "iSCSI" {
    description = "Name for your Network Object in your VMware vpShere Inventory: iSCSI "
    type = string
    default ="ISCSI_G1"
}
variable "LAN" {
    description = "Name for your Network Object in your VMware vpShere Inventory: LAN "
    type = string
    default ="E20_V1179"
}
variable "vcsa" {
    description = "VCSA FQDN"
    type = string
    default ="#FQDN vCenter#"
}
variable "vcsarootp" {
    description = "VCSA root Password"
    type = string
    default ="VMware123!"
}
variable "vcsaAdminp" {
    description = "Password for Administrator@vsphere.local"
    type = string
    default ="VMware123!"
}

variable "esxi-ram" {
    description = "ESXI RAM"
    type = number
    default =   8192
}
variable "DomainName" {
    description = "Domain name for your LAB"
    type = string
    default = "vExpert.lab"
}
variable "network-address" {
    description = "Network Address"
    type = string
    default = "10.0.0"
}
variable "network-gateway" {
    description = "Gateway Address"
    type = string
    default = "10.0.0.254"
}
variable "network-dnsserver" {
    description = "DNS server Address"
    type = string
    default = "10.0.0"
}
variable "esxi-password" {
    description = "Esxi root password"
    type = string
    default = "VMware1!"
}
variable "esxinumber" {
    description = "How many ESXI in my Lab"
    type = string
    default = "6"
}
variable "DNS-Layout" {
    description = "Keyboard Layout in DNS Server fr/us"
    type = string
    default = "fr"
}
variable "DNS-Hostname" {
    description = "The hostname for the DNS Server"
    type = string
    default = "DNSSRV"
}
variable "DNS-password" {
    description = "Root Password for the DNS Server"
    type = string
    default = "VMware1!"
}
variable "vm-client" {
    description = "Name VM Client"
    type = string
    default = "Client"
}

variable "ClientC" {
    description = "Number of Client Ubuntu VM"
    type = number
    default = 1
}
variable "Clientcpu" {
    description = "Number CPU for Ubuntu Client"
    type = number
    default = 2
}
variable "Clientram" {
    description = "Number RAM for Ubuntu Client"
    type = number
    default = 4096
}
variable "Clientguestid" {
    description = "Ubuntu VM guest"
    type = string
    default = "ubuntu64Guest"
}
```

If you want to test **Enabling vSAN 8 Express Storage Architecture** you need to adjust ESXi memory to 16Gb like this..

```terraform

variable "esxi-ram" {
    description = "ESXI RAM"
    type = number
    default =   16 384
}
```
## 3.3 vCenter 

For the vcenter we will create a virtual machine vcsa8_ref according to the version and we will unlock the properties of the appliances because only the properties of the phase 1 of deployment are accessible.

{: .notice--warning}
**Disclaimer**: This is not officially supported by VMware, use at your own risk
 1. import the OVA on your existing host with the json file
 1. Remove the protection with `PowerCli` script.
 
* JSON File you can modify value according to your infrastructure.
 ```json
 {
    "__version": "2.13.0",
    "__comments": "Sample template to deploy a vCenter Server Appliance with an embedded Platform Services Controller on a vCenter Server instance.",
    "new_vcsa": {
        "vc": {
            "__comments": [
                "'datacenter' must end with a datacenter name, and only with a datacenter name. ",
                "'target' must end with an ESXi hostname, a cluster name, or a resource pool name. ",
                "The item 'Resources' must precede the resource pool name. ",
                "All names are case-sensitive. ",
                "For details and examples, refer to template help, i.e. vcsa-deploy {install|upgrade|migrate} --template-help"
            ],
            "hostname": "#FQDN vCenter#",
            "username": "#Username#",
            "password": "##Password##",
            "deployment_network": "VM Network",
            "datacenter": [
                "Datacenter"
            ],
            "datastore": "#Datastore#",
            "target": [
                "#Cluster#"
            ]
        },
        "appliance": {
            "__comments": [
                "You must provide the 'deployment_option' key with a value, which will affect the vCenter Server Appliance's configuration parameters, such as the vCenter Server Appliance's number of vCPUs, the memory size, the storage size, and the maximum numbers of ESXi hosts and VMs which can be managed. For a list of acceptable values, run the supported deployment sizes help, i.e. vcsa-deploy --supported-deployment-sizes"
            ],
            "thin_disk_mode": true,
            "deployment_option": "tiny",
            "name": "vcsa8_ref"
        },
        "network": {
            "ip_family": "ipv4",
            "mode": "static",
            "system_name": "vcsa.VMware.lab",
            "ip": "10.0.0.80",
            "prefix": "24",
            "gateway": "10.0.0.254",
            "dns_servers": [
                "10.0.0.100"
            ]
        },
        "os": {
            "password": "VMware123!",
            "ntp_servers": "time.nist.gov",
            "ssh_enable": true
        },
        "sso": {
            "password": "VMware123!",
            "domain_name": "vsphere.local"
        }
    },
    "ceip": {
        "description": {
            "__comments": [
                "++++VMware Customer Experience Improvement Program (CEIP)++++",
                "VMware's Customer Experience Improvement Program (CEIP) ",
                "provides VMware with information that enables VMware to ",
                "improve its products and services, to fix problems, ",
                "and to advise you on how best to deploy and use our ",
                "products. As part of CEIP, VMware collects technical ",
                "information about your organization's use of VMware ",
                "products and services on a regular basis in association ",
                "with your organization's VMware license key(s). This ",
                "information does not personally identify any individual. ",
                "",
                "Additional information regarding the data collected ",
                "through CEIP and the purposes for which it is used by ",
                "VMware is set forth in the Trust & Assurance Center at ",
                "http://www.vmware.com/trustvmware/ceip.html . If you ",
                "prefer not to participate in VMware's CEIP for this ",
                "product, you should disable CEIP by setting ",
                "'ceip_enabled': false. You may join or leave VMware's ",
                "CEIP for this product at any time. Please confirm your ",
                "acknowledgement by passing in the parameter ",
                "--acknowledge-ceip in the command line.",
                "++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++"
            ]
        },
        "settings": {
            "ceip_enabled": true
        }
    }
}
 ```

* PowerCli script

```powershell
Connect-VIServer -server YourServerFQDN -User vCenterUserName -Password vCenterPassword
$VM = Get-VM -Name "vcsa8_ref"

$spec = New-Object VMware.Vim.VirtualMachineConfigSpec
$spec.changeVersion = $VM.ExtensionData.Config.ChangeVersion
$spec.vAppConfig = New-Object VMware.Vim.VmConfigSpec

$properties = $vm.ExtensionData.Config.VAppConfig.Property
foreach ($prop in $properties) {
    $p = New-Object VMware.Vim.VAppPropertySpec
    $p.operation = "edit"
    $p.info = $prop
    $p.info.UserConfigurable = $True
    
    $spec.vAppConfig.property += $p
    }
$VM.ExtensionData.ReconfigVM_Task($spec)

$vm.ExtensionData.Config.VAppConfig.Property | Select {$_.id,$_.Value}
```
## 3.4 Lab version

All the solution work with all versions actually the version it designed for `vSphere 8` if you want to setup a vSphere 7 Lab you just comment somme line because the vCenter 8 have one disk more than the vCenter 7
 
On the Block resource `"vsphere_virtual_machine" "vcsa"` comment line 337 to 342
```terraform
// For VMware vsphere 7 just comment the disk above 
        disk {
    label = "disk16"
    size = "200"
    unit_number = 16
    }
```

## 3.5 Vyos router Lab

Here the bloc for thr router, it use the config via cloud-init encode with a function **filebase64**

```terraform
resource "vsphere_virtual_machine" "vyos" {
    depends_on = [
        vsphere_folder.parent
    ]
    name                       = "RTR_Lab_${var.DomainName}"
    resource_pool_id           = data.vsphere_resource_pool.resource_pool.id
    datastore_id               = data.vsphere_datastore.datastore.id
    datacenter_id              = data.vsphere_datacenter.dc.id
    host_system_id             = data.vsphere_host.host.id
    folder                     = vsphere_folder.parent.path
    memory                     = 1024
    wait_for_guest_net_timeout = 0
    wait_for_guest_ip_timeout  = 0
    network_interface {
        network_id = data.vsphere_network.network.id
    }
    network_interface {
        network_id = data.vsphere_network.lan.id
    }
    network_interface {
        network_id = data.vsphere_network.ISCSI.id
    }


    ovf_deploy {
        local_ovf_path    = "/Storage/VMware/vyos.ova"
        disk_provisioning = "thin"
    }

    vapp {
        properties = {
        "password"  = "VMware1!",
        "user-data" = filebase64("${path.module}/vyos/user-data.yaml"),
        }
    }
    lifecycle {
        ignore_changes = all
    }
}
```
You need to modify according to all the variables and for your infrastructure
* Yaml file

```yml
vyos_config_commands:
  - set system host-name 'RTRLAB'
  - set interfaces ethernet eth1 address '10.0.0.254/24'
  - set interfaces ethernet eth2 address '172.18.0.254/24'
  - set service dhcp-server shared-network-name LAN subnet 10.0.0.0/24 default-router '10.0.0.254'
  - set service dhcp-server shared-network-name LAN subnet 10.0.0.0/24 dns-server '10.0.0.100'
  - set service dhcp-server shared-network-name LAN subnet 10.0.0.0/24 domain-name 'vExpert.lab'
  - set service dhcp-server shared-network-name LAN subnet 10.0.0.0/24 range 0 start '10.0.0.1'
  - set service dhcp-server shared-network-name LAN subnet 10.0.0.0/24 range 0 stop '10.0.0.79'
  - set service dhcp-server shared-network-name LAN subnet 10.0.0.0/24 range 1 start '10.0.0.110'
  - set service dhcp-server shared-network-name LAN subnet 10.0.0.0/24 range 1 stop '10.0.0.253'
  - set service dhcp-server shared-network-name ISCSI subnet 172.18.0.0/24 range 2 start '172.18.0.1'
  - set service dhcp-server shared-network-name ISCSI subnet 172.18.0.0/24 range 2 stop '172.18.0.55'
  - set nat source rule 100 outbound-interface 'eth0'
  - set nat source rule 100 source address '10.0.0.0/24'
  - set nat source rule 100 translation address 'masquerade'
```

# 4 Setup the Lab

1. Ensure you have all source mine ar located in **/Storage/VMware folder**
```terraform
 ovf_deploy {
        local_ovf_path    = "/Storage/VMware/PhotonOS_DNS_Appliance_0.2.2.ova"
        disk_provisioning = "thin"
        ovf_network_map = {
        "VM Network" = data.vsphere_network.lan.id
        }
    }
```

2. Run the command Terraform plan
1. Run the command Terraform apply 

And the résult : 

![IAC](/img/TerraformLab1.PNG){: .align-center}

# Optional config the Lab

If you want to configure you Lab with **One Gist** you can.
1. [Gist link](https://gist.github.com/JM2K69/625aeb845e828c4bdfcdf5a8649fca93)
1. Run this gist from your client and modify variables according to your lab
1. The client nned to have somme PowerShell module : 
    1. PowerCli 
    1. TrueNas to configure TrueNas server via API

![IAC](/img/TerraformLab2.PNG){: .align-center}

And gone ! ! 
The Terraform project is available on Github [here](https://github.com/JM2K69/Terraform_vSphere8)

Thank's for reading.🤗

Written by Jérôme Bezet-Torres @JM2K69.
