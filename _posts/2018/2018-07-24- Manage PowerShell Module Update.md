---
layout: single
title: "Manage PowerShell Module Update"
date: 2018-07-24
tags: 
  - Powershell
  - 'Modules Powershell'
  - XAML
categories:
  - Powershell
  - 'Material Design'
  - WPF
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



In my previous Blog [post](_https://jm2k69.github.io/2018-07-16-Manage-PowerShell-Module/) we talk about updating modules with my tools but my tools have some mistakes no runspace thank's to `@trevor_smsagent`. Now you can update some module and not all in the main time.


# 1.  The Update part update

In this part by **Default** when you Click on `Find-Module` the script search for All installed module if an update is available on PowerShell Gallery and it compare all version for the Scope `CurrentUser`.

Now during the phase wen the script search update the GUI don't freeze because I use a runspace. How to create a `runspace` with `PowerShell`
I you want to learn more look [here](_https://smsagent.wordpress.com/2015/09/07/powershell-tip-utilizing-runspaces-for-responsive-wpf-gui-applications/) `@trevor_smsagent` blog post how to use runspace.

![ComputerSection](/img/WUM8.PNG)

![ComputerSection](/img/WUM10.PNG)

Now you can selected module to be updated.
 
![ComputerSection](/img/WUM1_1.gif)

# 2.  The Install Pwsh module part update

You can now search module in beta with the parameter `-AllowPrerelease` in the two Buttons `Find-Module` and  `Install-Module`

![ComputerSection](/img/WUM11.PNG)

The project is available on my github profile.The link for the download is [here]( https://github.com/JM2K69/Powershell_WPF_GUI/tree/master/Projects/Module%20Pwsh%20Management).

Written by Jérôme Bezet-Torres @JM2K69.