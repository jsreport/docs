## Basics
`jsreport.Local` brings jsreport reporting power directly into c# without any other dependency or external server. It wraps the compiled [jsreport.exe](/learn/single-file-executable) binary with c# API on the top of it. This gives the same experience as having the access to the external full jsreport server instance but in very convenient way.

The assembly is compiled for [.NET standard 2.0](https://docs.microsoft.com/en-us/dotnet/standard/net-standard) and you can use it in full .net as well as in [.NET core 2.0](https://www.microsoft.com/net/core/platform) applications.


## Get started

The `jsreport.Local` and also `jsreport.Binary` nuget packages needs to be installed the first. The second mentioned includes the default compiled [jsreport.exe](/learn/single-file-executable) binary for windows and the first provides API on the top of it. 

The next example shows how you can convert html to pdf using jsreport local instance.

```csharp
var rs = new LocalReporting().UseBinary(JsReportBinary.GetBinary()).AsUtility().Create();

var report = await rs.RenderAsync(new RenderRequest()
{
	Template = new Template()
	{
		Recipe = Recipe.PhantomPdf,
		Engine = Engine.None,
		Content = "Hello from pdf"
	}
});
```

The same way you can [convert html to xlsx](/learn/html-to-xlsx), use [javascript templating engines](/learn/templating-engines) to dynamically assemble html, include custom [javascript function based hooks](/learn/scripts) and all the other beauty of jsreport. The c# API for such use cases is the same as for [jsreport.Client](/learn/dotnet-client), please check it out for deeper rendering API description.

## Configuration
jsreport core and the most of the extensions provides tons of [configuration options](/learn/configuration). These options can be passed to the local jsreport using the config file, but also right from the c#.

You can for example allow access to the local files or specify the root path for the report rendering so you can relatively link your styles and scripts.

```csharp
var rs = new LocalReporting()
	.UseBinary(JsReportBinary.GetBinary())
	.Configure(cfg =>cfg.AllowLocalFilesAccess().BaseUrlAsWorkingDirectory())
	.AsUtility()
	.Create();
```

## Razor templates

The `jsreport.Local` can be also used together with asp.net razor templates to render pdf or excel. This can be easily achieved using the [jsreport.AspNetCore](/learn/dotnet-aspnetcore) package which includes proper helpers and filters. The examples and description for asp.net helpers can be found in the separate [jsreport.MVC section](/learn/dotnet-aspnetcore).

## Locally stored templates
The `jsreport.Local` includes also web based studio for designing reports. There is no limitation in comparison with the [full jsreport installation](/on-prem) and you can do the whole report designing and rendering workflow also with `jsreport.Local`.

The next sections explains how you can run the jsreport studio from VS and use it to design report templates. Then how you can mark the created template files to be part of the VS project and build. And in the end how you can render these templates in production.

### Running studio
The first step is create a c# console project and install `jsreport.Local` and `jsreport.Binary` nugets.  The following snippet should be then pasted to the `Main` function. It looks a bit complex but I wanted to be more explicit what configs are being used to start the jsreport server.

```csharp
var rs = new LocalReporting()
     .UseBinary(JsReportBinary.GetBinary())
     .KillRunningJsReportProcesses()
     .RunInDirectory(Path.Combine(Directory.GetCurrentDirectory(), "jsreport"))
     .Configure(cfg => cfg.CreateSamples()
		.FileSystemStore()
		.BaseUrlAsWorkingDirectory())
     .AsWebServer()
     .RedirectOutputToConsole()
     .Create();

rs.StartAsync().Wait();

Process.Start(new ProcessStartInfo("cmd", $"/c start http://localhost:5488"));            

Console.ReadKey();

rs.KillAsync().Wait();
```

The console application now starts jsreport server and opens the studio in the browser on `http://localhost:5488`. This gives the same experience as using the full jsreport installation. 

> HINT - you can also edit report templates directly in the Visual Studio, because the jsreport studio automatically monitor source changes and instantly triggers the preview with the changes.

### Including templates in build

The templates created in the jsreport studio are now part of the VS 2017 project, however not automatically included in the bin folder. The Visual Studio currently doesn't include context menu to automatically copy the whole folder to the output, but fortunately you can edit the `csproj` manually and add this by hand.
```xml
<ItemGroup>
    <None Include="jsreport\**\*.*">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
</ItemGroup>
```

The `jsreport` folder with templates should be now automatically present in the `bin` after the build.

### Rendering stored templates

The templates are now ready and propagated to the output bin. This means templates can be also referenced in the rendering request from any project referencing the original console app. This looks weird to reference console project, but there is now harm in doing it.

```csharp
var rs = new LocalReporting()
	.UseBinary(JsReportBinary.GetBinary())
	.Configure(cfg => cfg.FileSystemStore().BaseUrlAsWorkingDirectory())
    .AsUtility()
    .Create();              
    
var report = await rs.RenderByNameAsync("Invoice", new
{
	Id = 123,
	From = "Erich Gamma",
	To = "Martin Fowler"
});
```

## Custom extensions

The [jsreport binary](https://jsreport.net/learn/single-file-executable) used in `jsreport.Local` includes only default [extensions](/learn/extensions), [engines](/learn/templating-engines) and [recipes](/learn/recipes). However there  are many additional extensions used with jsreport which you can also install to the c# project and use with `jsreport.Local`.

The first you need to make sure you have `jsreport` folder created in the project and everything in it is being marked to be replicated into the bin folder as mentioned in the previous chapter. Then create inside file `jsreport.config.json` with the following content:

```js
{ 
  "discover": true
}
```

This instructs jsreport to crawl this directory and search for the additional custom extension. The last step is to install a custom extension using [npm](https://www.npmjs.com/) into the `jsreport` directory.

To demonstrate this open cmd with the `jsreport` working directory and type:

```sh
npm install jsreport-wkhtmltopdf
```

Now you can use [wkhtmltopdf](/learn/wkhtmltopdf) recipe:

```csharp
rs.RenderAsync(new RenderRequest()
{
    Template = new Template()
    {
        Content = "Hello",
        Engine = Engine.None,
        Recipe = Recipe.Wkhtmltopdf
    }
}
```

## Utility or Web Server

`jsreport.Local` implements two strategies for communicating with the `jsreport.exe` binary. One is to use the command line arguments and the second to start the binary as web server and communicate with it using http protocol. The second strategy is currently significantly faster however this should change in the future releases and the performance should be close to each other. It is generally recommended to use the utility based strategy in production.

All the previous examples were using the command line based communication with `jsreport.exe`. The next example shows how to use web server based communication instead.

```csharp
var rs = new LocalReporting().UseBinary(JsReportBinary.GetBinary()).AsWebServer().Create();
await rs.StartAsync();

var report = await rs.RenderAsync(...);

await rs.KillAsync();
```            