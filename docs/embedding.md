`Embedding` extensions allows to open limited version of jsreport studio inside any web application and let end users to customize their reports. There are various scenarios where this can be used. Typical example can be when application is sending invoices to the customers and allows them to modify invoice template to the required design.

##Get started

Embedding jsreport is quite similar to inserting other frameworks to page like Facebook or twitter.  The most simple example of a web page with embedded jsreport editor looks following:

```html
<!DOCTYPE html>
<html>
<head lang="en">
	<!-- jquery is required for jsreport embedding -->
    <script src="//code.jquery.com/jquery-1.11.0.min.js"></script>
</head>
<body>
	<script>
		//jsreport will call jsreportInit function from global scope when its initialized
	    jsreportInit = function () {
		    //lets open an editor for template with specific shortid right away
	        jsreport.openEditor("Z1vT7FHyU");
	    };
	</script>

	<script>
		//add jsreport embedding script, just change url to jsreport server
	    (function (d, s, id) {
	        var js, fjs = d.getElementsByTagName(s)[0];
	        if (d.getElementById(id)) {
	            return;
	        }
	        js = d.createElement(s);
	        js.id = id;
	        js.src = "http://local.net:2000/extension/embedding/public/embed.min.js";
	        fjs.parentNode.insertBefore(js, fjs);
	    }(document, 'script', 'jsreport-embedding'));
	</script>
</body>
</html>
```
As you can see you need to fulfill four steps:
1. Include jquery into page
2. Add jsreport embedding script with target to running jsreport server
3. Create `jsreportInit` function in the global scope with similar meaning as `$.ready`
4. Use global object `jsreport` and its function `openEditor` to pop up jsreport

##Preview report in editor

After opening jsreport editor user can customize template and preview report as well. 

In the scenario where is the report generated at user request based on dynamic data from an html form or a web service you can use `openEditor` overload and specify dynamic input data.

```js
jsreport.openEditor({
    shortid: "Z1vT7FHyU",
    data: {
	    price: "1234"
    }
});
```

You can also set up a [custom script](/learn/scripts) to the report template loading input data for the report preview. Using custom scripts user can even specify desired input data source on its own.

##Render report to the placeholder

jsreport does not only handle end user report customization but also dynamic report rendering into specified placeholder. Use `jsreport.render` function for this:

```js
jsreport.render($("#placeholder"), "byvZXxtkB");
```

##Html widgets

jsreport embedding together with [client-html recipe](/learn/client-html) makes powerful combination allowing to add end user customizable html widgets into every web application.

First you need to create a jsreport template with [client-html recipe](/learn/client-html). Then add a placeholder for widget in your application and specify template shortid using `data-jsreport-widget` attribute.

```html
<div data-jsreport-widget="b1I_ThWlS" style="height: 500px"></div>
```
The last piece is to call `jsreport.renderAll` and specified widget should be rendered by jsreport.
```js
jsreport.renderAll()
```

Every widget contains an edit button which pops up when hovering widget. Edit button will open jsreport embedded editor where user can quickly modify the widget.

You can read more about widgets and client html recipe [here](/learn/client-html).

## Security

By embedding jsreport into your page you basically open server into public therefore you need to handle permissions and access rights to the report templates. jsreport is designed not to maintain authorization rules but rather ask external service if the particular user should be authorized for particular operation. This means you can keep all the authorization rules inside your application and jsreport will just ask for it. See [authorization](/learn/authorization) extension for details.
