---
layout: single
title: "Material Design WPF Creator"
date: 2019-01-09
tags: 
  - Powershell
  - 'Material Design'
  - 'XAML'
  - 'Kaxaml'
categories:
  - Powershell
  - 'WPF'
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


If you want ta start `WPF` with Material Design this Project can help you. This  WPF application create a `Blank` project with all you need to start learning `WPF` and 'Material Design`. This Apps use a RadialMenu Thank for my friend **[MVP] Damien Van Robaeys** he always find great ideas ;-).

# 1. RadialMenu

If you want learn how to use this excellent library you read the blog post [here](http://www.systanddeploy.com/2018/12/create-cool-wpf-menu-with-powershell.html)


![ComputerSection](/img/RadialMenu.PNG)


# 2. Project Setting

In this part you must set settings for your project :

* Name Project
* Author

The project name is a mandatory parameter because a folder will be create with this name in the futher step.

![ComputerSection](/img/ProjectS.PNG)

# 3. Windows Properties

In this part you must set settings for your Windows Apps :

* The Width
* The Height
* The Windows Title
* The rezise Mode
* The WindowsStartupLocation
* The Theme
* The Primary color
* The Accent color

All this settings me be set to have a complete project.

![ComputerSection](/img/WindowsPs.PNG)

# 4. Create Project

This part is enable if all the settings are correct. The new project is create in the subFolder  `Project`

![ComputerSection](/img/CreatePS.PNG)

In the Folder **Apps1** you find your new Material Design Project.

![ComputerSection](/img/ProjectS1.PNG)

And if open the Powershell File create in this example `Apps1.ps1` you find the result : 

```powershell
#========================================================================
#
# Tool Name	: Apps1
# Author 	: JM2K69
#
#========================================================================

##Initialize######
[System.Reflection.Assembly]::LoadWithPartialName('presentationframework') | out-null
[System.Reflection.Assembly]::LoadFrom('assembly\MahApps.Metro.dll')       			| out-null
[System.Reflection.Assembly]::LoadFrom('assembly\System.Windows.Interactivity.dll') | out-null
[System.Reflection.Assembly]::LoadFrom('assembly\MaterialDesignThemes.Wpf.dll') 			| out-null
[System.Reflection.Assembly]::LoadFrom('assembly\MaterialDesignColors.dll')       			| out-null
[String]$ScriptDirectory = split-path $myinvocation.mycommand.path

function LoadXml ($global:filename)
{
    $XamlLoader=(New-Object System.Xml.XmlDocument)
    $XamlLoader.Load($filename)
    return $XamlLoader
}

# Load MainWindow
$XamlMainWindow=LoadXml("$ScriptDirectory\main.xaml")
$Reader=(New-Object System.Xml.XmlNodeReader $XamlMainWindow)
$Form=[Windows.Markup.XamlReader]::Load($Reader)


$XamlMainWindow.SelectNodes("//*[@Name]") | %{
    try {Set-Variable -Name "$("WPF_"+$_.Name)" -Value $Form.FindName($_.Name) -ErrorAction Stop}
    catch{throw}
    }
 
Function Get-FormVariables{
if ($global:ReadmeDisplay -ne $true){Write-host "If you need to reference this display again, run Get-FormVariables" -ForegroundColor Yellow;$global:ReadmeDisplay=$true}
write-host "Found the following interactable elements from our form" -ForegroundColor Cyan
get-variable *WPF*
}
  #Get-FormVariables


$WPF_App_Close.add_Click({

   $Form.Close()

})

$Form.ShowDialog() | Out-Null


```

**Becarefull** dont touch the two file in the Folder `Projects`

* **Blank.ps1**
* **Main.Xaml**

If this file are modify the Apps Maetrial Design WPF creator will not run normally.

# 4. Run Project

With this button you can run also the current project in creation.

![ComputerSection](/img/FinalS.PNG)

We find the Windows Title, the size, the windows can be rezise with a Grip control and one button to close your future Material Design WPF Apps.

# 5 How to Obtain

My **Material Design WPF Creator** is available on my Github repository [here](https://github.com/JM2K69/MaterialDesign_WPF_Creator). Or you can run this command.

```powershell
 git clone https://github.com/JM2K69/MaterialDesign_WPF_Creator.git
```


Thanks for reading.

Written by Jérôme Bezet-Torres @JM2K69.
