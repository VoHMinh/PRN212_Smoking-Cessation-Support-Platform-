#### Contents
[Styling the window](#styling-the-window)

[Set the theme and accent color of the app](#set-the-theme-and-accent-color-of-the-app)

[Override predefined colors](#override-predefined-colors)

[Override the theme at element level](#override-the-theme-at-element-level)

[Enable IntelliSense for XAML resources](#enable-intellisense-for-xaml-resources)

## Styling the window
The window style provided by this library offers the following features:
1. Make the title bar adapt to the current theme.
2. Provide a consistent window chrome across different Windows versions.
3. Provide a back button on the top left corner.
4. Enable title bar customization.

Three are two ways to apply the style:
1. For simple scenarios, just set `ui:WindowHelper.UseModernWindowStyle="True"` on the window.
2. If you have your own window style, you can base it on `DefaultWindowStyle`:
```xaml
<Style x:Key="MyWindowStyle"
       TargetType="Window"
       BasedOn="{StaticResource DefaultWindowStyle}">
    ...
</Style>
```
If you don't need the features mentioned above, using the style is not mandatory. Setting the following properties on the window should suffice:
```xaml
Background="{DynamicResource SystemControlPageBackgroundAltHighBrush}"
Foreground="{DynamicResource SystemControlPageTextBaseHighBrush}"
ui:ThemeManager.IsThemeAware="True"
```

## Set the theme and accent color of the app
The default theme and accent color are as follows:
* For systems running Windows 10, the system accent color is used. If the Windows version supports the "choose your default app mode" setting, this setting is used; otherwise, the light theme is used.
* For systems running an earlier version of Windows, the light theme and the color #0078D7 (default blue) are used.

To override the default settings, set properties on the `ThemeResources` class in App.xaml. For example:
```xaml
<ui:ThemeResources RequestedTheme="Dark" AccentColor="Red" />
```
Values set this way are applied at both design time and runtime. If you'd like to change them at runtime, it's usually more convenient to use the `ThemeManager` class, which supports data binding. For example:
```cs
private void Button_Click(object sender, RoutedEventArgs e)
{
    ThemeManager.Current.ApplicationTheme = ApplicationTheme.Dark;
    ThemeManager.Current.AccentColor = Colors.Red;
}
```

## Override predefined colors
The easiest way is to use the `ColorPaletteResources` class. For example, edit the `ui:ThemeResources` element in App.xaml to following:
```xaml
<ui:ThemeResources>
    <ui:ThemeResources.ThemeDictionaries>
        <ResourceDictionary x:Key="Light">
            <ResourceDictionary.MergedDictionaries>
                <ui:ColorPaletteResources
                    TargetTheme="Light"
                    Accent="Green"
                    AltHigh="LightGray" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
        <ResourceDictionary x:Key="Dark">
            <ResourceDictionary.MergedDictionaries>
                <ui:ColorPaletteResources
                    TargetTheme="Dark"
                    Accent="Red"
                    AltHigh="DarkGray" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ui:ThemeResources.ThemeDictionaries>
</ui:ThemeResources>
```
Now in the light theme, `Green` is used as the accent color and `LightGray` is used as the `AltHigh` color. Colors for the dark theme are set in the same way.

## Override the theme at element level
Set the `ThemeManager.RequestedTheme` attached property on any `FrameworkElement`. For example:
```xaml
<Border
    Background="{DynamicResource SystemControlBackgroundAltHighBrush}"
    Padding="12"
    ui:ThemeManager.RequestedTheme="Dark">
    <Button Content="Dark theme button" />
</Border>
```
Now this `Border` and its descendants will use the dark theme while the theme used by the rest of the app remains unchanged. Note: This feature sometimes doesn't work correctly at design time.

## Enable IntelliSense for XAML resources
Add a [DesignTimeResources.xaml](https://antonymale.co.uk/design-time-resources-in-wpf.html) file to the project, and edit it to following:
```xaml
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:ui="http://schemas.modernwpf.com/2019">
    <ResourceDictionary.MergedDictionaries>
        <ui:IntellisenseResources Source="/ModernWpf;component/DesignTime/DesignTimeResources.xaml" />
    </ResourceDictionary.MergedDictionaries>
</ResourceDictionary>
```