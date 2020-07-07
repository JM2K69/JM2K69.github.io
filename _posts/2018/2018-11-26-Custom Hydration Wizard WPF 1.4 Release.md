---
layout: single
title: "Custom Hydration Wizard WPF 1.4 Release"
date: 2018-11-26
tags: 
  - Powershell
  - 'XAML'
categories:
  - Powershell
  - WPF
  - 'HydrationKit'
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


I have update my Custom Wizard HydrationKit, I add another part Summary and i add some icons int Tabitem Header.


# 1. Design Change

I my TabItem I add some icon from Mahapps Icon.

![ComputerSection](/img/WCRI_INT1.PNG)

To do this you must add `TabItem Header Template` with a **DataTemplate**.

```xaml
<TabItem>
      <TabItem.HeaderTemplate>
                    <DataTemplate>
                        <StackPanel Orientation="Horizontal">
                               <iconPacks:PackIconFontAwesome Kind="QuestionCircleRegular"  Width="24" Height="24" Margin="0 12 0 0 "/>
                               <Label Margin="2 0 0 0" Content="VM Info" Foreground="{DynamicResource {x:Static SystemColors.ActiveBorderBrushKey}}"/>
                         </StackPanel>
                     </DataTemplate>
             </TabItem.HeaderTemplate>
    ## Your Content

</TabItem>
```

# 2. Summary Part

In this summary you can observe all the changes you have made with the Custom Hydration Wizard.

![ComputerSection](/img/CWHUpdate.gif)

# 3. How to download my Application

The script is available in my github repository [Here](https://github.com/JM2K69/Custom-Wizard-Hydration/releases/download/1.4/CWH.zip).

**Please don't forget to Unblock Files** 

To use it you can first `Unblock File ` or you use the Apps from **[MVP] Damien Van Robaeys** [here](http://www.systanddeploy.com/2018/08/unblock-your-downloaded-files-with.html) ;-) ...

```powershell
get-ChildItem -recurse | Unblock-File
```
# 4. Update 1809 ADK Problem

Be carefull if you use the latest ADK 1809 there is some problem with `WPF` in `WinPe` there is some workaround :

* [@SeguraOSD](https://twitter.com/SeguraOSD) Apply October CU to your WinPe 1809 only : [Here](https://twitter.com/SeguraOSD/status/1050596674807054336)
* [@SeguraOSD](https://twitter.com/ferozekhan267oa)Copy 1 file from ADK 1803 into your Boot.wim [Here](https://twitter.com/ferozekhan267oa/status/1049169276656074753)

Thanks to [@SeguraOSD](https://twitter.com/SeguraOSD) he make a very good job to find the real problem, it missing 2 Dll Files. You can find the blog post from is web site [here](https://www.osdeploy.com/blog/winpe-10-1809-wpf-dramarama). 

You can use a `PowerShell` script created by Nickolaj Andersen [MVP]. You can read the blog post a catch the `PowerShell Script` [Here](http://www.scconfigmgr.com/2018/11/23/fix-windows-adk-version-1809-issue-with-crashing-wpf-applications/)

![ComputerSection](/img/Fix.PNG)



Written by Jérôme Bezet-Torres @JM2K69.
