---
layout: post
title: "Create your Active Directory Labs with Powershell"
date: 2018-10-22
tags: [PowerShell,Lab, Active Directory ]
---

* TOC
{:toc}

How to create your Active Directory Lab with Powershell. We are working with the latest Windows Server 2019 version aka 1809, We use Standard version with GUI, but you can run all this step in a Windows Server Core this is more secure to my mind, but you need have a remote Windows 10 with RSAT. In my Lab I use free API `https://randomuser.me/` for create random user in my Active Directory

# 1.  Configure Windows Server 2019

## 1.1 Networking

    How to configure networking with powershell.

```powershell
#Find the Interface Index for your NIC with the name Ethernet
$InterfaceIndex = $(Get-NetIPAddress | Where-Object {$_.InterfaceAlias -like "Ethernet*"}).InterfaceIndex

#Assign Address IPv4
New-NetIPAddress -InterfaceIndex $InterfaceIndex -AddressFamily IPv4 -IPAddress “172.17.0.1” –PrefixLength 16 -DefaultGateway 172.17.255.254

#Assign Server DNS
Set-DnsClientServerAddress -InterfaceIndex $InterfaceIndex  -ServerAddresses 172.17.0.1
```

## 1.2 ComputerName

```powershell
Rename-Computer -NewName DC -Restart 
```

## 1.3 Install WindowsFeature

* The first step is to install **Ad-Domain-Services**.

```powershell
Get-WindowsFeature -Name *AD-Domain*
```

* Install the Feature with all subfeatures

```powershell
Install-WindowsFeature -Name AD-Domain-services -IncludeAllSubFeature -IncludeManagementTools
```
* Verify all the Feature installed

```powershell
Get-WindowsFeature | Where-Object {$_. installstate -eq "installed"} | Format-List Name,Installstate | more
```
The Output :

```powershell
Name         : AD-Domain-Services
InstallState : Installed

Name         : FileAndStorage-Services
InstallState : Installed

Name         : File-Services
InstallState : Installed

Name         : FS-FileServer
InstallState : Installed

Name         : Storage-Services
InstallState : Installed

Name         : NET-Framework-45-Features
InstallState : Installed

Name         : NET-Framework-45-Core
InstallState : Installed

Name         : NET-WCF-Services45
InstallState : Installed

Name         : NET-WCF-TCP-PortSharing45
InstallState : Installed

Name         : GPMC
InstallState : Installed

Name         : RSAT
InstallState : Installed

Name         : RSAT-Role-Tools
InstallState : Installed

Name         : RSAT-AD-Tools
InstallState : Installed

Name         : RSAT-AD-PowerShell
InstallState : Installed

Name         : RSAT-ADDS
InstallState : Installed

Name         : RSAT-AD-AdminCenter
InstallState : Installed

Name         : RSAT-ADDS-Tools
InstallState : Installed

Name         : WoW64-Support
InstallState : Installed

Name         : Windows-Defender
InstallState : Installed

Name         : PowerShellRoot
InstallState : Installed

Name         : PowerShell
InstallState : Installed

Name         : PowerShell-ISE
InstallState : Installed

Name         : XPS-Viewer
InstallState : Installed

```

# 2.  Create Active Directory Forest

For Create Forest you have one powershell cmdlet `Install-ADDSforest'. the full documentation is available [here](https://technet.microsoft.com/fr-fr/library/hh974720(v=wps.630).aspx).
The function accept many parameters in my Lab, I use the minimal parameter to quickly promote my domain controller.

```powershell
#Secure my password
$Password ="123+aze"
$SecurePassword = $Password | ConvertTo-SecureString -AsPlainText -Force

# Promote the domain controller
Install-ADDSForest -DomainName JM2K69.pwsh -SafeModeAdministratorPassword $SecurePassword -Force
```
The Output in **French** sorry ;-)! :

```txt
AVERTISSEMENT : Les contrôleurs de domaine Windows Server 2019 offrent un paramètre de sécurité par défaut nommé « Autoriser les algorithmes de chiffrement compatibles avec Windows NT 4.0 ». Ce paramètre empêche l’utilisation d’algorithmes de chiffrement faibles lors de l’établissement de sessions sur canal sécurisé.

Pour plus d’informations sur ce paramètre, voir l’article 942564 de la Base de connaissances (http://go.microsoft.com/fwlink/?LinkId=1
04751).

AVERTISSEMENT : Il est impossible de créer une délégation pour ce serveur DNS car la zone parente faisant autorité est introuvable ou elle n’exécute pas le serveur DNS Windows. Si vous procédez à l’intégration avec une infrastructure DNS existante, vous devez manuellement créer une délégation avec ce serveur DNS dans la zone parente pour activer une résolution de noms fiable en dehors du domaine « JM2K69.pwsh ». Sinon, aucune action n’est requise.

AVERTISSEMENT : Les contrôleurs de domaine Windows Server 2019 offrent un paramètre de sécurité par défaut nommé « Autoriser les algorithmes de chiffrement compatibles avec Windows NT 4.0 ». Ce paramètre empêche l’utilisation d’algorithmes de chiffrement faibles lors de l’établissement de sessions sur canal sécurisé.

Pour plus d’informations sur ce paramètre, voir l’article 942564 de la Base de connaissances (http://go.microsoft.com/fwlink/?LinkId=1
04751).

```

Then you computer restart.

# 2.  Create your Organizational Unit (OU)

Now that the domain controller is up and running, you can start creating OUs. they allow to organize the objects of your company (Users, Computers, Printers ...). On these organizational units will be positioned the GPOs.

![ComputerSection](/img/OU.PNG)

## 2.1 PowerShell Time

At first, the script must be able to work on any ADDS. This code below allows us to retrieve all the information needed to create objects in active directory.

```powershell
$fqdn = Get-ADDomain
$fulldomain = $fqdn.DNSRoot
$domain = $fulldomain.split(".")
$Dom = $domain[0]
$Ext = $domain[1]
```

## 2.2 Create one OU

Th powershell Cmdlet for create OU is very simple :

```powershell
New-ADOrganizationalUnit -Name Test -Description Test
```
In your Lab Only and not in **Production** you can adn the `-parameter` **-ProtectedFromAccidentalDeletion $false** you can easily remove the OU.

## 2.3 My Script

```powershell
# Informations
$Sites = ("Lyon","New-York","London")
$Services = ("Production","Marketing","IT","Direction","Helpdesk")
$Materiel = ("Computer","Server","Printers")
$FirstOU ="Sites"

New-ADOrganizationalUnit -Name $FirstOU -Description $FirstOU  -Path "DC=$Dom,DC=$EXT" -ProtectedFromAccidentalDeletion $false


foreach  ($S in $Sites)
{
        New-ADOrganizationalUnit -Name $S -Description "$S"  -Path "OU=$FirstOU,DC=$Dom,DC=$EXT" -ProtectedFromAccidentalDeletion $false

     foreach ($Serv in $Services)
     {
        New-ADOrganizationalUnit -Name $Serv -Description "$S $Serv"  -Path "OU=$S,OU=$FirstOU,DC=$Dom,DC=$EXT" -ProtectedFromAccidentalDeletion $false
     }

 }
```
# 3.  Create User

I Create a function to use a free [`API`](https://randomuser.me/).

```powershell
function New-RandomUser {
    <#
        .SYNOPSIS
            Generate random user data from Https://randomuser.me/.
        .DESCRIPTION
            This function uses the free API for generating random user data from https://randomuser.me/
        .EXAMPLE
            Get-RandomUser 10
        .EXAMPLE
            Get-RandomUser -Amount 25 -Nationality us,gb 
        .LINK
            https://randomuser.me/
    #>
    [CmdletBinding()]
    param (
        [Parameter(Position = 0)]
        [ValidateRange(1,500)]
        [int] $Amount,

        [Parameter()]
        [ValidateSet('Male','Female')]
        [string] $Gender,

        # Supported nationalities: AU, BR, CA, CH, DE, DK, ES, FI, FR, GB, IE, IR, NL, NZ, TR, US
        [Parameter()]
        [string[]] $Nationality,

        [Parameter()]
        [ValidateSet('json','csv','xml')]
        [string] $Format = 'json',

        # Fields to include in the results.
        # Supported values: gender, name, location, email, login, registered, dob, phone, cell, id, picture, nat
        [Parameter()]
        [string[]] $IncludeFields,

        # Fields to exclude from the the results.
        # Supported values: gender, name, location, email, login, registered, dob, phone, cell, id, picture, nat
        [Parameter()]
        [string[]] $ExcludeFields
    )

    $rootUrl = "http://api.randomuser.me/?format=$($Format)"

    if ($Amount) {
        $rootUrl += "&results=$($Amount)"
    }

    if ($Gender) {
        $rootUrl += "&gender=$($Gender)"
    }

    if ($Nationality) {
        $rootUrl += "&nat=$($Nationality -join ',')"
    }

    if ($IncludeFields) {
        $rootUrl += "&inc=$($IncludeFields -join ',')"
    }

    if ($ExcludeFields) {
        $rootUrl += "&exc=$($ExcludeFields -join ',')"
    }
    Invoke-RestMethod -Uri $rootUrl
}
```

## 3.1 How to use the function

How to :

```powershell
$users = New-RandomUser -Amount 30 -Nationality us -IncludeFields name,dob,phone,cell -ExcludeFields picture | Select-Object -ExpandProperty results

foreach ($user in $users) {
    $newUserProperties = @{
        Name = "$($user.name.first) $($user.name.last)"
        City = "City"
        GivenName = $user.name.first
        Surname = $user.name.last
        Path = $adPath
        title = "Director"
        department="IT"
        OfficePhone = $user.phone
        MobilePhone = $user.cell
        Company="JM2K69"
        EmailAddress="$($user.name.first).$($user.name.last)@$($adDomain)"
        AccountPassword = (ConvertTo-SecureString $userPassword -AsPlainText -Force)
        SamAccountName = $($user.name.first).Substring(0,1)+$($user.name.last)
        UserPrincipalName = "$(($user.name.first).Substring(0,1)+$($user.name.last))@$($adDomain)"
        Enabled = $true
    }

    try {New-ADUser @newUserProperties}
    catch {}
}

```
# 4. The final Script

All the part combined in one. For The different site I change the `-parameter` **Nationality**.
In this example I create **20 Employees** and **5 Directors** / **Sites** / **Services**.

```powershell
$fqdn = Get-ADDomain
$fulldomain = $fqdn.DNSRoot
$domain = $fulldomain.split(".")
$Dom = $domain[0]
$Ext = $domain[1]
$userPassword = '123+aze'
$Sites = ("Lyon","New-York","London")
$Services = ("Production","Marketing","IT","Direction","Helpdesk")
$Materiel = ("Computer","Server","Printers")
$FirstOU ="Sites"

New-ADOrganizationalUnit -Name $FirstOU -Description $FirstOU  -Path "DC=$Dom,DC=$EXT" -ProtectedFromAccidentalDeletion $false


foreach  ($S in $Sites)
{
        New-ADOrganizationalUnit -Name $S -Description "$S"  -Path "OU=$FirstOU,DC=$Dom,DC=$EXT" -ProtectedFromAccidentalDeletion $false
 
     foreach ($Serv in $Services)
     {
        New-ADOrganizationalUnit -Name $Serv -Description "$S $Serv"  -Path "OU=$S,OU=$FirstOU,DC=$Dom,DC=$EXT" -ProtectedFromAccidentalDeletion $false
      
      switch ($S)
      {
          'Lyon' {      
                        $Employees = New-RandomUser -Amount 20 -Nationality fr -IncludeFields name,dob,phone,cell -ExcludeFields picture | Select-Object -ExpandProperty results
                        $Directors = New-RandomUser -Amount 5 -Nationality fr -IncludeFields name,dob,phone,cell -ExcludeFields picture | Select-Object -ExpandProperty results
                 }
          'New-York' {
                        $Employees = New-RandomUser -Amount 20 -Nationality us -IncludeFields name,dob,phone,cell -ExcludeFields picture | Select-Object -ExpandProperty results
                        $Directors = New-RandomUser -Amount 5 -Nationality us -IncludeFields name,dob,phone,cell -ExcludeFields picture | Select-Object -ExpandProperty results
                     }
          'London' {
                        $Employees = New-RandomUser -Amount 20 -Nationality gb -IncludeFields name,dob,phone,cell -ExcludeFields picture | Select-Object -ExpandProperty results
                        $Directors = New-RandomUser -Amount 5 -Nationality gb -IncludeFields name,dob,phone,cell -ExcludeFields picture | Select-Object -ExpandProperty results

          }
          Default {}
      }

        foreach ($user in $Employees)
        {
          $newUserProperties = @{
        Name = "$($user.name.first) $($user.name.last)"
        City = "$S"
        GivenName = $user.name.first
        Surname = $user.name.last
        Path = "OU=$Serv,OU=$S,OU=$FirstOU,dc=$Dom,dc=$EXT"
        title = "Employees"
        department="$Serv"
        OfficePhone = $user.phone
        MobilePhone = $user.cell
        Company="$Dom"
        EmailAddress="$($user.name.first).$($user.name.last)@$($fulldomain)"
        AccountPassword = (ConvertTo-SecureString $userPassword -AsPlainText -Force)
        SamAccountName = $($user.name.first).Substring(0,1)+$($user.name.last)
        UserPrincipalName = "$(($user.name.first).Substring(0,1)+$($user.name.last))@$($fulldomain)"
        Enabled = $true
    }

           Try{ New-ADUser @newUserProperties}
           catch{}

        }
        foreach ($user in $Directors)
        {
          $newUserProperties = @{
        Name = "$($user.name.first) $($user.name.last)"
        City = "$S"
        GivenName = $user.name.first
        Surname = $user.name.last
        Path = "OU=$Serv,OU=$S,OU=$FirstOU,dc=$Dom,dc=$EXT"
        title = "Directors"
        department="$Serv"
        OfficePhone = $user.phone
        MobilePhone = $user.cell
        Company="$Dom"
        EmailAddress="$($user.name.first).$($user.name.last)@$($fulldomain)"
        AccountPassword = (ConvertTo-SecureString $userPassword -AsPlainText -Force)
        SamAccountName = $($user.name.first).Substring(0,1)+$($user.name.last)
        UserPrincipalName = "$(($user.name.first).Substring(0,1)+$($user.name.last))@$($fulldomain)"
        Enabled = $true
    }

           Try{ New-ADUser @newUserProperties}
           catch{}

        }
     }
 }
```

Written by Jérôme Bezet-Torres @JM2K69.
