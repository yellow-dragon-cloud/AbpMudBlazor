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

To add downloaded projects to source control, open `.gitignore` and add this line:

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

