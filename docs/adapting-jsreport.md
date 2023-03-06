

## Configuration file
The most common way to adapt jsreport settings is using the configuration file. The configuration file is stored at the root directory with name `jsreport.config.json`. Editing this file, you can change, for example, which port service is running or scale up the number of worker processes jsreport uses. You can find full documentation of various options [here](/learn/configuration).

## Configuring using nodejs
In addition to configuration file-based settings, you can also use [node.js](http://nodejs.org) and dynamically adapt jsreport to your needs. The following chapters applies to those using jsreport on-premise and want to apply some advanced configurations.

Installing and initializing jsreport on-premise will create `server.js` file containing the following code:
```js
require('jsreport')().init();
```

This is the most basic jsreport server startup. It searches for a `jsreport.config.json` file and initializes a server based on the settings it finds in it or applies defaults. The same code can be also be used when integrating jsreport directly into the existing nodejs application.

In addition to the configuration file, jsreport also parses values from environment variables, command line arguments or you can pass them directly to the jsreport instantiation. Options passed as a parameter take precedence over environment variables which then take precedence over configuration file.

In the following example are the options passed directly to the jsreport instantiation.
```js
require('jsreport')({ httpPort: 3000, httpsPort: 0 }).init();
```

## Rendering
The goal of jsreport is to render reports. To do it programmatically, you use `jsreport.render`:

```js
const fs = require('fs').promises
const jsreport = require('jsreport')()

await jsreport.init()
const result = await jsreport.render({
	template: {
		content: '<h1>Hello {{foo}}</h1>',
		engine: 'handlebars',
		recipe: 'chrome-pdf'
	},
	data: {
		foo: "world"
	}
})
await fs.writeFile('report.pdf', resp.content)
```

## Running without web server
To render reports without starting the web server, you just need to disable `express` extension.
```js
const jsreport = require('jsreport')({
    extensions: {
        express: { enabled: false },
        studio: { enabled: false }
    }
})
await jsreport.init()
```

## Attach to existing express app
jsreport by default starts an [express.js](http://expressjs.com/)-based server running on ports specified in config. This behavior can be overridden by passing an `express` application instance to the options. In this case, jsreport `express` extension will just add required routes and middle-wares to the passed instance.

```js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hello from the main application');
});

const reportingApp = express();
app.use('/reporting', reportingApp);

const server = app.listen(3000);

const jsreport = require('jsreport')({
  extensions: {
	  express: { app: reportingApp, server: server },
  },
  appPath: "/reporting"
});

jsreport.init().then(() => {
  console.log('jsreport server started')
}).catch((e) => {
  console.error(e);
});
```

## Additional extensions
jsreport can be easily extended with additional extensions. Extensions usually add a new engine, recipe or even complex studio functionality. There are plenty of them ready to be installed like an extension allowing you to store templates in [mongodb](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-mongodb-store) or another extension storing reports in [Microsoft Azure](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-azure-storage). You can find the list of extensions in the [jsreport official documentation](/learn/extensions) or on the [github](https://github.com/jsreport/jsreport/tree/master/packages). If you didn't find what you are looking for, you can even implement your own custom extension. This topic is described separately [here](/learn/custom-extension).

## Dynamic access to jsreport entities
Once you have the jsreport running with some templates or reports created, you can reach them on the server using `documentStore` property. The API to search and manipulate underlying jsreport data is very similar to the official [mongodb driver](https://github.com/mongodb/node-mongodb-native). 

```js
reporter.documentStore
.collection('templates')
.find({ name: 'test'})
.then(function(res) {
  // use the entity results for your own purpose
	console.log(res)
});
```

You can read more about `documentStore` in the article describing [how to write custom extension](/learn/custom-extension).
