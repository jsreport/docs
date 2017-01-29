> **[Download examples from GitHub](https://github.com/jsreport/net/tree/master/examples)**

##Basics
jsreport comes with .NET c# client which wraps REST API and is very convenient to use if you are in .NET environment. You can use .NET c# client to connect to a remote jsreport server and also to [jsreport .NET embedded server](https://jsreport.net/learn/net-embedded).

## Get Started

I assume that you already understand basic jsreport concepts. If you don't, please follow instruction in the [get started section](https://jsreport.net/learn/get-started).

Installing jsreport c# client is easy using nuget package:
> PM> Install-Package [jsreport.Client](https://www.nuget.org/packages/jsreport.Client/)

Main facade you will use to access jsreport from c# is called `ReportingService`. 

>on-prem without authentication
>```cs
>var _reportingService = new ReportingService("https://192.168.02.01");
>```

>on-prem with authentication
>```cs
>var _reportingService = new ReportingService("https://192.168.02.01", "username", "password);
>```

>jsreportonline
>```cs
>var _reportingService = new ReportingService("https://[subdomain].jsreportonline.net",
                                              "email", "password");
>```

>embedded
>```cs
>var embeddedServer = new EmbeddedReportingServer();
>await embeddedServer.StartAsync();
>var _reportingService = embeddedServer.ReportingService;
>```

When you have the `ReportingService` instance, you'll most likely want to invoke report rendering. In the most common case, you will grab `shortid` from jsreport studio, collect some data and invoke rendering.

```cs
var report = await _reportingService.RenderAsync("g1xcKBanJc", new {
	someData = "foo"
});

new StreamReader(report.Content).ReadToEnd();
```

##Advanced rendering

If you want to have full control on template rendering you can use `RenderAsync` overload which accepts `RenderRequest` instance allowing you to fill bunch of other options. For example if you have some kind of a dynamic template, you don't need to specify template shortid and you can construct template content in c#.

```cs
var report = await _reportingService.RenderAsync(new RenderRequest() {
                template = new Template()
                {
                    recipe = "html",
                    engine = "jsrender",
                    content = "some dynamic template content"
                },
                data = new { firstName = "Jan", surname = "Blaha" }
            });
```

`Template` class contains only attributes that are in the core of jsreport. If you want to add    some additional values into jsreport report template you can send them using dynamic property called `additional`. jsreport client will send `additional` as the content would be directly inside `Template`. This is very useful when using an extension which is adding some attributes into `Template`.

Next example shows how to add a [custom script](/learn/scripts) into template.
```cs
var report = await _reportingService.RenderAsync(new RenderRequest() {
                template = new Template() {  name = "foo"  },
                additional = new {
                        script= new {
                            content = "request.template.content='Hello'; done()"
                        }
                    }
            });
```

You can use same approach for `options` property. Next example shows how to use [reports extension](/learn/reports) to permanently store rendering output.

```cs
var report = await _reportingService.RenderAsync(new RenderRequest() {
                template = new Template() {  name = "foo"  },
                options = new RenderOptions()
	                {
	                       additional = new  {
	                           reports = new { save = true }
                           }
			        }
            });
```

If you want complete freedom, you can simply pass anonymous object into the `RenderAsync`. The object parameter only needs to follow the same structure which jsreport API expects. See more about it [here](https://jsreport.net/learn/api).

```cs
var report = await _reportingService.RenderAsync(new {
                template = new Template() {  
	                content = "foo", 
	                engine = "handlebars", 
				},
                options = new {                     
	                reports = new { save = true }
                }
            });
```
           
##Odata

jsreport API for doing CRUD on entities is based on [odata](http://www.odata.org/) and you can use  great [Simple.OData.Client](https://github.com/object/Simple.OData.Client) library to consume it. 

```c#
var client = new ODataClient("http://localhost:5488/odata");
var metadata = client.GetMetadataAsync().Result;
```

To understand jsreport entities checkout jsreport API metadata at `https://[jsreport address]/odata/$metadata`. You can find many examples how to work with odata cliet [here](https://github.com/object/Simple.OData.Client).


### Filtering

```c#
var template = await client .For<Template>()
        .Filter(x => x.shortid == "Report1")
        .FindEntryAsync();
```                             


### Modifying entries


```c#
var template = await client.For<Template>()
        .Filter(x => x.shortid == "Report1")
        .FindEntryAsync();

await client.For<Template>()
        .Key(template._id)
        .Set(new {
                _id = template._id,
                content = "Some modified content"
            })
        .UpdateEntryAsync();
```

##Timeouts
`ReportingService` uses .NET `HttpClient` to make http calls. This library has default timeout of 100seconds for a request. This doesn't have to be enough for bigger reports therefore you may need to change this default timeout.

```cs
reportingService.HttpClientTimeout = TimeSpan.FromMinutes(10);
```

##Run jsreport studio in asp.net web handler
`jsreport.Client` provide `JsReportWebHandler` class which can be used to expose jsreport http endpoint directly in your app. This asp.net http handler can be used as a tunnel forwarding requests from jsreport web into jsreport server through your web application. 

First add `JsReportWebHandler` to your web config.
```xml
<system.webServer>
  <handlers>
      <add  name="jsreport" verb="*" path="jsreport.axd" type="jsreport.Client.JsReportWebHandler" />
  </handlers>
</system.webServer>
```

Then initialize handler by adding jsreport server uri during application startup
```cs
JsReportWebHandler.ReportingService = EmbeddedReportingServer.ReportingService ;

//you can also use standalone jsreport with authentication enabled
JsReportWebHandler.ReportingService = new ReportingService("http://jsreport-host", "username", "password");
```

Now you can open your asp.net application and navigate to `/jsreport.axd`.

