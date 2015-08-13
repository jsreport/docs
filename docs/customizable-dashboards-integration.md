One of the advanced features provided by jsreport is embeddable report designer which you can integrate into your application and let your users customize reports directly in the browser. Imagine for example a scenario in which user can modify invoice style before the systems creates a pdf and sends it. To go even further, it doesn't need to be a static pdf. In fact embeddable designer can be used together with live reports to add interactive customizable widgets or dashboards into your application. This sample demonstrates how to create such an application with integrated jsreport.

> Source code of the sample application integrated with the jsreport can be found **[here](https://github.com/jsreport/examples/tree/master/customizableDashboard)**.

##Preparing application

This sample shows how to integrate jsreport designer into an application and provide to the  end user widget customization capabilities. In this sample are widget definitions created through jsreport API and stored inside jsreport server as report templates, however this is not a requirement and applications can store templates themselves.

The sample covers following areas:

 - how to create a report template (widget definition) using jsreport API
 - how to render a report template into application's web
 - how to open jsreport designer directly from the application's web
 - how to securely access application's data from the widget
 - how to secure jsreport server

The provided source codes for this sample are using node.js for easiness, but it should be easily transformable into any server language. This article even doesn't use any server side language for examples and it is mostly about describing principles and REST calls.

If you want to try the integration on your own you need to install jsreport on your machine first. You can find installation instructions [here](/download). This article assumes jsreport without authentication enabled first and covers jsreport security in the last chapter.

##Create templates using API

The first thing the application should be able to do on the server side is create a report templates later used as widgets. This can be done by a single rest call:

> `POST:` http://localhost:4000/odata/templates<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js
   {
       "content" : "name": "foo", "Hello world", "recipe" : "wrapped-html", "engine": "jsrender"
   }
>```

The response is a newly created report template including `shortid` attribute which needs to be stored for the later identification of the template. 

##Render template from the server

When the report template is created you should be able to render it using API. This action could be also invoked from the browser but for the security reasons explained later you should do it from the application's server side.

> `POST:` http://localhost:4000/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js
   {
      "template": { "shortid" : "dsra23" },
   }
>```

The response should be a stream with the content of the newly created report. The application wraps the template rendering call and expose it as a route with `shortid` parameter in the web application.

##Render template from the browser

The application server should be now able to create and render the widget.  It's time to switch into the application web and add the widget into the html. Let's assume widget was already created and we just want to render it. 

The widget content could be just added into a html placeholder, but this would only work for some simple widgets. Imagine a widget with linked charting library from the cdn or some global styles. The best way to isolate such a widget is to render it into an iframe:

```html
<iframe scrolling="no" style="min-height:300px; width:100%" frameborder="0" name="{{widget.shortid}}"></iframe>
```

The iframe could already contain widget content, but it is better to add it in the asynchronous way to avoid slowing down the main page. The asynchronous load can be achieved by javascript based submit of the fake form into the iframe with the action equal to the previously created application route.

```js
var form = document.createElement("form");
form.action = "/widget/{{widget.shortid}}";
form.target = "{{widget.shortid}}";
document.body.appendChild(form);
form.submit();
```

This can be done directly on the page load and it should fill the iframe with the content of the rendered report template.



##Embeddeding designer

To be able to open widget designer, application main page needs to link jsreport `embed.js` script and also its`jquery` dependency.

```html
<script src="https://code.jquery.com/jquery-1.11.0.min.js"></script>
<script src="http://localhost:4000/extension/embedding/public/js/embed.js"></script>
```

Then the application gets access to the global javascript object named `jsreport`. This object can be used for opening designer in which user can easily preview and modify the widget. When the designer pop up is closed, application can for example reload the page to reflect the changes.

```js
jsreport.openEditor({{widget.shortid}}, { useStandardStorage: true })
	.on("close", function (template) {
            window.location.reload();
        });
```

##Secure accessing application data

The application user should be able to customize widget in jsreport designer now.  However the widget won't be very helpful without input data provided by the application. The next chapters describes approaches you can follow to securely provide application data into  the widgets.

###Push data into the report rendering

The first approach pushes data into the widget during rendering. The widget can then access data provided by the application in the common way. This is secure by design because application can push only authorized data into the rendering and there is no other way for the widget to access something else.

> `POST:` http://localhost:4000/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js
   {
      "template": { "shortid" : "dsra23" },
      "data": { "items": [ { Name: "foo" } ]
   }
>```

To be able to open jsreport designer together with provided data you need to adapt it's opening. The easiest way is to set the particular template recipe to `wrapped-html` which exposes currently rendered template together with the input data in the global javascript object. Then you can just use it as a source for opening editor:

```js
var iframe = document.getElementsByName(shortid)[0];
jsreport.openEditor(
	iframe.contentWindow.jsreport.template, 
	{ useStandardStorage: true} );
```

Note that this won't violate the cross domain iframe access policy because the widget iframe was loaded from the same domain as the application.

This is the easiest approach to implement and also secure by design. However it limits the user in the scope of visualized data and user can not change it and include for example another entity in the chart. This can be solved by using one of the next approaches.


###Use server script
The next approach uses jsreport [scripts](/learn/scripts) extension to access the data from the widget. This extension allows user to write a custom javascript function which then runs on jsreport server each time the widget is being rendered. This is particularly helpful for doing an extra request and fetch data from the original application. Fetched data can be then used in the widget in the common way.

Assuming the application has an API for data access then the custom script loading data can look the following:

```js
function beforeRender(done) {
    require("request")({
        url: "http://local.net:1000/data"
        json: true
    }, function (err, response, body) {
        request.data = body;
        done();
    });
}
```

The user can fill such a script directly in the jsreport embedded designer or the application can pre-create it. This can be done just by modifying creating template:
 
> `POST:` http://localhost:4000/odata/templates<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js
   {
       "content" : "name": "foo", "Hello world", "recipe" : "wrapped-html", "engine": "jsrender"
       "script": {
	       "content": "the custom script content"
	    }      
   }
>```

This is a very flexible way to define scope of the widget input data however not very secure one. The problem sits in the custom script requesting data from the application because the request doesn't identify the request user authority. This can be solved by giving a security token to the template during the rendering which application later verifies when serving the data to the custom script request. 

To give a secure token to the widget you can adapt the rendering in the following way:

> `POST:` http://localhost:4000/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js
   {
      "template": { "shortid" : "dsra23" },
      "options": {
            "verificationToken": "a-unique-token-related-touser",       
        }
   }
>```

Now you can add the security token into the custom script:
```js

    require("request")({
        url: 'http://local.net:1000/data?verificationToken=' + request.options.verificationToken,
        json: true
    }, function (err, response, body) {
        request.data = body;
        done();
    });
}
```

The last piece is just to verify the token inside the application `/data` route. Note that you need to use the same code for opening the jsreport designer as in the previous approach to propagate security token also to previewing the widget.

###Reuse browser session
The last approach takes advantage of the fact that widget iframe has the same domain as the parent application. This means the script widget can easily access the global functions of the parent page or share its cookie. In other words widget can use the same security mechanism as the parent page for doing calls directly from the browser. This can be for example used together with the [client-html](/learn/client-html) for designing and rendering widgets completely on the client side.

To see it in the action let's define a global function getting some data from the server in the parent page 

```js
function loadData(cb) {
        $.getJSON("/data", function(data) {
	        cb(data);
        }
    }
```

Now you can create a widget with `client-html` recipe and call this function and render the fetched data using a templating engine:

```html
<table id="dataTable">
	{{for items}}
	    <tr>
	       <td>{{:names}}</td>
	    </tr>
    {/for}}
</table>

<script>
	window.top.loadData(function(data) {
		jsreport.reload("#dataTable", data);
	});
</script>
```


##Enable authentication in jsreport

This whole article assumed that jsreport server is exposed to the public without any security mechanisms.  To change it you need to enable [authentication](/learn/authentication) on the jsereport server first.  This can be done by adding `authentication` property to the jsreport config file.

```js
"authentication" : {
	"cookieSession": {
		"secret": "dasd321as56d1sd5s61vdv32"
	},
    "admin": {
	    "username" : "admin",
        "password": "password"
    }
}
```

Enabling authentication forces the application to include credentials to the server requests:

> `POST:` http://localhost:4000/api/report <br/>
> `Headers`:<br/>
> Content-Type: application/json    
>Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ== 

The next mechanism which needs to be secured is editing widgets.  This can be done using [public-templates](/learn/public-templates) extension and granting edit access to the template during rendering:

> `POST:` http://localhost:4000/api/report <br/>
> `Headers`: Content-Type: application/json   <br/>
> `BODY:`    
>```js
   {
      "template": { "shortid" : "dsra23" },
      "options": {
            "authorization": {
	            "grantEdit": true
      }
    }
   }
>```

Rendering template with `authorization.grantEdit` option adds a secure token into the output itself which is used later by jsreport server to authorize edit operations on the template. To provide this token into jsreport designer you need to again use a modified code to open it:

```js
var iframe = document.getElementsByName(shortid)[0];
jsreport.openEditor(
	iframe.contentWindow.jsreport.template, 
	{ useStandardStorage: true} );
```

