---
layout: post
title: "Material Design theme Manager -First Post"
date: 2019-07-25
tags: [PowerShell, Material, Design, XAML, WPF, Themes, Manager ]
published : true
---

![Installed Part](/img/Manager.PNG) 


With the latest version 2.6 of **MaterialDesignInXamlToolkit** I decided to make a series of posts on MaterialDesignXamlToolkit. In this first post we will look at how to change the themes in our #WPF application with `PowerShell`. Thank to **Kevin Bost** `@kitokeboo`.

# 1.How to set Theme and Color in your XAML  

Here all the Primary Color and Secondary Color available.

![Installed Part](/img/palette.PNG) 


## 1.1 Before 2.6 version

We need three lines to define Theme one for the Theme `Dark` or `Light` an other one for the `Primary` color and one for the `Secondary` color.

```XML
<Window.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Dark.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Defaults.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/Recommended/Primary/MaterialDesignColor.Purple.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/Recommended/Accent/MaterialDesignColor.blue.xaml" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Window.Resources>
```

## 1.2 In 2.6 release

Only one line is necessary :

* **BaseTheme** = `Light` or `Dark`
* **PrimaryColor** = `Red`, `Pink`,`Purple`, `DeepPurple` ...
* **SecondaryColor** = `Red`, `Pink`,`Purple`, `DeepPurple` but not all Primary.

```XML
<Window.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
            <!-- work Only In 2.6 Material Design /-->
             <materialDesign:BundledTheme BaseTheme="Light" PrimaryColor="DeepPurple" SecondaryColor="Lime" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Defaults.xaml" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Window.Resources>
```

With this two method you can only change the Theme before your application is launch the goal it's to change all setting on the way ! !
How to make it `Dynamically`I need to look into the **MaterialDesignColors.dll** and **MaterialDesignThemes.Wpf.dll** with the great tool dnspy available on [Github](https://github.com/0xd4d/dnSpy/releases).
Now let's see how to do this.

# 2 Theme and Colors
Before to start, here is a quick reminder about MAterial Design Theme and Colors.
The theme is the main design of the GUI. It will work like Windows 10 with two themes:
- BaseDark: Dark theme, meaning black
- BaseLight: Light theme, meaning white
 
The Primary and Secondary Colors will be used to change all colors of you Controls in one click.

# 3. How to make it ?

To change the theme we will use four class `[MaterialDesignThemes.Wpf.ResourceDictionaryExtensions]`, `[MaterialDesignThemes.Wpf.ThemeExtensions]`,  `[MaterialDesignThemes.Wpf.ThemeExtensions]` and `[MaterialDesignColors.SwatchHelper]`.
With their own method : 
- `[MaterialDesignThemes.Wpf.ResourceDictionaryExtensions]::GetTheme` : To detect the current theme or colors
- `[MaterialDesignThemes.Wpf.ThemeExtensions]::SetBaseTheme` : To change the current theme `Dark` or `Light`
- `[MaterialDesignColors.SwatchHelper]::Lookup` : Use to find the colors definition object's name :  **Swatch**
- `[MaterialDesignThemes.Wpf.ThemeExtensions]::SetPrimaryColor` :or SetSecondaryColor to change the   


## 3.1 Theme Dynamically

As mentioned above I need to use **[MaterialDesignThemes.Wpf.ResourceDictionaryExtensions]::GetTheme** method like this In this example `$form` is my main Windows Application.

### 3.1.1 Find the Theme

```powershell
    $theme = [MaterialDesignThemes.Wpf.ResourceDictionaryExtensions]::GetTheme($form.Resources)

```

_**Becarefull**_ remember this point by default the theme is define in your `XAML` in the a **<Window.Resources>** that's why I used `$Form.Resources`

### 3.1.2 Change the theme


```powershell
      [MaterialDesignThemes.Wpf.ThemeExtensions]::SetBaseTheme($theme, [MaterialDesignThemes.Wpf.Theme]::Dark)

```
In my application i use a `ToogleButton` to chaneg the Theme and the code is :

```powershell
$Toggle.add_Click( {
    
    # Find the current Theme
    $theme = [MaterialDesignThemes.Wpf.ResourceDictionaryExtensions]::GetTheme($form.Resources)
   
    if ($Toggle.IsChecked -eq $true) {
      # Apply Dark Theme  
      [MaterialDesignThemes.Wpf.ThemeExtensions]::SetBaseTheme($theme, [MaterialDesignThemes.Wpf.Theme]::Dark)
    }
    if ($Toggle.IsChecked -eq $False) {
      # Apply Light Theme
      [MaterialDesignThemes.Wpf.ThemeExtensions]::SetBaseTheme($theme, [MaterialDesignThemes.Wpf.Theme]::Light)
    }
    [MaterialDesignThemes.Wpf.ResourceDictionaryExtensions]::SetTheme($form.Resources, $theme)
  })

```
## 3.2 Change Primary and Secondary Colors Dynamically

How to find all Primary and Secondary colors ? whit this powershell code :

**PrimaryColors**

```powershell
PS C:\MaterialDesign\ThemeManager>  [System.Enum]::GetNames([MaterialDesignColors.PrimaryColor])
Red
Pink
Purple
DeepPurple
Indigo
Blue
LightBlue
Cyan
Teal
Green
LightGreen
Lime
Yellow
Amber
Orange
DeepOrange
Brown
Grey
BlueGrey
```

**SecondaryColors**

```powershell
PS C:\MaterialDesign\ThemeManager>  [System.Enum]::GetNames([MaterialDesignColors.SecondaryColor])
Red
Pink
Purple
DeepPurple
Indigo
Blue
LightBlue
Cyan
Teal
Green
LightGreen
Lime
Yellow
Amber
Orange
DeepOrange
```

In my Application I have two **ComboBox Control** one for PrimaryColor and one for **SecondaryColor** there is the same method to change the Colors Dynamically. On my ComboBox I use the event `SelectionChanged`

```powershell
$CSecondarys.Add_SelectionChanged({
    
    # Find the current Theme
    $theme = [MaterialDesignThemes.Wpf.ResourceDictionaryExtensions]::GetTheme($form.Resources)
    
    # Obtain the Swatch Object with their name
    $SecondaryColors = [MaterialDesignColors.SwatchHelper]::Lookup[$CSecondarys.SelectedValue]
    
    # Set the SecondaryColor

    [MaterialDesignThemes.Wpf.ThemeExtensions]::SetSecondaryColor($theme, $SecondaryColors)
    
    # Apply the Full Theme
    [MaterialDesignThemes.Wpf.ResourceDictionaryExtensions]::SetTheme($form.Resources, $theme)
    
})
```
For the PrimaryColor the method is the same you must change `[MaterialDesignThemes.Wpf.ThemeExtensions]::SetSecondaryColor($theme, $SecondaryColors)` to `[MaterialDesignThemes.Wpf.ThemeExtensions]::SetPrimaryColor($theme, $PrimaryColors)`

# 4 Demo time

![alt text](https://github.com/JM2K69/JM2K69.github.io/blob/master/img/Gif.gif) 

My project is available on Github [here](https://github.com/JM2K69/Material-Design-Theme-Manager-)

Thanks for reading.

Written by Jérôme Bezet-Torres @JM2K69.
