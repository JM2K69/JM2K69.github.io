---
layout: single
title: "WPF Explorer 1.2"
date: 2020-07-16
tags: 
  - Powershell
  - Controls
  - Explorer
  - XAML
categories:
  - Powershell
  - WPF
  - Mahapps
published: true
comments: true
author_profile: true
header:
  overlay_image: /img/headers/Code04_1920x500.jpg
  og_image: /assets/images/page-header-image.png
  actions:
    - label: "Github"
      url: "https://github.com/JM2K69/WPF_CONTROL_EXPLORER"
#header:
#  teaserlogo:
#  teaser: ''
#  image: #images/headers/Code01_1920x500.jpg
#  caption:
gallery:
  - image_path: ''
    url: ''
    title: ''
toc: true
toc_sticky: true
toc_label: "Table of content"
---


Today I release an update for un application created by `SMSAgent` **@SMSagentTrevor** th original post can be read [here](https://smsagent.blog/tools/wpf-control-explorer/)

![Auhtors_img](/img/WPF_Controller.gif){: .align-center}


# 1. The Interface

I decided to use **Mahapps theme** for this Application, because it's very easy to use with XAML and PowerShell.

# 2. Why this application

If you wish to create your own WPF applications with PowerShell and XAML, you want to add controls and of course find the properties of these controls as well as the associated events. Well this application will allow you to discover all these aspects on the WPF controls.

But not that if you ever want to add controls that come from the community that you can find on the nuget galery or on github and it is possible to discover and load these assemblies to read the controls they contain.

>Don't forget by default the Application load the >default PresentationFramework if you want to load a >custom assembly use the toggle switch.

>*Ping* **@syst_and_deploy** 😊

![Load_assembly](/img/WPF_Controller_L.gif){: .align-center}

## 2.1 BROWSING CONTROLS
Select any control from the Control drop-down box. The .Net class name will be displayed underneath.

![Load_assembly](/img/WPF_Controler_C.PNG){: .align-center}

## 2.2 CONTROL MEMBERS
Select to view either the properties, methods or events defined in that control.

![Load_assembly](/img/WPF_Controler_M.PNG){: .align-center}

## 2.3 FILTERING
Simply start typing in the filter box to filter your results.  For methods, you can also filter further by checking the relevant checkbox, such as all “add_” methods.

![Load_assembly](/img/WPF_Controller_F.gif){: .align-center}


## 2.4 DEFINITIONS
Member definitions can be found underneath the member list box.  The definition shows you what type of data defines that member.  A bool type, for example, requires a boolean value of true or false.  Some definitions are other .Net classes, and you can search MSDN for that class name for more information on how that class is defined.

The {get;set} definition property shows you whether that property is read-only or can be set.

## 2.5 STATIC PROPERTIES
Some properties contain static members, ie there are certain values that can be used to define that property. The Visibility property is an example, where you can choose from Collapsed, Hidden or Visible, and these are displayed in the Static Properties box where appropriate.

# 3.Example
In this blog post use a custom assembly MaterialDesign extension created by **Philipp Spiegel** [Github link](https://github.com/spiegelp/MaterialDesignExtensions) wich provide some interesting custom controls for WPF.

In my further blog post I explain how to use the **Stepper** Controls from this library.😊

![Auhtors_img](/img/WPF_Controler_Ex.PNG){: .align-center}

# 4. How to download

![Auhtors_img](/img/Git1.png){: .align-center}

My project is available on Github [here](https://github.com/JM2K69/WPF_CONTROL_EXPLORER)


Thank's for reading.

Written by Jérôme Bezet-Torres @JM2K69.
