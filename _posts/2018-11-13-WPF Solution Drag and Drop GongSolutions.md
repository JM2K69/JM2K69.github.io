---
layout: post
title: "WPF Solution Drag and Drop GongSolutions"
date: 2018-11-14
tags: [PowerShell,WPF,XAML,Mahapps,WPF.DragDrop ]
---

* TOC
{:toc}

Today I found an other solution with free library to use in you `WPF` applications.

![ComputerSection](/img/Gongs.PNG)

# 1.Features

* Works with multiple selections.
* Can drag data within the same control to re-order, or between controls.
* Works with `ListBox`, `ListView`, `TreeView`, `DataGrid` and any other `ItemsControl`.
* Can insert, move or copy an item into a collection of the same control or into another.
* Can display Adorners to give the user visual feedback of the operation in progress.

# 2.Installation

You need at least VS Community 2015 or higher. Or Kaxaml to use the solution but for install, we need VS Community. 
Or: You take the latest version from NuGet: [https://www.nuget.org/packages/gong-wpf-dragdrop](https://www.nuget.org/packages/gong-wpf-dragdrop/)
I used the latest version in beta : 

```powershell
Install-Package gong-wpf-dragdrop -Version 2.0.0-alpha0099
``` 

# 2.  Namespace in your XAML file ?

In your `XAML file` you can add in specify the namespace.


To use `GongSolutions.WPF.DragDrop` in your application you need to add the namespace to your Xaml files.

```xaml
xmlns:dd="urn:gong-wpf-dragdrop"
```

or

```xaml
xmlns:dd="clr-namespace:GongSolutions.Wpf.DragDrop;assembly=GongSolutions.Wpf.DragDrop"
```


# 3. How to use Control it ?

This Example create a `ListBox`.

```xaml
 <ListBox 
    Height="Auto"
    x:Name="Button"
    HorizontalAlignment="Left"
    dd:DragDrop.IsDragSource="True"
    dd:DragDrop.IsDropTarget="True"
    dd:DragDrop.UseDefaultDragAdorner="False"
    dd:DragDrop.UseDefaultEffectDataTemplate="False"
    ItemContainerStyle="{x:Null}"
    SelectionMode="Extended">
        <ListBox.ItemsPanel>
            <ItemsPanelTemplate>
                <StackPanel Margin="5" Orientation="vertical" />
            </ItemsPanelTemplate>
        </ListBox.ItemsPanel>

        ### Your Objects

 </ListBox>


```
# 3. Demo Time in Kaxaml

## 3.1 References

We need to add some references in Kaxaml.
 
![computerSection](/img/referenceKaxaml.PNG)


## 3.2 Preview

This page is a example : 

![ComputerSection](/img/GongsWPF.gif)

## 3.3 The code

My page is available on my Github depot [here](https://github.com/JM2K69/Kaxaml-Page-WPF.DragDrop-). Or you can run this command.

```powershell
 git clone https://github.com/JM2K69/Kaxaml-Page-WPF.DragDrop-.git
```

Written by Jérôme Bezet-Torres @JM2K69.
