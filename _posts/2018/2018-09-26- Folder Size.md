---
layout: single
title: "Folder Size"
date: 2018-09-26
tags: 
  - Powershell
  - XAML
categories:
  - Powershell
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

 
In this post we cover the way to create report on folder Size on your local PC. The interface Look like this :

![ComputerSection](/img/Size.gif)

# 1.  The Tool

With this Tool I use XAML and WPF like all my others applications and in this I used LiveChart for create Charts in WPF. This tool can save the Charts to an image `PNG` and you can export to an `Excel` Sheet. I use the TreeView form @dev4sys read [here](https://www.dev4sys.com/2018/04/powershell-wpf-customize-treeview-icon.html) how to customize TreeView Icon.


# 2.  How to use it ?

The project is available on my github profile.The link for the download is [here]( https://github.com/JM2K69/Powershell_WPF_GUI/tree/master/Projects/Size).

To use it you can first Unblock File or you use the Apps from **[MVP] Damien Van Robaeys** [here](http://www.systanddeploy.com/2018/08/unblock-your-downloaded-files-with.html) ;-) ...
```powershell
get-ChildItem -recurse | Unblock-File
```

* When you choose the Letter Disk the TreeView will refresh all folder you can't to the card folder from different Letter Disk.

* When you Click to Save Button the Apps export the current Active Windows to an PNG file.

![ComputerSection](/img/Save.png)

![ComputerSection](/img/Export.png)

And we have the file in the same Application's directory.

* When you Click on Export the application export all the data to an `Excel Sheet` and it also save to the Application's directory.

Written by Jérôme Bezet-Torres @JM2K69.