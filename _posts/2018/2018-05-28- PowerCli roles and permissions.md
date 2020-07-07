---
layout: single
title: "PowerCli Roles and Privilege"
date: 2018-05-28
tags: 
  - Powershell
categories:
  - Powershell
  - 'PowerCli'
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



How to manage Roles and Permissions in vSphere 6.X environement.


In a VMware vSphere environment, you might want to give certain permissions
to users or administrators, who are not a part of the vSphere administrator's team,
to perform specific tasks. For example, you might want to give the administrators
of a server the permission to power on and off the server. You don't want to give
these administrators all of the privileges in your environment because then you
will lose control over it. There are many privileges you can give to somebody, and
you probably want to give only a few. If you assigned privileges to users directly,
it would be hard to see who has which privileges.

To begin, you will need to open a PowerCLI and connect to a vCenter server. You will take our folder structure and assign groups of users
from Active Directory to access these resources using predefined roles in VMware vsphere. This assumes that
you have properly configured your `VMware SSO` to allow `Active Directory` authentication.
You will take an Active Directory group called `IT Admins` and delegate access to the entire Primary datacenter. You will take the `Developers` group and delegate the operator access to them for the `Developpement` folder in vCenter.

# 1.  Predefined Roles

## 1.1 List predefined roles

```powershell
Get-VIRole | Select Name, Description
```

```powershell
PS C:\Users\JM2K69> Get-VIRole | Select Name, Description

Name                                  Description
----                                  -----------
NoCryptoAdmin                         Full access without Cryptographic operations privileges
NoAccess                              Used for restricting granted access
Anonymous                             Not logged-in user (cannot be granted)
View                                  Visibility access (cannot be granted)
ReadOnly                              See details of objects, but not make changes
Admin                                 Full access rights
VirtualMachinePowerUser               Provides virtual machine interaction and configuration permissions
VirtualMachineUser                    Provides virtual machine interaction permissions
ResourcePoolAdministrator             Supports delegated resource management
VMwareConsolidatedBackupUser          Used by the Consolidated Backup utility
DatastoreConsumer                     Assigned to datastores to allow creating disks or snapshots
NetworkConsumer                       Assigned to networks to allow association of virtual machines or hosts with networks
VirtualMachineConsoleUser             Provides virtual machine console interaction permissions. This role is required for VMRC sessions. Exercise...
AutoUpdateUser                        AutoUpdateUser
InventoryService.Tagging.TaggingAdmin InventoryService.Tagging.TaggingAdmin
com.vmware.Content.Admin              Provides full access to Content Library service

```

This command list all predefined role in your VMware vSphere Infrastructure.

## 1.2 Assigned Role to an Active Directory Group

To do this, you will use the `New-VIPermission` cmdlet. This cmdlet
requires an Entity where the permission will be applied, a Principal who represents the user or group and the desired role. For the first cmdlet, you will grant the `Admin role on the Root datacenter to our IT Admin group, which has the principal name JM2K69\IT Admin:

```powershell
New-VIPermission -Entity (Get-Datacenter "Datacenter") -Principal
"JM2K69\IT Admins" -Role Admin
```

## 1.3 Assigned permission for Developpers Group

Using the same format for another `New-VIPermission` cmdlet, you can now grant our Developers group the operator status as `VirtualMachineUser`
on the `Developpement` folder. You will use the Get-Folder cmdlet to set our entity (or location):

```powershell
New-VIPermission -Entity (Get-Folder "Developpement") -Principal "JM2K69\Developers" -Role VirtualMachineUser
```

All permissions are grouped by roles. With PowerCli you can list permissions by group.

```powershell
Get-VIPrivilege | select -Property ParentGroup -Unique
```

The output of the preceding command is too long

# 2 Create a custom role

In your vSphere web Client or Html5 web client you can create custom role in the view =>**Administration**=>**Contrôles d'accès**=>**Rôles**

![ComputerSection](/img/Right1.PNG)

Then you create a new role and you can add permission by Category and by object type.

![ComputerSection](/img/Right2.PNG)

For the Object type Virtual Machine you have multiple subcategory for the object how to find them with powercli in order to create your custom role.

## 2.1 Cmdlet Get-VIPrivilege

The Cmdlet `Get-VIPrivilege` offers the possibility to list them.

```powershell
PS C:\Users\JM2K69> Get-VIPrivilege

Name                                Description                                        Server
----                                -----------                                        ------
Anonymous                           The only privilege held by sessions which have ... 192.168.0.115
View                                Visibility without read access to an entity. Th... 192.168.0.115
Read                                Grants read access to an entity                    192.168.0.115
Manage custom attributes            Add, remove, and rename custom attribute defini... 192.168.0.115
Set custom attribute                Set the value of a custom attribute on an object   192.168.0.115
Log event                           Log a user-defined event on an object              192.168.0.115
Cancel task                         Cancel a running task                              192.168.0.115
Licenses                            Manage licenses                                    192.168.0.115
Diagnostics                         Export diagnostic data                             192.168.0.115
Settings                            Edit global settings                               192.168.0.115
Act as vCenter Server               Act as the vCenter Server                          192.168.0.115
Capacity planning                   Discover and convert physical host to virtual m... 192.168.0.115
Script action                       Schedule an external script action                 192.168.0.115
Proxy                               Add or remove endpoints to or from the proxy       192.168.0.115
Disable methods                     Operations are disabled in vCenter                 192.168.0.115
Enable methods                      Operations are enabled in vCenter                  192.168.0.115
Service managers                    Access the directory service                       192.168.0.115
Health                              Access the health of vCenter group                 192.168.0.115
System tag                          Add or remove system tag                           192.168.0.115
Global tag                          Add or remove global tag                           192.168.0.115
Create folder                       Create folder                                      192.168.0.115
Delete folder                       Delete folder                                      192.168.0.115
Rename folder                       Rename folder                                      192.168.0.115
Move folder                         Move folder                                        192.168.0.115
Create datacenter                   Create a datacenter                                192.168.0.115
Remove datacenter                   Remove a datacenter                                192.168.0.115
Rename datacenter                   Rename a datacenter                                192.168.0.115
Move datacenter                     Move a datacenter                                  192.168.0.115
Network protocol profile configu... Configure a network protocol profile on a datac... 192.168.0.115
Release IP allocation               Release IP allocation on a network protocol pro... 192.168.0.115
Query IP pool allocation            Query IP pool allocation on a network protocol ... 192.168.0.115
Reconfigure datacenter              Reconfigure a datacenter                           192.168.0.115
Rename datastore                    Rename a datastore                                 192.168.0.115
Move datastore                      Move a datastore                                   192.168.0.115
Remove datastore                    Remove a datastore from the datacenter             192.168.0.115
.....
```
The outpout is too long we need to apply some filter, because with this Cmdlets we list all privileges.
 
### 2.1.1 List ParentGroupId

All authorizations are grouped, you can list them with this filter

```powershell
PS C:\Users\JM2K69> Get-VIPrivilege | select -Property ParentGroupid -Unique

ParentGroupId
-------------
System
Global
Folder
Datacenter
Datastore
Network
DVSwitch
DVPortgroup
Host.Inventory
Host.Hbr
Host.Config
Host.Local
Host.Cim
VirtualMachine.Inventory
VirtualMachine.Interact
VirtualMachine.GuestOperations
VirtualMachine.Config
VirtualMachine.State
VirtualMachine.Hbr
VirtualMachine.Provisioning
VirtualMachine.Namespace
Resource
Alarm
Task
ScheduledTask
Sessions
Performance
Authorization
Extension
VApp
Profile
EAM
StoragePod
Certificate
HealthUpdateProvider
ExternalStatsProvider
.....
```
The outpout is too long so I cut it.

We can see tha for the `Object VirtualMachine` we have eight subcategories.

### 2.1.2 List Privilege by ParentGroupId

With the Cmdlets `Get-Viprivilege`with the `Parameter` -Id **VirtualMachine.Interact.***, you list all privilege for the interaction with a Virtual Machine .

```powershell
PS C:\Users\JM2K69> Get-VIPrivilege -Id VirtualMachine.Interact.*

Name                                Description                                        Server
----                                -----------                                        ------
Power on                            Power on or resume a virtual machine               192.168.0.115
Power off                           Power off a virtual machine                        192.168.0.115
Suspend                             Suspend a virtual machine                          192.168.0.115
Reset                               Reset (power cycle) a virtual machine              192.168.0.115
Pause or Unpause                    Pause or unpause a virtual machine                 192.168.0.115
Answer question                     Answer a virtual machine run-time question         192.168.0.115
Console interaction                 Interact with the virtual machine console          192.168.0.115
Connect devices                     Connect/disconnect media and network devices       192.168.0.115
Configure CD media                  Configure a different media for virtual CD-ROMs    192.168.0.115
Configure floppy media              Configure a different media for virtual floppies   192.168.0.115
Install VMware Tools                Install VMware Tools (or mount/unmount the tool... 192.168.0.115
Guest operating system managemen... Perform management operations within the guest ... 192.168.0.115
Defragment all disks                Defragment all disks on the virtual machine        192.168.0.115
Turn on Fault Tolerance             Turn on Fault Tolerance for this virtual machine   192.168.0.115
Turn off Fault Tolerance            Turn off Fault Tolerance for this virtual machine  192.168.0.115
Test failover                       Make the Secondary VM the Primary VM               192.168.0.115
Test restart Secondary VM           Terminate the Secondary VM                         192.168.0.115
Suspend Fault Tolerance             Suspend Fault Tolerance for this virtual machine   192.168.0.115
Resume Fault Tolerance              Resume Fault Tolerance for this virtual machine    192.168.0.115
Record session on virtual machine   Record session on a virtual machine                192.168.0.115
Replay session on virtual machine   Replay session on a virtual machine                192.168.0.115
Backup operation on virtual machine Backup operations on a virtual machine             192.168.0.115
Create screenshot                   Create a screenshot                                192.168.0.115
Inject USB HID scan codes           Inject a sequence of USB HID scan codes into th... 192.168.0.115
Perform wipe or shrink operations   Perform wipe or shrink operations on Flex-SE disks 192.168.0.115
Drag and drop                       Drag files between a virtual machine and a remo... 192.168.0.115
```

# 2.2 Create the role

For example, if you want to create custom role named `JM2K69_Role` with privileges to power on/off the virtual machine, add new disk, and configure advanced settings then the PowerCLI command used to create this custom vCenter Server role would look like this

```powershell
New-VIRole -Name “JM2K69_Role” -Privilege (Get-VIPrivilege -Id VirtualMachine.Interact.PowerOn,VirtualMachine.Interact.PowerOff,VirtualMachine.Config.AddNewDisk,VirtualMachine.Config.AdvancedConfig)
```

The outpout will be

```powershell
PS C:\Users\JM2K69> New-VIRole -Name “JM2K69_Role” -Privilege (Get-VIPrivilege -Id VirtualMachine.Interact.PowerOn,VirtualMachine.Interact.PowerOff,Virtual
Machine.Config.AddNewDisk,VirtualMachine.Config.AdvancedConfig)

Name                      IsSystem
----                      --------
JM2K69_Role               False
```

![ComputerSection](/img/Right3.PNG)

And with PowerCLI ...

```powershell
PS C:\Users\JM2K69> Get-VIRole

Name                      IsSystem
----                      --------
NoCryptoAdmin             True
NoAccess                  True
Anonymous                 True
View                      True
ReadOnly                  True
Admin                     True
VirtualMachinePowerUser   False
VirtualMachineUser        False
ResourcePoolAdministrator False
VMwareConsolidatedBack... False
DatastoreConsumer         False
NetworkConsumer           False
VirtualMachineConsoleUser False
AutoUpdateUser            False
InventoryService.Taggi... False
JM2K69_Role               False
com.vmware.Content.Admin  False
```

# 3.Conclusion

With hundreds of privileges packaged with vCenter, it can be daunting to try and create
custom roles. Some privileges that might not be obviously required can prevent a custom
role from having the desired access. One suggestion is to take an existing or default role and
then work from its privilege set to alter it for your uses. This can easily be done by retrieving
an existing privilege set and storing them in a variable. Then you can pass this existing list of
privileges into your new custom VIRole.

Written by Jérôme Bezet-Torres @JM2K69.