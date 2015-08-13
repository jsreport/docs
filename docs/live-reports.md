jsreport is not limited for rendering static documents like pdf or excel. You can use it also to render live and user interactive reports. Reports where user can filter data, change the matrix or drill down. This is achieved by using one of the `html` recipes together with one of the external javascript data visualization components like [highcharts](http://www.highcharts.com), [pivot table](https://github.com/nicolaskruchten/pivottable) or any other.

##Example

This example shows how to create a self contained, excel and pdf exportable and user interactive report using jsreport together with [pivottable](https://github.com/nicolaskruchten/pivottable) javascript component.

###Sample data
As in the first example, we need to prepare some sample data for this report before we start designing report template. The sample data we use this time are stored [here](http://nicolas.kruchten.com/pivottable/examples/mps.json). Just take the json from there and put it into a new sample data inside jsreport studio. Make just a tiny modification and wrap the array from the json into an object with property carrying the array.


### Define a template
The live reports are designed to run inside browser so you should choose from the recipes producing html.  This example use [wrapped-html](/learn/wrapped-html) which produces html with prepacked jsreport libraries that helps with exporting, editing or refreshing templates. Lets create a template with this recipe for now.

The template needs to link the pivot table library. This includes custom css, pivot table script, jquery and jquery ui.

```html
<link rel="stylesheet" type="text/css" href="https://cdnjs.cloudflare.com/ajax/libs/pivottable/1.3.0/pivot.min.css"/>
<script src="https://code.jquery.com/jquery-1.11.0.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/pivottable/1.3.0/pivot.min.js"></script>
<script src="https://code.jquery.com/ui/1.11.1/jquery-ui.min.js"></script>
```

Now its time to add add pivot table to the template and load input data in it. To access the input data we can use global `jsreport` object which is automatically added by `wrapped-html` recipe. In the most simple case html body can look the following:

```html
<body>
	<div id="pivottable"></div>
	<script>
		$(function(){
			$("#pivottable").pivotUI(jsreport.template.data.items);
		});
	</script>
</body>
```

Now you should be able to run the template and play with the live pivot table. You can also add a server side javascript templating engines rendering, [images](learn/images), [child templates](/learn/child-templates) or anything you already know from jsreport.

###Export report
Another feature you can add to jsreport live report is exporting capability into excel or pdf.  This can be achieved by calling jsreport server API directly from the live report. You basically virtually  create a template with particular recipe like `phantom-pdf` and render it remotely in jsreport server. To do it you use `render` function provided by the global `jsreport` object. The best is to show it on an example:

```js
$("#printCommand").click(function() {
	var printTemplate = $.extend({}, jsreport.template);
    printTemplate.recipe = "phantom-pdf";
    printTemplate.content = document.documentElement.outerHTML;
    
    printTemplate.phantom = { 
	    printDelay : 1000, /* wait a little bit for js rendering */
	    blockJavaScript : true, /* block pivot table from new rendering */                       
    };
    jsreport.render(printTemplate);
});
```

This example clones the original template and modifies it's recipe because we want to export to the pdf. It also takes the snapshot of the current html because we want to reflect the current configuration user made in the pivot table.  Calling the `render` method should open a new browser tab with the exported pdf.

You can use the same approach also to export to the excel. The nice thing about it is that you can assemble the exporting content dynamically. For example just snapshot part of the page or construct completely customized printing template.

```js
$("#printExcel").click(function() {
	var printTemplate = $.extend({}, jsreport.template);
    printTemplate.recipe = "html-to-xlsx";
    printTemplate.content = $(".pvtRendererArea")[0].outerHTML;
    jsreport.render(printTemplate);
});
```

You can find the full running example [here in the playground](https://playground.jsreport.net/#/playground/-yaLa6luK).

###API

Rendering such a live report through API is the same as any other report:

> `POST:` https://localhost/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`<br/>
>```js
   {
      "template": { "shortid" : "eygi2w2axR" },
      "data" : { "items": [...] }
   }
>```







