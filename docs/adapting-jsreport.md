##Configuration file
The most common way to adapt jsreport settings is using configuration file. Configuration file is stored at the root directory with name `[dev | prod].config.json`. Editing this file you can change for example port service is running on or scale up number of worker processes jsreport uses. You can find full documentation of various options [here](https://github.com/jsreport/jsreport/blob/master/config.md).

##Configuring using nodejs
In addition to configuration file based settings you can also use [node.js](http://nodejs.org) and dynamically adapt jsreport to your needs. Following chapters applies to those using jsreport on premise and want to apply some advanced configurations. 

### Basic startup

Installing and initializing jsreport on premise will create `server.js` file containing following code:
```js
require('jsreport').bootstrapper().start();
```

This is the most basic jsreport server startup. It searches for `[dev | prod].config.json` file depending on the nodejs environment and initialize server based on settings it finds in the configuration file or applies defaults. Same code can be also used when integrating jsreport directly to the existing nodejs application.

In addition to configuration file you can add options also to the `bootstrapper` function as a parameter or also pass options through environment variables. Options passed as a parameter take precedence over environment variables which then takes precedence over configuration file. Changing http port jsreport server will listen on can be done then through environemnt variable or through bootstrapper parameter like this:
```js
require('jsreport').bootstrapper({ httpPort: 3000 }).start();
```

### Reporter class
Once you bootstrap jsreport you get access to a `Reporter` class instance representing a facade to the most of jsreport functions including report rendering. `Reporter` instance can be obtained from a promise returned by `start` method.

```js
require('jsreport').bootstrapper().start().then(function(bootstrap) {
	var reporter = bootstrap.reporter;
};
```

###Dynamic rendering of reports
The goal of jsreport is to render reports. This can be dynamically invoked using `render` method on the instances of `Reporter` class. The most simple use case can look like this:

```js
reporter.render({
	template: { 
		content: "<h1>Hello world</h1>",
		recipe: "phantom-pdf"
	}
}).then(function (response) {
   //you can for example pipe it to express.js response
   response.stream.pipe(res);
});
```

You can also specify other parameters like `shortid` of existing template, javascript templating engine or input data.

```js
reporter.render({
	template: { 
		shortid: "a18dfF",		
	},
	data: {
		people: ["Jan Blaha", "John Doe"]
	}
})
```

### Attach to existing express app
If the jsreport contains `express` extension it will automatically start a default [express.js](http://expressjs.com/) based server running on ports specified in config. This behavior can be overridden with passing `express` application instance to the options. In this case jsreport `express` extension will just add required routes and middle-wares to the passed instance. 

```js
var  app = require('express')();

require("jsreport").bootstrapper({
	express : { 
		app : app
	}
}).start().then(function() {
	app.listen(5000);
});
```
This approach can be used for example in conjunction with [vhost](https://github.com/expressjs/vhost) middle-ware to start jsreport on sub domain of the existing application.

###Using rendering shortcut

It can be convenient sometimes to use jsreport shortcut `require("jsreport").render` if you want just to render a report. This happens to be usefull when you are not interested in starting jsreport server or storing data in the document store. Shortcut doesn't use configuration files but instead you can add configurations dynamicaly into `require("jsreport").renderDefaults`.

```js
var http = require('http');
var jsreport = require('jsreport');

http.createServer(function (req, res) {
  jsreport.render("<h1>Hello world</h1>").then(function(out) {
    out.stream.pipe(res);
  }).catch(function(e) {	
    res.end(e.message);
  });
  
}).listen(1337, '127.0.0.1');
```

You can find some additional information for using shortcut [here](/learn/pdf-reports-in-nodejs).


###Dynamic access to jsreport entities
jsreport is currently storing data into [mongodb](http://www.mongodb.org/) or [nedb](https://github.com/louischatriot/nedb) based on the configuration. The `Reporter` instance exposes abstraction around the data source through `documentStore` property.

```js
reporter.documentStore.collection("templates")
	.find({ name: "test"})
	.then(function(res) { 
	...
});
```

You can read more about `documentStore` in the article describing [how to write custom extension](/learn/custom-extension).

###Custom extensions
You can also install into jsreport your own custom extension. This topic is described separately [here](/learn/custom-extension).

###Custom logging
jsreport is doing logging with [winston](https://github.com/flatiron/winston) library by default. You can change this if you call `createLogger` function on bootstrap and provide your own logger.

```js
require("jsreport")
	.bootstrapper()
	.createLogger(function(bootstrapper) {
		return {
			info: function() {...},
			debug: function() {...},
			error: function() {...},
			warn: function() {...},
		}
	}
	.start();
```

Or you can use one of the default loggers. jsreport currently supports a `winston`, `console` and `dummy` logger. You can specify a logger also in the config.

```js
require("jsreport")
	.bootstrapper({
		logger: { providerName: "console" }
	})
	.start();
```

