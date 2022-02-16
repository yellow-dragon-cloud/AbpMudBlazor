# MudBlazor Theme in Abp Blazor WebAssembly

## Introduction
It is quite easy to customize default template and layout of an [ABP](https://abp.io/) Blazor application. This sample demonstrates how you can use MudBlazor layouts in your ABP Blazor WebAssembly applications. The source code is [available on GitHub](https://github.com/yellow-dragon-cloud/AbpMudBlazor/edit/main/README.md)


## What is MudBlazor?
[MudBlazor](https://www.mudblazor.com/) is a Blazor component library trusted by thousands of users, from hobbyists to enterprises.


## 1. Create a new ABP Blazor WebAssembly application

Install or update the ABP CLI:

```bash
dotnet tool install -g Volo.Abp.Cli || dotnet tool update -g Volo.Abp.Cli
```

Create a new ABP Blazor WebAssembly application:

```bash
abp new Acme.BookStore -u blazor -d mongodb
```


## 2. Add packages to your solution

Copy the source code of [Basic Theme](https://docs.abp.io/en/abp/latest/UI/Blazor/Basic-Theme) to your solution:

```bash
abp add-package Volo.Abp.AspNetCore.Components.WebAssembly.BasicTheme --with-source-code --add-to-solution-file
```

Then, navigate to downloaded `Volo.Abp.AspNetCore.Components.WebAssembly.BasicTheme` project directory and run:

```bash
abp add-package Volo.Abp.AspNetCore.Components.Web.BasicTheme --with-source-code --add-to-solution-file
```

To add downloaded projects to source control, open `.gitignore` file and add this line:

```gitignore
!**/packages/*Theme*
```


## 3. Add MudBlazor

Navigate to downloaded `Volo.Abp.AspNetCore.Components.Web.BasicTheme` project directory and install MudBlazor:

```bash
dotnet add package MudBlazor
```

In `Volo.Abp.AspNetCore.Components.Web.BasicTheme` project, open `AbpAspNetCoreComponentsWebBasicThemeModule.cs` file, and replace its content with the following code:

```csharp
using MudBlazor.Services;
using Volo.Abp.AspNetCore.Components.Web.Theming;
using Volo.Abp.Modularity;

namespace Volo.Abp.AspNetCore.Components.Web.BasicTheme;

[DependsOn(
    typeof(AbpAspNetCoreComponentsWebThemingModule)
)]
public class AbpAspNetCoreComponentsWebBasicThemeModule : AbpModule
{
    public override void ConfigureServices(ServiceConfigurationContext context)
    {
        base.ConfigureServices(context);
        context.Services.AddMudServices();
    }
}
```

And in `Volo.Abp.AspNetCore.Components.WebAssembly.BasicTheme` project, open `BasicThemeBundleContributor.cs` file, and replace its content with the following code:

```csharp
using Volo.Abp.Bundling;

namespace Volo.Abp.AspNetCore.Components.WebAssembly.BasicTheme;

public class BasicThemeBundleContributor : IBundleContributor
{
    public void AddScripts(BundleContext context)
    {
        context.Add("_content/MudBlazor/MudBlazor.min.js");
    }

    public void AddStyles(BundleContext context)
    {
        context.Add("_content/Volo.Abp.AspNetCore.Components.Web.BasicTheme/libs/abp/css/theme.css");

        context.Add("https://fonts.googleapis.com/css?family=Roboto:300,400,500,700&display=swap");
        context.Add("_content/MudBlazor/MudBlazor.min.css");
    }
}
```

Add the following to your HTML head section of `index.html` file in `Acme.BookStore.Blazor` project:

```html
<link href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700&display=swap" rel="stylesheet" />
<link href="_content/MudBlazor/MudBlazor.min.css" rel="stylesheet" />
```

In the same file but located in the end of it add the MudBlazor js file, it should be in the same location as the default blazor script:

```html
<script src="_content/MudBlazor/MudBlazor.min.js"></script>
```

Add this line to all `_Imports.razor` files:

```razor
@using MudBlazor
```


## 4. Customize Basic Theme

Open `Volo.Abp.AspNetCore.Components.Web.BasicTheme/Themes/Basic/` folder.

Replace `Branding.razor` file's content with the following code:

```razor
@using Volo.Abp.Ui.Branding
@inject IBrandingProvider BrandingProvider

<MudText Typo="Typo.h5" Class="ml-3">
    @BrandingProvider.AppName
</MudText>
```

Replace `FirstLevelNavMenuItem.razor` file's content with the following code:

```razor
@using Volo.Abp.UI.Navigation

@{
    var elementId = MenuItem.ElementId ?? "MenuItem_" + MenuItem.Name.Replace(".", "_");
    var cssClass = string.IsNullOrEmpty(MenuItem.CssClass) ? string.Empty : MenuItem.CssClass;
    var disabled = MenuItem.IsDisabled ? "disabled" : string.Empty;
    var url = MenuItem.Url == null ? "#" : MenuItem.Url.TrimStart('/', '~');
}

@if (MenuItem.IsLeaf)
{
    if (MenuItem.Url is not null)
    {
        <MudNavLink Icon="@MenuItem.Icon" Href="@url" Target="@MenuItem.Target" Match="NavLinkMatch.All">
            @MenuItem.DisplayName
        </MudNavLink>
    }
}
else
{
    <MudNavGroup Icon="@MenuItem.Icon" Title="@MenuItem.DisplayName">
        @foreach (var childMenuItem in MenuItem.Items.OrderBy(i => i.Order))
        {
            <SecondLevelNavMenuItem MenuItem="@childMenuItem"/>
        }
    </MudNavGroup>
}
```

Replace `SecondLevelNavMenuItem.razor` file's content with the following code:

```razor
@using Volo.Abp.UI.Navigation

@{
    var elementId = MenuItem.ElementId ?? "MenuItem_" + MenuItem.Name.Replace(".", "_");
    var cssClass = string.IsNullOrEmpty(MenuItem.CssClass) ? string.Empty : MenuItem.CssClass;
    var disabled = MenuItem.IsDisabled ? "disabled" : string.Empty;
    var url = MenuItem.Url == null ? "#" : MenuItem.Url.TrimStart('/', '~');
}

@if (MenuItem.IsLeaf)
{
    if (MenuItem.Url is not null)
    {
        <MudNavLink Icon="@MenuItem.Icon" Href="@url" Target="@MenuItem.Target">
            @MenuItem.DisplayName
        </MudNavLink>
    }
}
else
{
    <MudNavGroup Icon="@MenuItem.Icon" Title="@MenuItem.DisplayName">
        @foreach (var childMenuItem in MenuItem.Items.OrderBy(i => i.Order))
        {
            <SecondLevelNavMenuItem MenuItem="@childMenuItem"/>
        }
    </MudNavGroup>
}
```

Replace `NavToolbar.razor` file's content with the following code:

```razor
@foreach (var render in ToolbarItemRenders)
{
    @render
}
```

Replace `MainLayout.razor` file's content with the following code:

```razor
@inherits LayoutComponentBase

<MudThemeProvider />
<MudDialogProvider />
<MudSnackbarProvider />

<MudLayout>
    <MudAppBar Elevation="8">
        <MudIconButton Icon="@Icons.Material.Filled.Menu" Color="MudBlazor.Color.Inherit" Edge="Edge.Start" OnClick="@((e) => DrawerToggle())" />
        <Branding />
        <MudSpacer />
        <NavToolbar />
    </MudAppBar>
    <MudDrawer @bind-Open="_drawerOpen" ClipMode="DrawerClipMode.Always" Elevation="8">
        <NavMenu />
    </MudDrawer>
    <MudMainContent>
        <MudContainer MaxWidth="MaxWidth.False" Class="mt-4">
            <PageAlert />
            @Body
            <UiMessageAlert />
            <UiNotificationAlert />
            <UiPageProgress />
        </MudContainer>
    </MudMainContent>
</MudLayout>

@code 
{
    private bool _drawerOpen = true;

    private void DrawerToggle()
    {
        _drawerOpen = !_drawerOpen;
    }
}
```
