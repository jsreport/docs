

## Basics

If you prefer to construct your reports using ASP.NET MVC engines like Razor you can benefit from [jsreport.AspNetCore](https://www.nuget.org/packages/jsreport.AspNetCore) nuget package. This package provides middleware filter capable of transforming view output into any format jsreport supports. You can for example easily transform MVC view into pdf or excel. The idea is to use views as html generator and jsreport server as transformer to the desired output.

Note this package is dedicated to `ASP.NET Core` applications. Please navigate to similar [jsreport.MVC](/learn/dotnet-mvc) documentation when working with the ASP.NET application running full .NET framework.

The example sources are hosted in [jsreport/jsreport-dotnet-example-webapp](https://github.com/jsreport/jsreport-dotnet-example-webapp) repository.

## Get Started

`jsreport.AspNetCore` includes just helper classes to work with jsreport and it still requires to connect to instance running through `jsreport.Local` or to the instance running remotely through `jsreport.Client`.

This basic example uses `jsreport.AspNetCore`, `jsreport.Local` and `jsreport.Binary` packages for setup.

```csharp
public void ConfigureServices(IServiceCollection services)
{
	services.AddMvc();              
	services.AddJsReport(new LocalReporting()
		.UseBinary(JsReportBinary.GetBinary())
		.AsUtility()
		.Create());
}
```

The next step is to decorate the particular controller's action with `MiddlewareFilter` and specify the jsreport recipe for transforming the view output.

```csharp
[MiddlewareFilter(typeof(JsReportPipeline))]
public IActionResult Invoice()
{
	HttpContext.JsReportFeature().Recipe(Recipe.PhantomPdf);
	return View();
}
```

This enables asp.net filter which captures the view rendering result and uses the specified [phantom-pdf recipe](/learn/phantom-pdf) to convert the output html into pdf.

This is just very basic scenario particularly useful in asp.net based applications. However jsreport includes tons of nice features worth it to explore. It allows to [convert html to excel](/learn/html-to-xlsx), evaluate javascript [templating engines](/learn/templating-engines) on top of the razor, run [custom javascript functions](/learn/scripts) and much more.

## Modify response

The response headers or other advanced response's attributes can be filled inside the `OnAfterRender` hook.

```csharp
var contentDisposition = "attachment; filename=\"myReport.pdf\"";

HttpContext.JsReportFeature().Recipe(Recipe.PhantomPdf)
                .OnAfterRender((r) => HttpContext.Response.Headers["Content-Disposition"] = contentDisposition);
```

## Save to file
`OnAfterRender`  hook can be used also to store output to file and at the same time return stream as the response.
```csharp
[MiddlewareFilter(typeof(JsReportPipeline))]
public async Task<IActionResult> InvoiceWithHeader()
{
	HttpContext.JsReportFeature().Recipe(Recipe.PhantomPdf);
	HttpContext.JsReportFeature().OnAfterRender((r) => {
		using (var file = System.IO.File.Open("report.pdf", FileMode.Create))
		{
			r.Content.CopyTo(file);
		}
		r.Content.Seek(0, SeekOrigin.Begin);
	});
	return View(InvoiceModel.Example());
}	
```

## Pdf headers

The pdf headers can be specified in extra partial view and rendered in runtime along with the main pdf content.  The first you need to get the `IJsReportMVCService` helper class from the asp.net container.

```csharp
public IJsReportMVCService JsReportMVCService { get; }

public HomeController(IJsReportMVCService jsReportMVCService)
{
	JsReportMVCService = jsReportMVCService;
}
```      

Afterwards you can use it to render a partial view based on its name, get the header content and provide it to the jsreport request.

```csharp
[MiddlewareFilter(typeof(JsReportPipeline))]
public IActionResult InvoiceWithHeader()
{
	var header = await JsReportMVCService.RenderViewToStringAsync(
		HttpContext, RouteData, "Header", new { });

	HttpContext.JsReportFeature()
	    .Recipe(Recipe.PhantomPdf)
	    .Configure((r) => r.Template.Phantom = new Phantom{ Header = header });

	return View("Invoice", InvoiceModel.Example());
}
```  

## Debug logs

Calling `DebugLogsToResponse` instructs the filter to write jsreport logs into the response instead of the report content. This is useful when dealing with errors  which may occur during phantomjs conversion.

```csharp
[MiddlewareFilter(typeof(JsReportPipeline))]
public IActionResult InvoiceDebugLogs()
{
    HttpContext.JsReportFeature()
        .DebugLogsToResponse()
        .Recipe(Recipe.PhantomPdf);

    return View("Invoice", InvoiceModel.Example());
}
```

## Render from html without Razor
You don't have to use Razor and MVC views to render the report html content. The rendering can be invoked just from raw html as well.
```csharp
var report = await JsReportMVCService.RenderAsync(new RenderRequest()
{
	Template = new Template
	{
		Content = "<h1>Hello world</h1>",
		Engine = Engine.None,
		Recipe = Recipe.PhantomPdf
	}
});
```