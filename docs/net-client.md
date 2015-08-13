> **[Download examples from GitHub](https://github.com/jsreport/net/tree/master/examples)**

##Basics
jsreport comes with .NET c# client which wraps REST API and is very convenient to use if you are in .NET environment. You can use .NET c# client to connect to a remote jsreport server and also to [jsreport .NET embedded server](http://jsreport.net/learn/net-embedded).

## Get Started

I assume that you already understand basic jsreport concepts. If you don't, please follow instruction in the [get started section](http://jsreport.net/learn/get-started).

Installing jsreport c# client is easy using nuget package:
> PM> Install-Package [jsreport.Client](https://www.nuget.org/packages/jsreport.Client/)

Main facade you will use to access jsreport from c# is called `ReportingService`. 

>on-prem without authentication
>```c#
>var _reportingService = new ReportingService("https://192.168.02.01");
>```

>on-prem with authentication
>```c#
>var _reportingService = new ReportingService("https://192.168.02.01", "username", "password);
>```

>jsreportonline
>```c#
>var _reportingService = new ReportingService("https://[subdomain].jsreportonline.net",
                                              "email", "password");
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

`Template` class contains only attributes that are in the core of jsreport. If you want to add    some additional values into jsreport report template you can send them using dynamic property called `additional`. jsreport client will send `additional` as the content would be directly inside `Template`. This is very useful when using an extension which is adding some attributes into `Template`.

Next example shows how to add a [custom script](/learn/scripts) into template.
```c#
var report = await _reportingService.RenderAsync(new RenderRequest() {
                template = new Template() {  content = "foo"  },
                additional = new {
                        script= new {
                            content = "request.template.content='Hello'; done()"
                        }
                    }
            });
```

You can use same approach for `options` property. Next example shows how to use [reports extension](/learn/reports) to permanently store rendering output.

```js
var report = await _reportingService.RenderAsync(new RenderRequest() {
                template = new Template() {  content = "foo"  },
                options = new RenderOptions()
	                {
	                       additional = new  {
	                           reports = new { save = true }
                           }
			        }
            });
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

##Timeouts
`ReportingService` uses .NET `HttpClient` to make http calls. This library has default timeout of 100seconds for a request. This doesn't have to be enough for bigger reports therefore you may need to change this default timeout.

```cs
reportingService.HttpClientTimeout = TimeSpan.FromMinutes(10);
```

##Embedding
jsreport includes [embedding extension](/learn/embedding) which you can use to add report editor into your web pages and allow your customers to customize their reports. `jsreport.Client` package makes this easier by providing`JsReportWebHandler` class. This asp.net http handler can be used as a tunnel forwarding requests from jsreport web into jsreport server through your web application. This is very convenient especially together with [jsreport.Embedded](/learn/netembedded) package because it securely hides jsreport behind your application and meanwhile provide it to your web users.

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

Last you need to add jsreport `embed.js` to your page in the following way
```js
 <script>           
	(function (d, s, id) {
	    var js, fjs = d.getElementsByTagName(s)[0];
        if (d.getElementById(id)) {
	        return;
        }
        js = d.createElement(s);
        js.id = id;
        js.src = "/jsreport.axd?url=/extension/embedding/public/js/embed.js";
        fjs.parentNode.insertBefore(js, fjs);
	}(document, 'script', 'jsreport-embedding'));
</script>
```

You can find example for embedding jsreport into asp.net application on [github](https://github.com/jsreport/net/tree/master/examples/EndUserCustomizations) and also online running at [net-embedding.jsreport.net](http://net-embedding.jsreport.net/) and general documentation for embedding functions [here](/learn/embedding).

