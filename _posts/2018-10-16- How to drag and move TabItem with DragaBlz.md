---
layout: post
title: "How to drag and move TabItem with DragaBlz"
date: 2018-10-16
tags: [PowerShell,WPF,XAML,MaterialDesign,DragaBlz ]
---

* TOC
{:toc}

I discover a good thing for all your `#WPF` Applications you can use it with the two popular `Themes` **Mahapps** and **Material Design**. You can find they own `Github` project [Mahapps](https://github.com/MahApps/MahApps.Metro) and [Material Design](https://github.com/MaterialDesignInXAML/MaterialDesignInXamlToolkit) and [DragaBlz](https://github.com/ButchersBoy/Dragablz)

Today we focus on DragaBlz with Material Design
With the demo project you can view the XAML for each objects.
# 1.  How have all the Library ?

* In Visual Studio Community Create an WPF Application and you can manage your nuget package.

![ComputerSection](/img/VS2017N.PNG)

* And we can search Material Design Themes

![ComputerSection](/img/MD1.PNG)

* In your Visual Studio you can Install the nuget package **Dragablz**
```powershell
Install-Package Dragablz -Version 0.0.3.203
```
* When done you can retrieve all the library in your source directory.



# 2.  Namespace in your XAML file ?

In your `XAML file` you can add in specify the namespace. In this example I have Mahapps Windows.

```XAML
<Window 
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:dragablz="clr-namespace:Dragablz;assembly=Dragablz"
        xmlns:materialDesign="clr-namespace:MaterialDesignThemes.Wpf;assembly=MaterialDesignThemes.Wpf"
        Title="DragaBlz WPF Demo" Height="350" Width="525"
        ResizeMode="NoResize"
        WindowStyle="None"
        WindowStartupLocation="CenterScreen">
```
You can add this line
**xmlns:materialDesign="clr-namespace:MaterialDesignThemes.Wpf;assembly=MaterialDesignThemes.Wpf"** in order to create some `Material Design` **Objects**.
For the Dragablz in the namespace you can add the line
 **xmlns:dragablz="clr-namespace:Dragablz;assembly=Dragablz"**


Then you need add some ressources in the **ResourceDictionary**. 
```XAML
    <Window.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                 <ResourceDictionary>
                  <!-- Primary colour -->
                    <ResourceDictionary.MergedDictionaries>
                          <ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/MaterialDesignColor.Blue.xaml" />
                    </ResourceDictionary.MergedDictionaries>
                    
                    <SolidColorBrush x:Key="PrimaryHueLightBrush" Color="{StaticResource Primary100}"/>
                    <SolidColorBrush x:Key="PrimaryHueLightForegroundBrush" Color="{StaticResource Primary100Foreground}"/>
                    <SolidColorBrush x:Key="PrimaryHueMidBrush" Color="{StaticResource Primary500}"/>
                    <SolidColorBrush x:Key="PrimaryHueMidForegroundBrush" Color="{StaticResource Primary500Foreground}"/>
                    <SolidColorBrush x:Key="PrimaryHueDarkBrush" Color="{StaticResource Primary700}"/>
                    <SolidColorBrush x:Key="PrimaryHueDarkForegroundBrush" Color="{StaticResource Primary700Foreground}"/>
                </ResourceDictionary>
 
                <!-- secondary colour -->
                <ResourceDictionary>
                     <ResourceDictionary.MergedDictionaries>
                     <ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/MaterialDesignColor.Yellow.xaml" />
                    </ResourceDictionary.MergedDictionaries>
                     <SolidColorBrush x:Key="SecondaryAccentBrush" Color="{StaticResource Accent200}"/>
                    <SolidColorBrush x:Key="SecondaryAccentForegroundBrush" Color="{StaticResource Accent200Foreground}"/>
                </ResourceDictionary>
 
                <!-- Include the Dragablz Material Design style -->
                <ResourceDictionary Source="pack://application:,,,/Dragablz;component/Themes/materialdesign.xaml"/>
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Defaults.xaml" />  
                </ResourceDictionary.MergedDictionaries>
 
            <!-- tell Dragablz tab control to use the Material Design theme -->
            <Style TargetType="{x:Type dragablz:TabablzControl}" BasedOn="{StaticResource MaterialDesignTabablzControlStyle}" />
        </ResourceDictionary>
    </Window.Resources>        
```
You need Add in your on Style for the Control `dragablz:TabablzControl`and you specify **BasedOn="{StaticResource MaterialDesignTabablzControlStyle}"**
# 3. Dragablz Control
This Example create a TabControl Dragablz.
```XAML
<dragablz:TabablzControl Margin="8">
    <dragablz:TabablzControl.InterTabController>
        <dragablz:InterTabController />
    </dragablz:TabablzControl.InterTabController>
    <TabItem Header="Dragablz" IsSelected="True">
        <TextBlock>Hello World</TextBlock>
    </TabItem>
    <TabItem Header="#WPF">
        <TextBlock>We Have Tearable Tabs!</TextBlock>
    </TabItem>
    <TabItem Header="#XAML">
        <TextBlock>Wellcom in XAML and WPF!</TextBlock>
    </TabItem>
</dragablz:TabablzControl>
```
# 3. My Application

This application is a example for Dragablz preview in `Windows Presentation Foundation` with XAML and PowerShell

![ComputerSection](/img/DagaBlz.gif)
 
My Application is available on my Github depot [here](https://github.com/JM2K69/Powershell_WPF_GUI/tree/master/Projects/DragaBlz).

Written by Jérôme Bezet-Torres @JM2K69.
