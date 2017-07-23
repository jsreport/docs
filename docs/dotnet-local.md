##Basics
`jsreport.Local` brings jsreport reporting power into c# without any other dependency or external server. It includes the compiled [jsreport.exe](/learn/single-file-executable) binary and c# API on the top of it. This gives the same experience as having the access to the external full jsreport server instance.

The assembly is compiled for .net standard 2.0 and you can use it in full .net as well as in .net core 2.0 applications.


## Get started

The `jsreport.Local` and also `jsreport.Binary` nuget packages needs to be installed the first. The second mentioned includes the default compiled [jsreport.exe](/learn/single-file-executable) and the first provides API on the top of it. 

The next example shows how you can for example convert html to pdf using jsreport local instance.

```csharp
var rs = new LocalReporting().UseBinary(JsReportBinary.GetStream()).AsUtility().Create();

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

The same way you can [convert html to xlsx](/learn/html-to-xlsx), use [javascript templating engines](/learn/engines) to dynamically assemble html, include custom [javascript function based hooks](/learn/scripts) and all the other beauty of jsreport. The c# API for such use cases is the same as for `jsreport.Client`, please check it out for deeper rendering API description.

## Configuration
jsreport core and the most of the extensions provides tons of [configuration options](/learn/configuration). These options can be passed to the local jsreport using the config file, but also right from the c#.

You can for example allow access to the local files or specify the root path for the report rendering so you can relatively link your styles and scripts.

```csharp
var rs = new LocalReporting().UseBinary(JsReportBinary.GetStream()).Configure(cfg => cfg.AllowLocalFilesAccess().BaseUrl()).AsUtility().Create();
```

## Locally stored templates

The [full jsreport installation](/on-prem) by default uses web based studio to design reports and  file system to store report templates. Such stored templates can be also accessed by the `jsreport.Local`. You just need to put them to the `[project]/jsreport` folder and make sure the folder has `copy always` set. Then you can configure jsreport to use local file system store and render the template by its name.

```csharp
var rs = new LocalReporting()
              .UseBinary(JsReportBinary.GetStream())
              .Configure(cfg => cfg.FileSystemStore())
              .AsUtility()
              .Create();
              
var report = await rs.RenderByNameAsync("Invoice", new
{
	Id = 123,
	From = "Erich Gamma",
	To = "Martin Fowler"
});
```

This way you can use jsreport studio to design reports which are stored and also later published with the VS project. To make this workflow even easier you can install [jsreport visual studio extension](https://marketplace.visualstudio.com/items?itemName=JanpofiderBlaha.jsreportvisualstudiotools) which adds to the project context menu two handful buttons.

`Add jsreport` - this adds the `jsreport` folder to the project together with the sample templates, default config and `jsreport.exe` binary

`Run jsreport development studio` - this runs jsreport development studio on `http://localhost:5488` and open the default browser with it.

## Utility or Web Server

`jsreport.Local` implements two strategies for communicating with the `jsreport.exe` binary. One is to use the command line arguments and the second to start the binary as web server and communicate with it using http protocol. The second strategy is currently significantly faster however this should change in the future releases and the performance should be close to each other. 

All the previous examples were using the command line based communication with `jsreport.exe`. The next example shows how to use web server based communication instead.

```csharp
var rs = new LocalReporting().UseBinary(JsReportBinary.GetStream()).AsWebServer().Create();
await rs.StartAsync();

await rs.KillAsync();
```            