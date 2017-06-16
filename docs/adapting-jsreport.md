## Configuration file
The most common way to adapt jsreport settings is using configuration file. Configuration file is stored at the root directory with name `[dev | prod].config.json`. Editing this file you can change for example port service is running on or scale up number of worker processes jsreport uses. You can find full documentation of various options [here](/learn/configuration).

## Configuring using nodejs
In addition to configuration file based settings you can also use [node.js](http://nodejs.org) and dynamically adapt jsreport to your needs. Following chapters applies to those using jsreport on premise and want to apply some advanced configurations.

Installing and initializing jsreport on premise will create `server.js` file containing following code:
```js
require('jsreport')().init();
```

This is the most basic jsreport server startup. It searches for `[dev | prod].config.json` file depending on the nodejs environment and initialize server based on settings it finds in the configuration file or applies defaults. Same code can be also used when integrating jsreport directly to the existing nodejs application.

In addition to configuration file, jsreport also parses values from environment variables, command line arguments or you can pass them directly to the jsreport instantiation. Options passed as a parameter take precedence over environment variables which then take precedence over configuration file.

In the following example are the options passed directly to the jsreport instantiation.
```js
require('jsreport')({ httpPort: 3000, httpsPort: 0 }).init();
```

## Additional extensions
jsreport can be easily extended with additional extensions. Extension usually adds a new engine, recipe or even complex studio functionality. There are plenty of them ready to be installed like extension allowing you to store templates in [mongodb](https://github.com/jsreport/jsreport-mongodb-store) or another extension storing reports in [Microsoft Azure](https://github.com/jsreport/jsreport-azure-storage).  You can find the list of extensions in the [jsreport official documentation](/learn/extensions) or on the [github](https://github.com/jsreport/jsreport-core#list-of-extensions). If you didn't find what you are looking for, you can even implement your own custom extension. This topic is described separately [here](/learn/custom-extension).

## Rendering
The goal of jsreport is to render reports. To do it you use `jsreport.render`:

```js
jsreport.init().then(function () {     
   jsreport.render({
       template: {
           content: '<h1>Hello {{:foo}}</h1>',
           engine: 'jsrender',
           recipe: 'phantom-pdf'
        },
        data: {
            foo: "world"
        }
    }).then(function(resp) {
     //prints pdf with headline Hello world
     console.log(resp.content.toString())
   });
}).catch(function(e) {
  console.log(e)
})
```

See the [jsreport-core](https://github.com/jsreport/jsreport-core) for complete documentation for rendering.

## Attach to existing express app
jsreport by default starts [express.js](http://expressjs.com/) based server running on ports specified in config. This behavior can be overridden with passing `express` application instance to the options. In this case jsreport `express` extension will just add required routes and middle-wares to the passed instance.

```js
var express = require('express');
var app = express();

app.get('/', function (req, res) {
  res.send('Hello from the main application');
});

var reportingApp = express();
app.use('/reporting', reportingApp);

var server = app.listen(3000);

var jsreport = require('jsreport')({
  express: { app :reportingApp, server: server },
  appPath: "/reporting"
});

jsreport.init().catch(function (e) {
  console.error(e);
});
```

## Using rendering shortcut

It can be convenient sometimes to use jsreport shortcut `require("jsreport").render` if you want just to render a report. This happens to be usefull when you are not interested in starting jsreport server or storing data in the document store. Shortcut doesn't use configuration files but instead you can add/modify configurations dynamicaly into `require("jsreport").renderDefaults`, just make sure to **only modify the existing `require("jsreport").renderDefaults` object, if you assign a new object in `renderDefaults` it will not be recognized**.

```js
var http = require('http');
var jsreport = require('jsreport');

http.createServer(function (req, res) {
  jsreport.render({ template: { content: 'Hello world', engine: 'jsrender', recipe: 'phantom-pdf' } }).then(function(out) {
    out.stream.pipe(res);
  }).catch(function(e) {
    res.end(e.message);
  });

}).listen(1337, '127.0.0.1');
```

## Dynamic access to jsreport entities
Once you have the jsreport running with some templates or reports created. You can reach them on the server using `documentStore` property.  The API to search and manipulate underlying jsreport data is very similar to official [mongodb driver](https://github.com/mongodb/node-mongodb-native). It is just using promises.

```js
reporter.documentStore.collection("templates")
	.find({ name: "test"})
	.then(function(res) {
	...
});
```

You can read more about `documentStore` in the article describing [how to write custom extension](/learn/custom-extension).
