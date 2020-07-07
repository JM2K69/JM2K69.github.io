---
layout: single
title: "Fake Windows 10 Update"
date: 2018-01-05
tags: 
  - Powershell
  - 'Fake Windows 10'
  - XAML
categories:
  - Powershell
  - 'WPF'
  - VMware
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


You know the @syst_and_deploy project. The fake Windows 10 WPF, I updated his project by adding a boot screen and an installation screen for Windows updates.

**The original project**

![alt text](https://3.bp.blogspot.com/-hEArzfknu8c/WhWHlngt69I/AAAAAAAAGyc/TGthWiaQ_SgYgJFzinkGJZqGdsLPQ5aOgCLcBGAs/s1600/win10%2BWPF%2BGIF.gif)

**The project now**

![ComputerSection](/img/Windows 10 Update.gif)

* TOC
{:toc}

# 1. Splash Screen

For create a splash screen we need to use runspaces. A Runspace is a specific instance of PowerShell which contains MODIFIABLE collections of commands, providers, variables, functions, and language elements that are available to the command line user (or more generically, to the "host" application).
Yes but now how to use it.

## 1.1 Create the Runspace

```powershell

    $hash = [hashtable]::Synchronized(@{})
    $runspace = [runspacefactory]::CreateRunspace()
    $runspace.ApartmentState = "STA"
    $Runspace.ThreadOptions = "ReuseThread"
    $runspace.Open()
    $runspace.SessionStateProxy.SetVariable("hash",$hash) 
    $Pwshell = [PowerShell]::Create()

```
Now we can add script to the variable `$Pwshell`in order to create the WPF with XAML and Mahapps.

```Ppowershell
 $Pwshell.AddScript({
    $xml = [xml]@"
     <Window
	xmlns:Controls="clr-namespace:MahApps.Metro.Controls;assembly=MahApps.Metro"
	xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
	xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
	x:Name="WindowSplash" Title="SplashScreen" WindowStyle="None" WindowStartupLocation="CenterScreen"
	Background="Black" ShowInTaskbar ="true" 
	Width="900" Height="650" ResizeMode = "NoResize" >
	
	<Grid>
		<Grid.RowDefinitions>
            <RowDefinition Height="70"/>
            <RowDefinition/>
        </Grid.RowDefinitions>
		
		<Grid Grid.Row="0" x:Name="Header" >	
			<StackPanel Orientation="Horizontal" HorizontalAlignment="Left" VerticalAlignment="Stretch" Margin="20,10,0,0">       
				
			    
			</StackPanel> 
		</Grid>
        <Grid Grid.Row="1" >
		 	<StackPanel Orientation="Vertical" HorizontalAlignment="Center" VerticalAlignment="Center" Margin="5,5,5,5">
             <Image x:Name="Logo" RenderOptions.BitmapScalingMode="Fant" HorizontalAlignment="Center" Margin="0,0,0,15" Width="100" Height="100" VerticalAlignment="Top" />    
             <Label x:Name = "LoadingLabel"  Foreground="White" HorizontalAlignment="Center" VerticalAlignment="Center" FontSize="24" Margin = "0,0,0,0"/>
				<Controls:ProgressRing IsActive="{Binding IsActive}" Foreground="White" HorizontalAlignment="Center"  Margin = "0,30,0,0" Width="40" Height="8"/>
			</StackPanel>	
        </Grid>
	</Grid>
		
</Window> 
"@
 
 
    $reader = New-Object System.Xml.XmlNodeReader $xml
    $hash.window = [Windows.Markup.XamlReader]::Load($reader)
    $hash.LoadingLabel = $hash.window.FindName("LoadingLabel")
    $hash.Logo = $hash.window.FindName("Logo")
    $hash.Logo.Source=".\images\Winlogo.png"
    $hash.window.ShowDialog() 
    
}) | Out-Null
```
With this the splash screen will look like this :

![ComputerSection](/img/boot.PNG)

## 1.2 Functions for Start and Close Runspace

### Start function
```powershell
function Start-SplashScreen (){
    $Pwshell.Runspace = $runspace
    $script:handle = $Pwshell.BeginInvoke() 
}
```

### Close function
```powershell
function close-SplashScreen (){
    $hash.window.Dispatcher.Invoke("Normal",[action]{ $hash.window.close() })
    $Pwshell.EndInvoke($handle) | Out-Null
    #$runspace.Close() | Out-Null
    
}
```
# 2. Final Splash Sreen

For the final screen, create a separate script and it call by a function in the main script when the user want to quit the GUI.

I make loop for simulate the installation of windows 10 update.

## 2.1 The Loop

```powershell
Do
	{			
        $Current_Step_Number++

		If ($Previous_Step -ne $Next_Step)
			{
				$Current_Step_Total = $Current_Step_Number / $Total_Step * 100 
				$Round_Current_Step_Total = [math]::Round($Current_Step_Total)
				$Percent_Complete = "$Round_Current_Step_Total %" 
				Update_progressBar "$Percent_Complete complete" "Install  the update $Current_Step_Number of $Total_Step "							
			}		
  }
        
       
until ($Current_Step_Number -eq $Global:Total_Step) 
```
## Update the StatusBar 

When you run runspace we need to create function to access to all objects which are runninng into the runspace it much easier.

```powershell

Function Update_progressBar {
    param(
    [String]$script:Step_Name,
    [String]$script:Step_Status		
    )
        $syncProgress.ProgressRing.Dispatcher.Invoke("Normal",[action]{	
        $syncProgress.Label.Content=$script:Step_Name
        $syncProgress.Label2.Content=$script:Step_Status			
    })
}

```
The project is available on my github profile.The link for the download is [here]( https://github.com/JM2K69/Powershell_WPF_GUI/tree/master/Projects/Windows10_Design_WPF)

Written by Jérôme Bezet-Torres @JM2K69.
