## Basics

If you prefer to construct your reports using ASP.NET MVC engines like Razor you can benefit from [jsreport.MVC](https://www.nuget.org/packages/jsreport.MVC) nuget package. This package provides `JsReportFilterAttribute` capable of transforming view output into any format jsreport supports. You can for example easily transform MVC view into pdf or excel. The idea is to use views as html generator and jsreport server as transformer to the desired output. 

Note this package is dedicated to `ASP.NET` applications running the full framework. Please navigate to similar [jsreport.AspNetCore](/learn/dotnet-aspnetcore) documentation when running `ASP.NET core`.

The example sources are hosted in [jsreport/jsreport-dotnet-example-net-webapp](https://github.com/jsreport/jsreport-dotnet-example-webapp) repository.

## Get Started

[jsreport.MVC](https://www.nuget.org/packages/jsreport.MVC)  includes just helper classes to work with jsreport and it still requires to connect to an instance running through [jsreport.Local](/learn/dotnet-local) or to the instance running remotely through [jsreport.Client](/learn/dotnet-client).


For this basic example install nuget packages [jsreport.Binary](https://www.nuget.org/packages/jsreport.Binary), [jsreport.Local](https://www.nuget.org/packages/jsreport.Local) and [jsreport.MVC](https://www.nuget.org/packages/jsreport.MVC) and edit the `FilterConfig.cs` as following. 

```csharp
public static void RegisterGlobalFilters(GlobalFilterCollection filters)
{
	filters.Add(new JsReportFilterAttribute(new LocalReporting()
		.UseBinary(JsReportBinary.GetBinary())
		.AsUtility()
		.Create()));
}
```

This configures jsreport ASP.NET filter to render output using the local binary. The next step is to decorate the particular controller's action with ``EnableJsReport` ` and specify the jsreport recipe for transforming the view output. 

```csharp
[EnableJsReport()]
public IActionResult Invoice()
{
	HttpContext.JsReportFeature().Recipe(Recipe.PhantomPdf);
	return View();
}
```

This enables ASP.NET filter which captures the view rendering result and uses the specified [phantom-pdf recipe](/learn/phantom-pdf) to convert the output html into pdf. 

## Advanced configuration

The most of the request as well as the response configuration can be set through `HttpContext.JsReportFeature()`. You can configure jsreport request to for example [convert html to excel](/learn/html-to-xlsx) evaluate javascript [templating engines](/learn/templating-engines) on top of the razor, run [custom javascript functions](/learn/scripts) and much more. You can find more information how to do this in the documentation for [jsreport.AspNetCore](/learn/dotnet-aspnetcore) package, because the API in this case is the same.

### Pdf headers

The only case when the API for the full framework ASP.NET and ASP.NET Core is different is configuring pdf headers. In the both cases you can specify header in extra partial view, but this time you should use the extension method `RenderViewToString` on controller to render it to string and provide the output content to jsreport.

```csharp 
[EnableJsReport()]
public ActionResult InvoiceWithHeader()
{
	HttpContext.JsReportFeature()
		.Recipe(Recipe.PhantomPdf)
		.Configure((r) => r.Template.Phantom.Header = this.RenderViewToString("Header", new { }));

	return View("Invoice", InvoiceModel.Example());
}
```