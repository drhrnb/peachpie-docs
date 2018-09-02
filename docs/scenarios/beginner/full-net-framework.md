# Target .NET Framework

*(not .NET Core)*

Projects built with PeachPie depends on PeachPie runtime which targets **.NET Standard 2.0**. This makes it compatible with vast array of other .NET frameworks and runtimes, until they are compatible with .NET Standard 2.0. This includes .NET Core 2.0, Mono 5.4, Xamarin.Android 8.0, or .NET Framework 4.6.1 or higher.

## Building the project

### Build a library

When compiling a library project to be referenced by other projects, it is always the best option to target the lowest .NET Standard possible so it can be referenced by majority of .NET projects. In this case, it is `netstandard2.0`. Ensure you have following property set in [the project file](/php/msbuild):

```xml
<TargetFramework>netstandard2.0</TargetFramework>
```

### Build an executable

In order to build an executable application for .NET Framework (not .NET Core), target `net461` or higher and specify the output type `exe` in [the project file](/php/msbuild):

```xml
<OutputType>exe</OutputType>
<TargetFramework>net461</TargetFramework>
```

Optionally specify [the startup object](/php/msbuild#startupobject) which should be called upon starting the application.

## ASP.NET application

Assuming the project with PHP files is compiled as a library, targeting either `netstandard2.0` or `net461`.

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AssemblyName>MyWebsite</AssemblyName><!-- an optional name -->
```

Handle the requests to `*.php` pages on your ASP.NET (Integrated Pipeline) server (specifically IIS 7, IIS Express or Apache with mod_mono) in two ways. Either by modifying `web.config` and passing requests directly to `MyWebsite.dll`, or by handling the requests programically in your C# code.

### Option 1. Web.Config

Alter the web application's `web.config` file with the following configuration:

```xml
<system.webServer>
    <handlers>
      <add name="PeachpieHandler" path="*.php" verb="*" type="Peachpie.RequestHandler.RequestHandler, Peachpie.RequestHandler, Version=0.9.0.0, Culture=neutral, PublicKeyToken=5b4bee2bf1f98593" resourceType="Unspecified" preCondition="integratedMode" />
    </handlers>
<system.webServer>
```

Ensure the `/bin` folder of your ASP.NET application contains all the necessary assemblies including `MyWebsite.dll`, `Peachpie.RequestHandler.dll` and other Peachpie runtime assemblies.

### Option 2. Programically

In order to handle requests to compiled `*.php` pages, make use of `RequestHandler` API. Obtain the instance of `System.Web.HttpContext` and pass it to `RequestHandler.ProcessRequest(HttpContext)` method.

```c#
using Peachpie.RequestHandler;
new RequestHandler().ProcessRequest( httpContext );
```

The code above lazily loads PHP library assemblies (e.g. `MyWebsite.dll`) and lookups for compiled script corresponding to given `httpContext`. If it is found, it gets included and processed.

## Related links

- [Target frameworks](https://docs.microsoft.com/en-us/dotnet/standard/frameworks) *(docs.microsoft.com)*
- [.NET Standard Versions Interactive Table](https://immo.landwerth.net/netstandard-versions/#) *(immo.landwerth.net)*