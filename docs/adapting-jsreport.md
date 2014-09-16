##Configuration file
The most common way to adapt jsreport settings is using configuration file. Configuration file is stored at the root directory with name `[dev | prod].config.json`. Editing this file you can change for example port service is running on or scale up number of worker processes jsreport uses. You can find full documentation of various options [here](https://github.com/jsreport/jsreport/blob/master/config.md).

##Configuring using nodejs
In addition to configuration file based settings you can also use [node.js](http://nodejs.org) and dynamically adapt jsreport to your needs. Following chapters applies to those using jsreport on premise and want to apply some advanced configurations. Those  who wants to use jsreport rendering directly inside their node.js application should refer to [this](/learn/pdf-reports-in-nodejs) article. 

### Basic startup

Installing and initializing jsreport on premise will create `server.js` file containing following code:
```js
require('jsreport').bootstrapper().start();
```

This is the most basic jsreport server startup. It searches for `[dev | prod].config.json` file depending on the nodejs environment and initialize server based on settings it finds in the configuration file. If configuration file is not found, it will try to create default one. Same code can be also used when integrating jsreport directly to the existing nodejs application.

In addition to configuration file you can add options also to the `bootstrapper` function as a parameter. Options passed as a parameter take precedence over configuration file. In fact configuration file does not need to exist at all and you can specify all the parameter using `bootstrapper` argument. Changing http port jsreport server will listen on can be done then like this:
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
   //response.result is a stream to a pdf
   //you can for example pipe it to express.js response
   response.result.pipe(res);
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


###Dynamic access to jsreport entities
jsreport is using [jaydata](http://jaydata.org/) library as data access layer. It's a great library for abstracting access to various data sources jsreport can use like [mongodb](http://www.mongodb.org/), [nedb](https://github.com/louischatriot/nedb) or just in memory volatile storage. It also provides an [odata](http://www.odata.org/) http endpoint and libraries jsreport uses to do CRUD operations remotely from browser. You can use jaydata to dynamically query or update entities from node.js as well. You can access jaydata entity context which is kind of a unit of work using following code.

```js
reporter.dataProvider.startContext().then(function (ctx) {
	//query all jsreport templates and find one with specific shortid
	return ctx.templates.single(function (t) {  
			   return t.shortid === "a18dfF";
		   });
});
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
