>  **[Download example from GitHub](https://github.com/jsreport/net/tree/master/examples/Mvc)**

##Basics

If you prefer to construct your reports using ASP.NET MVC engines like Razor you can benefit from `jsreport.MVC` nuget package. This package provides a filter capable of transforming view output into any format jsreport supports. You can for example easily transform MVC view into pdf or excel. The idea is to use views as html generator and jsreport server as transformer to the desired output. 

##Get Started
To get started you need to install `jsreport.MVC` package into your `asp.net mvc` project first.

> Install-Package jsreport.MVC

Then you need to add `JsReportFilterAttribute` to filters collection. This require access to jsreport server. You can use [.net embedded](http://jsreport.net/learn/net-embedded) or [on prem](http://jsreport.net/on-prem) or [jsreport online](http://jsreport.net/online) service.
```c#
public static void RegisterGlobalFilters(GlobalFilterCollection filters)
{
  filters.Add(new HandleErrorAttribute());

  //when using .net embedded version you need to start server first
  EmbeddedReportingServer embeddedReportingServer = new EmbeddedReportingServer();
  embeddedReportingServer.StartAsync().Wait();
  filters.Add(new JsReportFilterAttribute(embeddedReportingServer.ReportingService));

  //when using on prem or online just instantiate ReportingService with correct url
  //filters.Add(new JsReportFilterAttribute(new ReportingService("http://localhost:2000")));
}
```
The last step is to enable particular controller or action to use jsreport for rendering. You will do this using `EnableJsReport` attribute.

```c#
//render view as pdf converted from html
[EnableJsReport(Recipe = "phantom-pdf")]
public ActionResult Pdf()
{
  return View();
}

//render view as xlsx converted from html table
[EnableJsReport(Recipe = "html-to-xlsx")]
public ActionResult Xlsx()
{
  return View();
}
```

And now you can try to visit the page and it should open a pdf respectively xlsx instead of html.

jsreport will evaluate all your css files and even javascript. You only need to keep in mind that every resource like script or style has to be linked with absolute url.

####Dynamic recipe resolution
The `EnableJsReport` doesn't let you pass anything dynamic, however you can fully specify the required rendering request and pass it to `ViewBag.jsreportRenderRequest` inside the action code.
```cs
[EnableJsReport()]
public ActionResult Contact()
{
	ViewBag.jsreportRenderRequest = new RenderRequest() {
	    template = new Template() {
                            engine = "none",
                            recipe = "phantom-pdf"
                        }
    };
    return View();
}
```
