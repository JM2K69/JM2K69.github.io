---
layout: post
title: "Custom Wizard Hydration for HydrationKit - Part1"
date: 2017-03-30
tags: [PowerShell, Active Directory, PowerShell Studio, Scripting]
---

Today, to automate or customize products we are forced to use scripts, I wish I could wish customize the Hydration Kit for SCCM and other products.
That is why I have decided to create a GUI in PowerShell to interact with all of the files of configurations that offers us MDT when using offline media. So i use PowerShell 2017 Studio to create this GUI.

This is the first post in a series covering : 
* Part 1: The Custom Wizard Hydration Interface 
* Part 2: How to use the PowerShell Script 
* Part 3: Other Hydration Kit - Deployment - SharePoint - Exchange 
 
# Index

* TOC
{:toc}


# What can I customize ?

With Custom Hydration you can customize :

**Script interface**


![ComputerSection](/img/Full.PNG)


**1. Computers Section**
* Computer Name
  * Local Admin Password
  * Domain Admin Password for domain controller only
  * Worgroup or Domain


![ComputerSection](/img/First.PNG)


**2. Network Section @IPV4**
* IPv4 Address
  * Subnet Mask
  * DNS Server
  * Gateway

![IPV4Section](/img/IPv4Section.PNG)


**3. DHCP Section**
* DHCP scope subnet
  * DHCP scope name
  * DHCP scope start IPv4
  * DHCP scope end IPv4
  * DHCP scope router
  * DHCP scope DNS server
  * DHCP scope domain name
  * DHCP scope subnet mask

![DHCPSection](/img/DHCPSection.PNG)


**4. Active Directory Section for Domain Controller**
* Domain DNS name
  * Domain NetBios name
  * Domain recovery password
  * Site name
  * Organizational Unit

![ActiveDirectorySection](/img/ActiveDrectory.PNG)


**5. Informations only**
* Platform Virtualization
  * VM memory
  * WinPe / ADK version
  * Language
  * Install VMware Tools

![VMSection](/img/VM.PNG)

All these values are stored in variables that result from running the **_ZTIGather.wsf_** script, then its values are stored in memory and in the **_VARIABLES.DAT_** file.

# Customsetting.ini

The customsettings.ini file contains all the necessary rules and all the information for a task sequence. Therefore, modify the customsettings.ini file so that the CWH.ps1 script to work properly.

## Sample Customsetting.ini

```ini
[Settings]
Priority=Default
Properties=HydrationOSDComputerName,HydraTimerSec,VMTOOLS

[Default]
HydraTimerSec = 20
VMTOOLS=NO
HydrationOSDComputerName=DC01
```



The _HydraTimerSec_ **Property** corresponds to the time from which we have the opportunity to press the **_modify_** button. The property VMTOOLS is if you run the Hydration Kit in VMware products like vSphere or Workstation it install VMware Tools during the **TaskSequence**.

## TaskSequences

For each virtual machine, there is an INI file that populates the machine-specific information, they are located in the Deploy\Control\ directory of the DVD or USB bootable they are named in the following way :

*CustomSettings_"**ID Tasksequence**".ini



Now in the Hydration Kit all applications must be accept by default all domain and it will be available  for all OS Culture (Fr - EN....). Nowadays it works only for French OS Culture. I found a way to find OS Culture during the OSD. I wrote this Function :

```posh

function Get-OsCulture
{
	[CmdletBinding()]
	param ()
	
	$oscode = Get-WmiObject Win32_OperatingSystem -ComputerName localhost -ErrorAction continue | foreach { $_.oslanguage }
	$Culture =switch ($oscode) `
	{
		1033 { "English" };
		1036 { "French" };
		default { "Unknown" }
	}

    switch ($Culture)
    {
        'French'{
        $AdminAccount = "Administrateur"
		$AdminGroupsAccount = "Administrateurs"
		$AdminDomaintGroupsAccount= "Admins du domaine"

        return $AdminAccount,$AdminGroupsAccount,$AdminDomaintGroupsAccount
         }
        {'English'}{
        $AdminAccount = "Administrator"
		$AdminGroupsAccount = "Administrators"
		$AdminDomaintGroupsAccount = "Domain Admins"
        
        return $AdminAccount,$AdminGroupsAccount,$AdminDomaintGroupsAccount
        }
        
        Default {}
    }
}
# Use varaibles to get AD account groups
$AdminAccount=(Get-OsCulture)[0]
$AdminGroupsAccount = (Get-OsCulture)[1]
$AdminDomaintGroupsAccount = (Get-OsCulture)[2]
```




# Applications

All Applications or wrapper used PowerShell.

## One Example

Here the code for the PowerShell Application **_Configure - Create AD Structure_**. Actually the script doesn't use the **_Get-OsCulture_**.


```posh
<#
	Solution HydrationKit
	Action : Configure - AD Structure
    Created:	 2016-02-1
    Version:	 1.0

	This script is provided "AS IS" with no warranties, confers no rights and 
	is not supported by the author. 

    Author - Jérôme Bezet-Torres
    Twitter: @JM2K69
    
#>

# Determine where to do the logging 
$tsenv = New-Object -COMObject Microsoft.SMS.TSEnvironment
$logPath = $tsenv.Value("LogPath")
$logFile = "$logPath\$($myInvocation.MyCommand).log"

# Start the logging 
Start-Transcript $logFile
Write-Host "Logging to $logFile"

#Get ADDS DOMAIN Info
$info = get-addomain
$infoC = $info.DNSRoot
function get-DN ($param1)
{
	$Rdomain = ""
	$split = $param1.split(".")
	$nb = $split.Count
	
	switch ($nb)
	{
		2{
			
			$domain1 = $split[0]
			$Ext1 = $split[1]
			$Rdomain = "dc=$domain1,dc=$ext1"
			
			return $Rdomain
		}
		3{
			$sdomain = $split[0]
			$domain = $split[1]
			$Ext = $split[2]
			
			$Rdomain = "dc=$sdomain,dc=$domain,dc=$ext"
			
			return $Rdomain
		}
	}
}

$dom = get-DN $infoC

# Décalaration des variables
$1niveau = @("Matériels", "Utilisateurs", "Ordinateurs")
$Users = $1niveau[1]
$ordi = $1niveau[2]
$Services = @("Marketing", "Informatique", "Direction", "Comptabilité", "Production", "Comptes_services")
$Groupes = @("Groupes_DL", "Groupes_GL", "Groupes_U")
$Type = @("Fixes", "Portables", "Serveurs")


#Création des Ou de 1 er Niveaux Matériels, Utilisateurs, Ordinateurs
Foreach ($x in $1niveau) { New-ADOrganizationalUnit -Name $x -ProtectedFromAccidentalDeletion $false }

#Création des Ou sous Utilisteurs

New-ADOrganizationalUnit -Name "Services" -path "ou=$Users,$dom" -ProtectedFromAccidentalDeletion $false
Foreach ($x in $Services) { New-ADOrganizationalUnit -Name $x -path "ou=Services,ou=$Users,$dom" -ProtectedFromAccidentalDeletion $false }

New-ADOrganizationalUnit -Name "Groupes" -path "ou=$Users,$dom" -ProtectedFromAccidentalDeletion $false
Foreach ($x in $Groupes) { New-ADOrganizationalUnit -Name $x -path "ou=Groupes,ou=$Users,$dom" -ProtectedFromAccidentalDeletion $false }

#Création des Ou sous Ordinateurs

Foreach ($x in $Type) { New-ADOrganizationalUnit -Name $x -path "ou=$ordi,$dom" -ProtectedFromAccidentalDeletion $false }

New-ADGroup -DisplayName "G_Informatique" -GroupScope Global -Name "G_Informatique" -Path "OU=Groupes_GL,OU=Groupes,OU=Utilisateurs,$dom"
New-ADGroup -DisplayName "G_Marketing" -GroupScope Global -Name "G_Marketing" -Path "OU=Groupes_GL,OU=Groupes,OU=Utilisateurs,$dom"
New-ADGroup -DisplayName "G_Comptabilité" -GroupScope Global -Name "G_Comptabilité" -Path "OU=Groupes_GL,OU=Groupes,OU=Utilisateurs,$dom"
New-ADGroup -DisplayName "G_Directions" -GroupScope Global -Name "G_Directions" -Path "OU=Groupes_GL,OU=Groupes,OU=Utilisateurs,$dom"
New-ADGroup -DisplayName "G_Tech Support" -GroupScope Global -Name "G_Tech Support" -Path "OU=Groupes_GL,OU=Groupes,OU=Utilisateurs,$dom"
New-ADGroup -DisplayName "G_MDT" -GroupScope Global -Name "G_MDT" -Path "OU=Groupes_GL,OU=Groupes,OU=Utilisateurs,$dom"
New-ADGroup -DisplayName "G_SysCenterSuite" -GroupScope Global -Name "G_SysCenterSuite" -Path "OU=Groupes_GL,OU=Groupes,OU=Utilisateurs,$dom"


$Informatiques = @("Julien", "Laurent", "Jerome", "Fabien")
$Password = "123+aze"
$ou = "OU=Informatique,ou=Services,ou=$Users,$dom"
foreach ($p in $Informatiques)
{
	New-ADUser -Name $p -GivenName $p -DisplayName "$p" -SamAccountName $p -Path $OU -PasswordNeverExpires 1 -Description "Administrateurs"
	Set-ADAccountPassword -Identity $p -NewPassword (ConvertTo-SecureString -AsPlainText $Password -Force)
	Add-ADPrincipalGroupMembership -Identity $p -MemberOf "Administrateurs", "Admins du domaine", "G_Informatique"
	Enable-ADAccount -Identity $p
}

$Marketing = @("Juliette", "Sylvie", "Anne", "Stéphane")
$Password = "123+aze"
$ou = "OU=Marketing,ou=Services,ou=$Users,$dom"
foreach ($p in $Marketing)
{
	New-ADUser -Name $p -GivenName $p -DisplayName "$p" -SamAccountName $p -Path $OU -PasswordNeverExpires 1 -Description "Employés Marketing"
	Set-ADAccountPassword -Identity $p -NewPassword (ConvertTo-SecureString -AsPlainText $Password -Force)
	Add-ADPrincipalGroupMembership -Identity $p -MemberOf "G_Marketing"
	Enable-ADAccount -Identity $p
}

$Comptabilité = @("Maria", "Nathalie", "Jean", "Bernard")
$Password = "123+aze"
$ou = "OU=Comptabilité,ou=Services,ou=$Users,$dom"
foreach ($p in $Comptabilité)
{
	New-ADUser -Name $p -GivenName $p -DisplayName "$p" -SamAccountName $p -Path $OU -PasswordNeverExpires 1 -Description "Employés Comptabilité"
	Set-ADAccountPassword -Identity $p -NewPassword (ConvertTo-SecureString -AsPlainText $Password -Force)
	Add-ADPrincipalGroupMembership -Identity $p -MemberOf "G_Comptabilité"
	Enable-ADAccount -Identity $p
}

$Directions = @("Alain", "Cammille")
$Password = "123+aze"
$ou = "OU=Direction,ou=Services,ou=$Users,$dom"
foreach ($p in $Directions)
{
	New-ADUser -Name $p -GivenName $p -DisplayName "$p" -SamAccountName $p -Path $OU -PasswordNeverExpires 1 -Description "Direction"
	Set-ADAccountPassword -Identity $p -NewPassword (ConvertTo-SecureString -AsPlainText $Password -Force)
	Add-ADPrincipalGroupMembership -Identity $p -MemberOf "G_Directions"
	Enable-ADAccount -Identity $p
}

$TechSupport = @("Jule", "Liza", "Kym", "Olivier")
$Password = "123+aze"
$ou = "OU=Informatique,ou=Services,ou=$Users,$dom"
foreach ($p in $TechSupport)
{
	New-ADUser -Name $p -GivenName $p -DisplayName "$p" -SamAccountName $p -Path $OU -PasswordNeverExpires 1 -Description "Employés Informatiques"
	Set-ADAccountPassword -Identity $p -NewPassword (ConvertTo-SecureString -AsPlainText $Password -Force)
	Add-ADPrincipalGroupMembership -Identity $p -MemberOf "G_Tech Support"
	Enable-ADAccount -Identity $p
}

$MDT = @("MDT_JD", "MDT_BA", "MDT_User")
$Password = "123+aze"
$ou = "OU=Comptes_services,ou=Services,ou=$Users,$dom"
foreach ($p in $MDT)
{
	New-ADUser -Name $p -GivenName $p -DisplayName "$p" -SamAccountName $p -Path $OU -PasswordNeverExpires 1 -Description "MDT_users"
	Set-ADAccountPassword -Identity $p -NewPassword (ConvertTo-SecureString -AsPlainText $Password -Force)
	Add-ADPrincipalGroupMembership -Identity $p -MemberOf "G_MDT", "G_Informatique"
	set-adUser -identity $p -PasswordNeverExpires $true
	Enable-ADAccount -Identity $p
}

$MDT = @("CM_NAA", "CM_CP", "CM_SR", "CM_JD", "CM_Admin")
$Password = "123+aze"
$ou = "OU=Comptes_services,ou=Services,ou=$Users,$dom"
foreach ($p in $MDT)
{
	New-ADUser -Name $p -GivenName $p -DisplayName "$p" -SamAccountName $p -Path $OU -PasswordNeverExpires 1 -Description "SystemCenter_users"
	Set-ADAccountPassword -Identity $p -NewPassword (ConvertTo-SecureString -AsPlainText $Password -Force)
	Add-ADPrincipalGroupMembership -Identity $p -MemberOf "G_SysCenterSuite", "Administrateurs"
	set-adUser -identity $p -PasswordNeverExpires $true
	Enable-ADAccount -Identity $p
}

# Stop logging 
Stop-Transcript
```

It will be update Later.