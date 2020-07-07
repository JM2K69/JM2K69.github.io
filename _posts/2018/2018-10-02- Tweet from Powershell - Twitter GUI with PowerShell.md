---
layout: single
title: "Tweet from Powershell - Twitter GUI with PowerShell and Material Design"
date: 2018-10-02
tags: 
  - Powershell
  - 'Twitter'
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

 
![ComputerSection](/img/Pwsh.gif)

In this post I create my first WPF application with MaterialDesign, you have the possibility to post tweets, for that you need at first the Poshtwit powershell module, thank's to `@pcgeek86` Trevor Sullivan [MVP].  If the module isn't present you receive an error and the application will close.

# 1.  Configure Poshtwit

This module enables you to post tweets. You need to configure the token.json file with your own details from [https://apps.twitter.com](https://apps.twitter.com). 
* Create an application,
* Generate an access token for the application, 
* **Optional** you can run the Set-PoshTwitToken command to configure the : **1)** Consumer Secret, **2)** Consumer Key, **3)** Access Token, **4)** Access Secret.
* Or you can kept all and put it in the Application 
* Once you've done this, you can run Publish-Tweet to send a tweet.

The second method is to insert tour Twitter Application secret into the `Powershell file` like this.

![ComputerSection](/img/Config_Apps_tweet.PNG)


# 2.  How to use it ?

The project is available on my github profile.The link for the download is [here]( https://github.com/JM2K69/Powershell_WPF_GUI/tree/master/Projects/Pwsh%20Tweet%20Apps).

To use it you can first `Unblock File ` or you use the Apps from **[MVP] Damien Van Robaeys** [here](http://www.systanddeploy.com/2018/08/unblock-your-downloaded-files-with.html) ;-) ...
```powershell
get-ChildItem -recurse | Unblock-File
```

Whit this application you can post Tweet in your Timeline.

In the next post I talk aubout How to use MaterialDesing with powershell.


Written by Jérôme Bezet-Torres @JM2K69.