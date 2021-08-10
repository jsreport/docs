## Basics
`jsreport.Local` brings jsreport reporting power directly into c# without any other dependency or external server. It wraps the compiled [jsreport.exe](/learn/single-file-executable) binary with c# API on the top of it. This gives the same experience as having the access to the external full jsreport server instance but in very convenient way.

> Note that although it can be very convenient to run jsreport right from the .NET the way described in this arcticle, you should always consider pros and cons compared to running jsreport separately. This follows the better architectural concept and in case your deployment already includes multiple services or even [AKS cluster](https://azure.microsoft.com/services/kubernetes) then it is usually better to run jsreport as standalone service. An example of running asp.net core app in one container and jsreport in another container can be found [here](https://github.com/jsreport/jsreport-dotnet-example-docker-compose).

## Get started

The `jsreport.Local` and also `jsreport.Binary` nuget packages needs to be installed the first. The second mentioned includes the default compiled [jsreport.exe](/learn/single-file-executable) binary for windows and the first provides API on the top of it.

The next example shows how you can convert html to pdf using jsreport local instance.

```csharp
var rs = new LocalReporting().UseBinary(JsReportBinary.GetBinary()).AsUtility().Create();

var report = await rs.RenderAsync(new RenderRequest()
{
	Template = new Template()
	{
		Recipe = Recipe.ChromePdf,
		Engine = Engine.None,
		Content = "Hello from pdf"
	}
});
```

The same way you can [convert html to xlsx](/learn/html-to-xlsx), use [javascript templating engines](/learn/templating-engines) to dynamically assemble html, include custom [javascript function based hooks](/learn/scripts) and all the other beauty of jsreport. The c# API for such use cases is the same as for [jsreport.Client](/learn/dotnet-client), please check it out for deeper rendering API description.

The assembly is compiled for [.NET standard 2.0](https://docs.microsoft.com/en-us/dotnet/standard/net-standard) and you can use it in full .net as well as in [.NET core](https://www.microsoft.com/net/core/platform) applications.

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

In case you want to use the jsreport config file, you need to create directory "jsreport" in the project root. Then create `jsreport.config.json` inside and change its properties to "Copy" to the output directory.

## Temp files
The sdk during the first render extracts [jsreport.exe binary](learn/single-file-executable) into the user's temp directory. The jsreport.exe afterwards extracts also some files to the same location. This can cause some problems when the user running the app has temp somewhere where doesn't have write access. This is happening when using IIS. In this case you can explicitely change the temp directory to your desired location.

```csharp
new LocalReporting().TempDirectory("my temp path")
```

Note it can sometimes help to clean up the jsreport temp files when it gets corrupted. You can find it in `[userstemp]/jsreport`.

## jsreport background process
The sdk by default uses background jsreport processes to improve the rendering performance. The background process occupies the default 5488 port. In case you have multiple apps using jsreport on the same machine, you need to make sure the jsreport ports differ.

```csharp
new LocalReporting().Configure(cfg => {
  cfg.HttpPort = 3000;
  return cfg;
})
```

You may also want to disable this optimization when having issues with permissions. It can be done this way.

```csharp
new LocalReporting().UseBinary().AsUtility().KeepAlive(false)
```

## Razor templates

The `jsreport.Local` can be also used together with asp.net razor templates to render pdf or excel. This can be easily achieved using the [jsreport.AspNetCore](/learn/dotnet-aspnetcore) package which includes proper helpers and filters. The examples and description for asp.net helpers can be found in the separate [jsreport.MVC section](/learn/dotnet-aspnetcore).

## Custom npm helpers

To be able to use custom npm helpers make sure the configuration allows using custom modules through `AllowLocalFilesAccess`.

```csharp
var rs = new LocalReporting()
	.UseBinary(JsReportBinary.GetBinary())	
	.Configure(cfg => cfg.AllowLocalFilesAccess())
	.AsUtility()
	.Create();
```

Then install npm modules to the application folder and include the folder to the build in the `.csproj` file. The jsreport will search in both `node_modules` and `jsreport` folder so choose which fits you more.

```xml
<ItemGroup>
    <None Include="node_modules\**\*.*">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
</ItemGroup>
```

Now you can use custom modules in your helpers:

```js
function now () {
  return require('moment')().format('MMMM Do YYYY, h:mm:ss a')
}
```

Note [handlebars-intl](https://github.com/yahoo/handlebars-intl) package currently doesn't work because it requires some native Intl features to be compiled with the node.

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
See an example of running jsreport in separate project [here](https://github.com/jsreport/jsreport-dotnet-example-multiple-projects).

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
npm install @jsreport/jsreport-wkhtmltopdf
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

`jsreport.Local` implements two strategies for communicating with the `jsreport.exe` binary. One is to use the command line arguments and the second to start the binary as web server and communicate with it using http protocol. 

All the previous examples were using the command line based communication with `jsreport.exe`. The next example shows how to use web server based communication instead.

```csharp
var rs = new LocalReporting().UseBinary(JsReportBinary.GetBinary()).AsWebServer().Create();
await rs.StartAsync();

var report = await rs.RenderAsync(...);

await rs.KillAsync();
```          

## Windows Linux  Mac
 The `jsreport.Binary` works only on the Windows platform. However you can install extra binary package [jsreport.Binary.Linux](https://github.com/jsreport/jsreport-dotnet-binary-linux) or [jsreport.Binary.OSX](https://github.com/jsreport/jsreport-dotnet-binary-osx) if you want to support other platforms.

 ```csharp
 var rs = new LocalReporting()                
    .UseBinary(RuntimeInformation.IsOSPlatform(OSPlatform.Windows)  ?
        jsreport.Binary.JsReportBinary.GetBinary() :
        jsreport.Binary.Linux.JsReportBinary.GetBinary())                
    .AsUtility()
    .Create();
```

The list of nugets including jsreport binary can be found [here](https://github.com/jsreport/jsreport-dotnet).

## Docker
Running the `jsreport.Local` in [Docker](https://www.docker.com/) linux container requires this adaptation of `Dockerfile`. And of course using the correct binary as mentioned in the previous chapter.

```
# install chrome with deps, see https://github.com/jsreport/jsreport/blob/master/docker/full/Dockerfile
RUN apt-get update && apt-get install -y --no-install-recommends libgconf-2-4 gnupg git curl wget ca-certificates libgconf-2-4 && \
    wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - && \
    sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list' && \
    apt-get update && \  
    apt-get install -y lsb-release google-chrome-stable fonts-ipafont-gothic fonts-wqy-zenhei fonts-thai-tlwg fonts-kacst libxtst6 libxss1 --no-install-recommends

# debian needs this additional install
RUN apt-get install -y libx11-6 libx11-xcb1

ENV chrome_launchOptions_executablePath google-chrome-stable
ENV chrome_launchOptions_args --no-sandbox,--disable-dev-shm-usage,--single-process,--no-zygote
```

Make sure you add this to the right position of `Dockerfile`. Visual Studio usually generates docker file with multiple sections and this should be part of the `Base` image at the top.

See the working example with jsreport, docker, asp.net core and azure web app [here](https://github.com/jsreport/jsreport-dotnet-example-docker).

## Azure Web Apps
jsreport uses [headless chrome](https://github.com/GoogleChrome/puppeteer) to print pdf. Unfortunately Azure Web Apps running on windows are very restrictive and doesn't allow running  [headless chrome](https://github.com/GoogleChrome/puppeteer) process. In the other words `jsreport.Local` won't be able to print pdf in Azure Web Apps running on windows.

Fortunately, Azure Web Apps running in docker with Linux host are using different sandboxing strategy and [headless chrome](https://github.com/GoogleChrome/puppeteer) works there. If this is an option for you, enable Linux docker support in your Azure Web App and add to your `Dockerfile` lines from the [docker chapter](/learn/dotnet-local#docker). Additionally you need to explicitly specify jsreport internal port, because of collision in the environment variables.

```csharp
new LocalReporting()
  .UseBinary(JsReportBinary.GetBinary())           
  .Configure((cfg) => {
    cfg.HttpPort = 1000;
    return cfg;
  })
  .AsUtility()
  .Create());
```

Note that problematic run of `jsreport.Local` in a restricted environment like windows based Azure Web Apps has nothing to do with jsreport remote client. You can always run full jsreport externally in another VM, docker container or even external service like [jsreportonline](/online) and connect to it from Azure Web App using `jsreport.Client`. This is anyway usually better design in the era of micro-services.

See the working example with jsreport, docker, asp.net core and azure web app [here](https://github.com/jsreport/jsreport-dotnet-example-docker).

## License key

In case you purchased enterprise jsreport license you can apply it using configuration file `jsreport.config.json` and property "licenseKey". Or you can also pass the license key directly from c# using the following code.

```csharp
var rs = new LocalReporting()
	.UseBinary(JsReportBinary.GetBinary())
	.Configure(cfg => {
	  cfg.LicenseKey = "xxx-xxx-xxx-xxx";
	  return cfg;
	}))
	.AsUtility()
	.Create();
```