---
layout: post
title: "Comic IT with MaterialDesign"
date: 2018-12-21
tags: [PowerShell,XAML,WPF]
---

* TOC
{:toc}


Today a new Material Design application that allows to retrieve images on two websites `CommitStrip.com` and `Monkeyuser.com` using RSS feeds

# 1. The Application

![ComputerSection](/img/ComicIT1.PNG)

We can choose different setting :

* Language **Setting**

![ComputerSection](/img/ComicIT2.PNG)

* Period **Setting**

![ComputerSection](/img/ComicIT3.PNG)

Whe can choose the latest picture available on the web site or a `random` one.

# 2. My PowerShell function

In this project I create on function to work with `RSS feeds` website. The Commitstrip web have 2 language available.

```powershell
function Find-CommitStripImage {
    [CmdletBinding()]
    Param
    (
              
        [Parameter(Mandatory = $true)]
        [ValidateSet("EN","FR")]$lang,
        [Parameter(Mandatory = $true)]
        [ValidateSet("Yes","No")]$Random
    )

    begin {
        switch ($lang) {
            'FR' {       
                 $URLCommitStrip = "http://www.commitstrip.com/fr/feed/?"
                }
            'EN' {
                $URLCommitStrip = "http://www.commitstrip.com/en/feed/?"
            }
            Default {}
        }
        
    }
    process{
        switch ($Random)
        {
            'No' {        
                    $resquest =Invoke-RestMethod $URL
                    $Image = $(Invoke-WebRequest -uri $resquest[0].link).Images.src | where {$_ -like "https://***"}
                    [String]$Title = $resquest[0].title
                 }
            'Yes'{
                    $resquest =Invoke-RestMethod $URL
                    $NB = $resquest.Count
                    $PostNb=get-random -Minimum 0 -Maximum $NB
                    $Image = $(Invoke-WebRequest -uri $resquest[$PostNb].link).Images.src | where {$_ -like "https://***"}
                    $Title =  $resquest[$PostNb].title
                    [String]$Date = $resquest[$PostNb].pubDate
                 }
            Default {}
        }

    }
    end {
    $myObject = [PSCustomObject]@{
    Image     = $Image
    Title     = $Title
    Date      = $Date
        }
        return $myObject
    }
    
}

```
If you want to execute in Powershell you will have this output :

```powershell
PS C:\Users\JM2K69> Find-CommitStripImage -lang EN -Random No |fl

Image : https://www.commitstrip.com/wp-content/uploads/2018/12/Strip-Gilets-jaunes-ordinateur-pour-les-nouveaux-650-finalenglishV2.jpg
Title : A simple solution to a complex problem
Date  :

PS C:\Users\JM2K69> Find-CommitStripImage -lang EN -Random Yes |fl

Image : https://www.commitstrip.com/wp-content/uploads/2018/11/Strip-Plus-qua-coder-la-feature-650-finalenglish.jpg
Title : One final detail
Date  : Tue, 20 Nov 2018 15:03:00 +0000
```

With the parameter `Random` to **No** we don't have the date.

# 3. The Source

The application is available in my github repository [Here](https://github.com/JM2K69/ComictIT).

# 4. Demo Time

![ComputerSection](/img/commicit.gif)

Thanks for reading.

Written by Jérôme Bezet-Torres @JM2K69.
