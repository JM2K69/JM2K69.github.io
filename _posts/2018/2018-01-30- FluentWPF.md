---
layout: single
title: "FLuentWPF"
date: 2018-01-30
tags: 
  - Powershell
  - FluentWPF
categories:
  - Powershell
  - XAML
  - Mahapps
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


You know Mahapps or Material Design and now we have the opportunity to use FluentWPF with PowerShell and XAML

# 1. FluentWPF

We can find the source on a MVP Jan Karger Github repository [here]( https://github.com/punker76/FluentWPF) or if we use Visual Studio Community 201-5-7 you can manage Nuget Package
```powershell

Install-Package FluentWPF

```


## 1.1 Visual

**First view Acrylic**

![ComputerSection](/img/AcrylicWindow.gif)

**Second view Reveal** 

![ComputerSection](/img/demo.gif)


## 1.2 How to use it

In your XAML code you need to insert a reference to `FluentWPF`

- [ ] **Insert** the reference in the XAML code

```xml
 
 <Window 
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:fw="clr-namespace:SourceChord.FluentWPF;assembly=FluentWPF"      
        Title="Windows 10 Fluent WPF Design" 
		Height="281" Width="335"
        WindowStartupLocation="CenterScreen" 
		ResizeMode="NoResize"
        fw:AcrylicWindow.Enabled="True">

```



- [ ] **Insert** ResourceDictionary in the XAML code

```xml
<Window.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
				<ResourceDictionary Source="resources\Icons.xaml" />
                <ResourceDictionary Source="pack://application:,,,/FluentWPF;component/Styles/Controls.xaml" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Window.Resources>
```

## 1.2 Basic Interface with 2 buttons

### XAML File
```xml
<Window 
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:fw="clr-namespace:SourceChord.FluentWPF;assembly=FluentWPF"      
        Title="Windows 10 Fluent WPF Design" 
		Height="281" Width="335"
        WindowStartupLocation="CenterScreen" 
		ResizeMode="NoResize"
        fw:AcrylicWindow.Enabled="True">
		
		
    <Window.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
				<ResourceDictionary Source="resources\Icons.xaml" />
                <ResourceDictionary Source="pack://application:,,,/FluentWPF;component/Styles/Controls.xaml" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Window.Resources>		
		
  
<Grid fw:PointerTracker.Enabled="True" Background="#01FFFFFF" Margin="3">
        <StackPanel VerticalAlignment="Center">
        <StackPanel Orientation="Horizontal">
    
            <Button Name="Welcome" Content="Welcome" HorizontalAlignment="Left" Margin="5" Width="75" Height="32"
                    Style="{StaticResource ButtonRevealStyle}"/>

            <Button Name="FluentWPF" Content="FluentWPF" HorizontalAlignment="Left" Margin="5" Width="75" Height="32"
                    Background="Transparent"
                    Style="{StaticResource ButtonRevealStyle}" />
    </StackPanel>
            <TextBox HorizontalAlignment="Left" Height="23" Margin="5" Text="TextBox" Width="120"
                 Style="{StaticResource TextBoxRevealStyle}"/>
        </StackPanel>
    </Grid>

</Window>        
```

### PowerShell Script

The file **FluentWPF.dll** is in a directory Assembly like Mahapps project.

```powershell
##Initialize######
[System.Reflection.Assembly]::LoadWithPartialName('presentationframework') 				| out-null
[System.Reflection.Assembly]::LoadFrom('assembly\FluentWPF.dll')       				| out-null
[String]$ScriptDirectory = split-path $myinvocation.mycommand.path
#$ScriptDirectory =(get-location).path
function LoadXml ($global:filename)
{
    $XamlLoader=(New-Object System.Xml.XmlDocument)
    $XamlLoader.Load($filename)
    return $XamlLoader
}

# Load MainWindow
$XamlMainWindow=LoadXml("$ScriptDirectory\Main.xaml")
$Reader=(New-Object System.Xml.XmlNodeReader $XamlMainWindow)
$Form=[Windows.Markup.XamlReader]::Load($Reader)

$Welcome=$Form.FindName("Welcome")
$FluentWPF=$Form.FindName("FluentWPF")
    
    
$FluentWPF.add_Click({
  
    
    $form.Close()

})


$Form.ShowDialog() | Out-Null


```
# 2. The result



![ComputerSection](/img/FluentWPF_01.gif)



Written by Jérôme Bezet-Torres @JM2K69.
