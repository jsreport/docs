jsreport node.js client is a wrapper around [jsreport api](http://jsreport.net/learn/api) allowing to connect to a remote jsreport server, render pdf reports and operate on the entities. It supports both local [on-premise](http://jsreport.net/on-prem) jsreport server as well as jsreport [online SaaS](http://jsreport.net/online).

##Installation
> npm install jsreport-client

##Initialization

`jsreport-client` package returns function with parameters `url`, `username` and `password` where only `url` is mandatory. Function returns `Client` object representing facade to remote jsreport server running on specified `url`.

```js
var client = require("jsreport-client")(url, username, password)
```

##Rendering reports
`Client` instance contains `render` method that triggers remote report rendering process and callbacks response. `Render` parameter is a request object that is posted to the jsreport server. It needs to contain a `template` property representing a reference to the existing jsreport template using `shortid` or raw definition of the template. Row definition typically defines `content`, `helpers`, `recipe` and `engine` property. Second but optional property of request is `data` representing report input data. See [API](http://jsreport.net/learn/api) documentation for details.

```js
client.render({
	template: { content: "hello {{:someText}}", recipe: "html",
				engine: "jsrender" },
	data: { someText: "world!!" }
}, function(err, response) {
	response.body(function(body) {
		//prints hello world!!
		console.log(body.toString());
	});
});
```

##Chaining response stream
Object returned in callback of `render` method is a report stream and can be piped to the file or also directly to the [express](http://expressjs.com) response. Response already contains all the proper headers and can by directly opened in the browser.
```js
app.get("/report", function(req, res, next) {
    client.render({
        template: { content: "Hello World", recipe: "phantom-pdf"}
    }, function(err, response) {
        if (err) {
            return next(err);
        }
        response.pipe(res);
    });
});
```

##Handling request timeout

Optionally you can pass second parameter to the `render` function containing additional options of the made http request. These options are passed to the [request](https://github.com/request/request) module making actual rest call.  Most likely you should be interested in the timeout property:

```js
client.render({
	template: { }	
}, { timeout: 5000 },function(err, response) {

});
```

