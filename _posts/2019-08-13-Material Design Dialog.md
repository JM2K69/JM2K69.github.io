---
layout: post
title: "Material Design Dialog - Second Post"
date: 2019-08-13
tags: [PowerShell, Material, Design, XAML, WPF, Themes, Manager ]
published : true
---


With the latest version 2.6 of **MaterialDesignInXamlToolkit** I decided to make a series of posts on MaterialDesignXamlToolkit. In this first post we will look at how to change the themes in our #WPF application with `PowerShell`. Thank to **Kevin Bost** `@kitokeboo`. In this new Part we focused on Dialog in `MaterielDesignXamlToolkit`.

# 1.Dialogs

The Material Design In XAML Toolkit's dialogs implementation is designed to:

*Provide correct styling
*Allow any dialog to be constructed
*Compatible with code-behind
*Compatible with MVVM
*Compatible with pure XAML
*Work in applications with multiple windows

Dialogs are asynchronous so at some point you will have to deal with that in your code.

# 2.Ok but with which Control ?

The DialogHost control. It’s a content control, meaning the underlying content over which the popup dialog will be displayed can be targeted; to a specific area of your app, or the entire Window content.

```xml
<materialDesign:DialogHost>
    <materialDesign:DialogHost.DialogContent>
        <dialogContent />
    </materialDesign:DialogHost.DialogContent>
    <mainContent />
</materialDesign:DialogHost>
```

It's also possible to use `DialogHost.DialogContentTemplate` in order to define **Style**, **Font**  etc...

When the dialog is open, the underlying content will be disabled.

# 3 How to show and close Dialog

## 3.1 Open with XAML

There is a RoutedCommand can pe implemented in your `Button` vi the `CommandParameter` with the this code :

```xml
<Button Command="{x:Static md:DialogHost.OpenDialogCommand}" />
```

## 3.2 Open with Property IsOpen

Now The **Controls** `<materialDesign:DialogHost/>` have a Property IsOpen wich accept value : `True` or `False`

*My XAML Code :*

```xml
<materialDesign:DialogHost Name="Dialog1">
        <materialDesign:DialogHost.DialogContent>
            <StackPanel Margin="15">
                <TextBlock Margin="10" Text="My First dialog" />
                <CheckBox Name="Check_Me" Content="Check me"/>
                <Button Margin="10" Name="Close_ME" Content="Close" />
            </StackPanel>
        </materialDesign:DialogHost.DialogContent>

        <StackPanel HorizontalAlignment="Center" VerticalAlignment="Center">
            <Button Name="Show" Content="Show Dialog" Margin="0,0,0,10"/>
        </StackPanel>
    </materialDesign:DialogHost>
```

*My PS1 Code :*

```powershell
$Show = $Form.findname("Show")
$Dialog = $Form.findname("Dialog1")
$Check_ME =  $Form.findname("Check_Me")
$Close = $Form.findname("Close_ME")

$Show.add_Click({

$Dialog.IsOpen = $True

})

$Close.add_Click({
    $Dialog.IsOpen = $False
})```

## 3.1 One example with a CheckBox

In this example the first Dialog here the code :

*My XAML Code :*

```xml
<materialDesign:DialogHost Name="Dialog1">
        <materialDesign:DialogHost.DialogContent>
            <StackPanel Margin="15">
                <TextBlock Margin="10" Text="My First dialog" />
                <CheckBox Name="Check_Me" Content="Check me"/>
                <Button Margin="10" Name="Close_ME" Content="Close" />
            </StackPanel>
        </materialDesign:DialogHost.DialogContent>
        <StackPanel HorizontalAlignment="Center" VerticalAlignment="Center">
            <Button Name="Show" Content="Show Dialog" Margin="0,0,0,10"/>
        </StackPanel>
    </materialDesign:DialogHost>
```

*My PS1 Code :*

```powershell
[System.Reflection.Assembly]::LoadWithPartialName('presentationframework') |Out-Null
[System.Reflection.Assembly]::LoadFrom("assembly\System.Windows.Interactivity.dll") | Out-Null
[System.Reflection.Assembly]::LoadFrom("assembly\MaterialDesignThemes.Wpf.dll") | Out-Null
[System.Reflection.Assembly]::LoadFrom("assembly\MaterialDesignColors.dll") | Out-Null
[System.Reflection.Assembly]::LoadWithPartialName("System.Windows.Forms") | Out-Null

function LoadXml ($global:filename)
{
    $XamlLoader=(New-Object System.Xml.XmlDocument)
    $XamlLoader.Load($filename)
    return $XamlLoader
}

$XamlMainWindow=LoadXml("MonInterface.xaml")
$Reader=(New-Object System.Xml.XmlNodeReader $XamlMainWindow)
$Form=[Windows.Markup.XamlReader]::Load($Reader)
$Show = $Form.findname("Show")
$Dialog = $Form.findname("Dialog1")
$Check_ME =  $Form.findname("Check_Me")
$Close = $Form.findname("Close_ME")

$Show.add_Click({

$Dialog.IsOpen = $True

})

$Close.add_Click({

    if ($Check_ME.IsChecked -eq $true)
    {
        [System.Windows.Forms.MessageBox]::Show("You close the Dialog with the CheckBox Checked")
        $Check_ME.IsChecked = $False
    }
    else
    {
        [System.Windows.Forms.MessageBox]::Show("You close the Dialog without the CheckBox Checked")
    }

    $Dialog.IsOpen = $False
})

$Form.ShowDialog() | Out-Null
```

![Password_demo](/img/Dialog_CheckedBox.gif)

## 3.2 Example Credential

*My XAML Code :*

```xml
<materialDesign:DialogHost Name="Dialog1">
        <materialDesign:DialogHost.DialogContent>
            <StackPanel Margin="15">
              <StackPanel Orientation="Vertical">  
                <TextBlock Margin="10" Text="Enter your Credential" />
                <TextBox Name="Name" materialDesign:HintAssist.Hint="Name" />
                <PasswordBox Name="PasswordBox" 
                    materialDesign:HintAssist.Hint="Floating Password" 
                    materialDesign:HintAssist.Foreground="Green"
                    materialDesign:TextFieldAssist.UnderlineBrush="Green"
                    Style="{StaticResource MaterialDesignFloatingHintPasswordBox}" />
              </StackPanel>
                <Button Margin="10" Name="Close_ME" Content="Close" />
            </StackPanel>
        </materialDesign:DialogHost.DialogContent>

        <StackPanel HorizontalAlignment="Center" VerticalAlignment="Center">
            <Button Name="Show" Content="Show Dialog" Margin="0,0,0,10"/>
        </StackPanel>
    </materialDesign:DialogHost>
```

*My PS1 Code :*

```powershell
[System.Reflection.Assembly]::LoadWithPartialName('presentationframework') |Out-Null
[System.Reflection.Assembly]::LoadFrom("assembly\System.Windows.Interactivity.dll") | Out-Null
[System.Reflection.Assembly]::LoadFrom("assembly\MaterialDesignThemes.Wpf.dll") | Out-Null
[System.Reflection.Assembly]::LoadFrom("assembly\MaterialDesignColors.dll") | Out-Null
[System.Reflection.Assembly]::LoadWithPartialName("System.Windows.Forms") | Out-Null

function LoadXml ($global:filename)
{
    $XamlLoader=(New-Object System.Xml.XmlDocument)
    $XamlLoader.Load($filename)
    return $XamlLoader
}

$XamlMainWindow=LoadXml("MonInterface.xaml")
$Reader=(New-Object System.Xml.XmlNodeReader $XamlMainWindow)
$Form=[Windows.Markup.XamlReader]::Load($Reader)
$Show = $Form.findname("Show")
$Dialog = $Form.findname("Dialog1")
$Name =  $Form.findname("Name")
$Password= $Form.findname("PasswordBox")
$Close = $Form.findname("Close_ME")

$Show.add_Click({

$Dialog.IsOpen = $True


})

$Close.add_Click({

    #find value from Controls

    $MSG1 = $Name.Text  
    $MSG2 = $Password.password.tostring()

        [System.Windows.Forms.MessageBox]::Show("You name is $MSG1 with the Password $MSG2")


    $Dialog.IsOpen = $False
})

$Form.ShowDialog() | Out-Null
``` 

![Password_demo](/img/Dialog_Password.gif)

# 4 Dialogs whit ItemList

![Demo](/img/Dialog_OSList.gif)

# 5 For the Fun you can also In Dialog launch a web page 

![Fun](/img/Dialog_WebBrowser.gif)

My project is available on Github [here](https://github.com/JM2K69/Material_Design_Dialog)

Thanks for reading.

Written by Jérôme Bezet-Torres @JM2K69.
