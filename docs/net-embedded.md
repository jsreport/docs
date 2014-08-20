> Install-Package [jsreport.Embedded](https://www.nuget.org/packages/jsreport.Embedded)

jsreport can be easily installed using [nuget package](https://www.nuget.org/packages/jsreport.Embedded) into c# project and run with the same lifecycle as orignal .NET process. Everything needed is inside a package and no node.js or any other dependency installation is required.

When you have nuget package installed you can just create `EmbeddedReportingServer`, start it and then you are ready to print any pdf report you want.


```c#
var embeddedServer = new EmbeddedReportingServer(port: 2000);
await embeddedServer.StartAsync();

var result = await embeddedServer.ReportingService.RenderAsync(new RenderRequest()
{
    template = new Template()
    {
        content = "<h1>Hello world</h1>",
        recipe = "phantom-pdf"
    }
});

using (var fileStream = File.Create("C:\\temp\\report.pdf"))
{
    report.Content.CopyTo(fileStream);
}
```

You can also combine it with [jsreport visual studio tools](http://jsreport.net) and design reports inside visual studio and render them in jsreport embedded. In this case you should also call `SynchronizeTemplatesAsync` to synchronize templates from visual studio project into jsreport before you start rendering. 

```c#
var embeddedServer = new EmbeddedReportingServer(port: 2000);
await embeddedServer.StartAsync();
await embeddedServer.ReportingService.SynchronizeTemplatesAsync();

var result = await embeddedServer.ReportingService.RenderAsync("Report1", new { ... });
```


##Running in asp.net
jsreport embedded will run from `bin` folder by default. This is not very good for asp.net applications where every change in bin folder forces application to restart. That's why using `RelativePathToServer` attribute you can configure where jsreport server is extracted and running. For asp.net it's recommended to use `App_Data` folder, so starting jsreport embedded in asp.net should look following:

```c#
var embeddedServer = new EmbeddedReportingServer() { RelativePathToServer = "../App_Data" };
await embeddedServer.StartAsync();
```


##How does it work

Installing `jsreport.Embedded` nuget package will add into your project zipped version of jsreport and `node.exe` executable that is able to start jsreport server. Zipped jsreport is extracted and stared using `nodejs` after `EmbeddedReportingServer.StartAsync()` is called.

Starting embedded server will also create a separate thread pinging jsreport server. Server itself will then  terminate when there are no ping requests comming. This will make sure jsreport server lifecycle is same as .NET original process. jsreport also automaticaly quits after application domain unloads.


![jsreport](http://jsreport.net/img/embedded.png)

##Html studio
When running embedded jsreport you can still access it's html based studio. Just start it and go to url http://localhost:2000 and it should pop up. This also can be very handy for testing report templates for external people not having visual studio installed.