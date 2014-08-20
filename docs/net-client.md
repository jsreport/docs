jsreport comes with .NET c# client which wraps REST API and is very convenient to use if you are in .NET environment. You can use .NET c# client to connect to a remote jsreport server and also to [jsreport .NET embedded server](http://jsreport.net/learn/net-embedded).

## Get Started

I assume that you already understand basic jsreport concepts. If you don't, please follow instruction in the [get started section](http://jsreport.net/learn/get-started).

Installing jsreport c# client is easy using nuget package:
> PM> Install-Package [jsreport.Client](https://www.nuget.org/packages/jsreport.Client/)

Main facade you will use to access jsreport from c# is called `ReportingService`. This class has different ways of creation for on-prem, online and embedded version:

>on-prem
>```c#
>var _reportingService = new ReportingService("https://192.168.02.01");
>```

>online
>```c#
>var _reportingService = new ReportingService("https://[subdomain].jsreportonline.net", "email", "password");
>```

>embedded
>```c#
>var embeddedServer = new EmbeddedReportingServer();
>await embeddedServer.StartAsync();
>var _reportingService = embeddedServer.ReportingService;
>```

When you have the `ReportingService` instance, you'll most likely want to invoke report rendering. In the most common case, you will grab `shortid` from jsreport studio, collect some data and invoke rendering.

```c#
var report = await _reportingService.RenderAsync("g1xcKBanJc", new {someData = "foo"});
new StreamReader(report.Content).ReadToEnd();
```

##Advanced rendering

If you want to have full control on template rendering you can use `RenderAsync` overload which accepts `RenderRequest` instance allowing you to fill bunch of other options. For example if you have some kind of a dynamic template, you don't need to specify template shortid and you can construct template content in c#.

```c#
var report = await _reportingService.RenderAsync(new RenderRequest() {
                template = new Template()
                {
                    recipe = "html",
                    content = "some dynamic template content"
                },
                data = new { firstName = "Jan", surname = "Blaha" }
            });
```

`Template` class contains only attributes that are in the core of jsreport. If you are working on your own extensions and adding some additional values into jsreport report template you can send them using dynamic property called `additional`. jsreport client will send `additional` as the content would be directly inside `Template`.

```c#
var report = await _reportingService.RenderAsync(new RenderRequest() {
                template = new Template() {  shortid = "asd34fsd"  },
                additional = new {
                        someExtension = new {
                            valie = "specific value"
                        }
                    }
            });
```

The best way in these scenarios is to use jsreport studio API helper to get possible values. API helper  is located at the report template. You can read more about it in the [API section](http://jsreport.net/learn/api).

##Using reports extension

When you have `reports` extension enabled, you can send `saveResult=true`  option and let the jsreport server take care of storing output report. You just need to store output `report.PermanentLink` and later use it for downloading actual report content.

```c#
var report = await _reportingService.RenderAsync(new RenderRequest()
            {
                template = new Template() { shortid = "g1xcKBanJc" },
                options = new RenderOptions() { saveResult = true }
            });

var loadedReport = await _reportingService.ReadReportAsync(report.PermanentLink);
```

##Synchronizing visual studio templates
jsreport client can synchronize reports defined inside visual studio project using [jsreport visual studio tools](http://jsreport.net/learn/visual-studio-extension) against embedded or remote jsreport server.

```c#
await _reportingService.SynchronizeTemplatesAsync();
``` 

This will synchronize all images, report templates and sample data files from bin folder against jsreport server. Afterwards you can use report file names instead of shortids. This means that if you have a report template file called `Report1.jsrep` you can issue following request:

```c#
var report = await _reportingService.RenderAsync("Report1", new { ... });
```            

##Odata

jsreport API is based on [odata](http://www.odata.org/) and jsreport c# client exposes CRUD operations on jsreport entities using great [Simple.OData.Client](https://github.com/object/Simple.OData.Client) library. You can instantiate odata client using `CreateODataClient` method:

```c#
await _reportingService.CreateODataClient().GetSchemaAsync()
```

To understand jsreport entities checkout jsreport API metadata at `https://[jsreport address]/odata/$metadata`. You can find many examples how to work with odata cliet [here](https://github.com/object/Simple.OData.Client).


### Filtering

```c#
var template = await _reportingService.CreateODataClient().For<Template>()
        .Filter(x => x.shortid == "Report1")
        .FindEntryAsync();
```                             


### Modifying entries


```c#
var template = await _reportingService.CreateODataClient().For<Template>()
        .Filter(x => x.shortid == "Report1")
        .FindEntryAsync();

await _reportingService.CreateODataClient()
        .Key(template._id)
        .Set(new {
                _id = template._id,
                content = "Some modified content"
            })
        .UpdateEntryAsync();

```