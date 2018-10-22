---
layout: post
title: "PowerCli Tag Folder"
date: 2018-04-19
tags: [PowerShell,PowerCLi, VMware ]
---


How to manage Tag in VMware vSphere 6.x and folders in VM View and Templates.


* TOC
{:toc}

First, what are the tags for our virtual machines? They will allow us to search with simple keywords. We add a tag attribute with a value to the virtual machine. This management with this metadata allows us to have a more flexible management of our infrastructure, since several objects will be able to receive a TAG.


# 1.  Tag and Metadata

## 1.1 Create

All Tags belong to a category and we have the possibility to create a tag that is said multiple that belong to several category

How to create Tag Category with PowerCLI

```powershell
New-TagCategory -Name "JM2K69_Tag" -Cardinality "Single" -Description "Testing PowerCLi" -EntityType "VirtualMachine"

New-Tag -Name "VM_bronze" -Category "JM2K69_Tag"

New-Tag -Name "VM_platinum" -Category "JM2K69_Tag" 

$myTag = Get-Tag -Category "JM2K69_Tag" -Name "VM_bronze"

Get-VM "DC01" | New-TagAssignment -Tag $myTag
```
![ComputerSection](/img/Tag1.PNG)

With this command we create one Category `JM2K69_Tag` with two Tag `VM_Bronze` and `VM_platinum`.

## 1.2 Work with Tag

Now how to Find a VM with a Tag :
```powershell

$cat = Get-TagCategory -Name 'JM2K69_Tag'
[string]$Tag = "VM_bronze"
$info=Get-VM | Where{Get-TagAssignment -Entity $_ -Category $cat } | Select Name,@{N="Tag";E={(Get-TagAssignment $_.name).tag}} |Where-Object {$_.Tag -like "*$Tag"}
```
![ComputerSection](/img/Tag2.PNG)

Why i use `Where-Object {$_.Tag -like "*$Tag"}` the command return an object with name Tag we have **Category** **/** **Tag**, with the `-like "*$Tag"` we can pass just one value on the filter and if your Tag is multiple ( depend on two category), you can find it. 

![ComputerSection](/img/Tag3.PNG)

In my case the result is the same. 

# 2.  Folder

We have the possibility to create the VM and Model view folder to organize our VMS according to the clients or their different role within our infrastructure.

## 2.1 Create 

For create the directory in VMware Inventory I use `Folder.txt` a simple file text like this.

```txt
vm\Formation
vm\Hébergment
vm\Infrastructure
vm\Production
vm\Developpement
vm\Formation\Postes de travail
vm\Formation\Serveurs
vm\Hébergment\Client1
vm\Hébergment\Client2
vm\Hébergment\Client3
vm\Hébergment\Client1\IaaS
vm\Hébergment\Client1\IaaS\Postes de travail
vm\Hébergment\Client1\IaaS\Serveurs
vm\Hébergment\Client2\PaaS
vm\Hébergment\Client2\PaaS\J2EE
vm\Hébergment\Client3\SaaS
vm\Hébergment\Client3\SaaS\mail
vm\Infrastructure\Contrôleurs de domaine
vm\Infrastructure\Exchange
vm\Infrastructure\Templates
vm\Production\Postes de travail
vm\Production\Serveurs
vm\Developpement\Postes de travail
vm\Developpement\Serveurs
vm\Test\Integration
```
I use a PowerCLI function for create the Folder in vSphere.

```powershell
function New-FolderFromPath
{
  param
  (
  [Parameter(Mandatory=$true)]
  [String] $Path 
  )
  $splittedPaths = $Path.Split('\')

  $location = Get-Folder $splittedPaths[0] -ErrorAction Stop

  $splittedPaths[1..$splittedPaths.Length] | ForEach-Object {
    $nouveauDossier = Get-Folder -Name $_ -Location $location `
            -ErrorAction Ignore
    if($nouveauDossier -eq $null) { 
        Write-Verbose "Création du répertoire $_"
        $nouveauDossier = New-Folder -Name $_ -Location $location
    }else{
        Write-Warning "Le dossier $_ existe déjà"
    }
    $location = $nouveauDossier
  }
  
  return $nouveauDossier
} 
```
In Practice How to use it :

```powershell
$Folder = Get-Content -Path 'C:\FrPSUG\Folder.txt' 

$Folder | ForEach-Object {New-FolderFromPath -Path $_}

```

![ComputerSection](/img/Folder1.PNG)

In the web client vSphere the result is the same.

![ComputerSection](/img/Folder2.PNG)


## 2.2 Delete 

For Remove folders I create the same function but with a remove-folder.
> [!WARNING]
> When you remove Folder your delete also all ressources in this folder VM Cluster.

```powershell
function remove-FolderFromPath
{
  param
  (
  [Parameter(Mandatory=$true)]
  [String] $Path 
  )
  $splittedPaths = $Path.Split('\')

  $location = Get-Folder $splittedPaths[0] -ErrorAction Stop

  $splittedPaths[2..$splittedPaths.Length] | ForEach-Object {
    $nouveauDossier = Get-Folder -Name $_ -Location $location `
            -ErrorAction Ignore
    if($nouveauDossier -ne $null) { 
       
        $nouveauDossier | Remove-Folder -Confirm:$false
    }else{
        
    }
    $location = $nouveauDossier
  }
  
  return $nouveauDossier
} 

```
In Practice How to use it :

```powershell
$Folder = Get-Content -Path 'C:\FrPSUG\Folder.txt' 

$Folder | ForEach-Object {remove-FolderFromPath -Path $_}
```
![ComputerSection](/img/Folder3.PNG)

The result si the same output but my function delete only subfolders and not the parent.

![ComputerSection](/img/Folder4.PNG)

# 3.  Conclusion

In the next post we will talk about authorization always in PowerCLI that we will apply on our VMware objects.

Written by Jérôme Bezet-Torres @JM2K69.