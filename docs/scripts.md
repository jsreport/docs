> Run custom javascript to modify report inputs/outputs or to send a report

## Basics

Define global functions `beforeRender`  or (and) `afterRender` in script and use parameters  `req` and `res` to reach your needs. script functions expect parameters to be `req, res, done` or `req, res`.

```js
async function beforeRender(req, res) {
  // merge in some values for later use in engine
  // and preserve other values which are already in
  req.data = Object.assign({}, req.data, {foo: "foo"})
  req.data.computedValue = await computedValueOperationThatReturnsPromise()
}
```

or

```js
function beforeRender(req, res, done) {
  // merge in some values for later use in engine
  // and preserve other values which are already in
  req.data = Object.assign({}, req.data, {foo: "foo"})
  done()
}
```

## Use external modules

You can `require` external modules in the node.js way, however you need to first enable them in the config.

```js
{
  "extensions": {
    "scripts": {
      "allowedModules": ["request"]
    }
  }
}
```

Alternatively you can enable all modules using `extensions.scripts.allowedModules="*"`.

The following example uses popular [request](https://github.com/request/request) module to make a rest call and [sendgrid](https://github.com/sendgrid/sendgrid-nodejs) service to email the output service.

```js
//load some data from the remote API
function beforeRender(req, res, done) {
    require('request')({
      url:"http://service.url",
      json:true
    }, function(err, res, body){
        req.data = Object.assign({}, req.data, body);
        done();
    });
}

//send the pdf report by mail
function afterRender(req, res, done) {
  var SendGrid = require('sendgrid');
  var sendgrid = new SendGrid('username', 'password');

  sendgrid.send({ to: '',  from: '', subject: '',
          html: 'This is your report',
          files: [ {filename: 'Report.pdf', content: new Buffer(res.content) }]
  }, function(success, message) {          
          done(success);
  });
}
```

## request, response
* `req.template` - modify report template, mostly `content` and `helpers` attributes
* `req.data` - json object used to modify report input data
* `req.context.http` - object which contain information about input http headers, query params, etc.
* `res.content` - buffer with output report
* `res.meta.headers` - output headers

## Multiple scripts
You can associate multiple scripts to the report template. The scripts are then serially executed one after one in the order specified in the jsreport studio.

## Global scripts
You can set up a script to run for every single template by adding flag `isGlobal`. This can be done for example in jsreport studio script's properties. Global script can be useful for common tasks like adding common helpers or settings to templates.

## Use custom configuration

If you have some custom configuration values stored in a file (like a json file) and want to use those value inside your scripts you can just read the file and use it in normal variables.

> You will need to allow the usage of `path`, `fs` and `process` modules in `allowedModules` option to try the following examples, see ["Use external modules"](#use-external-modules) for more info

```js
const path = require('path')
const fs = require('fs')

function beforeRender(req, res, done) {
  const configPath = path.join(__appDirectory, 'myconfig.json')

  // any message called with console.log, console.warn, console.error will be saved into logs
  console.log('reading custom file..')

  fs.readFile(configPath, (err, content) => {
    if (err) {
      return done(err)
    }

    try {
      const myconfig = JSON.parse(content.toString())

      // use config values to apply some conditional logic in the script
      if (myconfig.validateData) {
        // .. your custom logic here ..
      }

      // or pass custom config as data to your templates
      req.data.myconfig = myconfig
      done()
    } catch (e) {
      done(new Error('error while trying to parse custom config at ' + configPath + ': ' + e.message))
    }
  })
}
```

Note that you can also require the [`process`](https://nodejs.org/api/process.html) module inside your scripts to get some useful information (env vars, processor architecture, OS, etc) about the environment where jsreport is running, you can also use that information to conditionally load some custom configuration depending of the OS for example.

```js
const process = require('process')

function beforeRender(req, res, done) {
  // pass platform information to your template to active some conditional
  // content or styles
  req.data.platform = process.platform
  done()
}
```

```js
const process = require('process')

function loadConfig(configFile, cb) {
  const configPath = path.join(__appDirectory, configFile)

  fs.readFile(configPath, (err, content) => {
    const myconfig

    if (err) {
      return cb(err)
    }

    try {
      myconfig = JSON.parse(content.toString())
      cb(null, myconfig)
    } catch (e) {
      cb(new Error('error while trying to parse custom config at ' + configPath + ': ' + e.message))
    }
  })
}

function beforeRender(req, res, done) {
  const env = process.env.NODE_ENV
  const configFile = 'myconfig.development.json'

  // load some configuration based on `NODE_ENV` env var
  if (env === 'production') {
    configFile = 'myconfig.production.json'
  }

  loadConfig(configFile, (err, myconfig) => {
    if (err) {
      return done(err)
    }

    req.data.myconfig = myconfig
    done()
  })
}
```


## phantom-pdf note
Please note that some recipes like [phantom-pdf](/learn/phantom-pdf) are invoking the whole rendering process for the main page and also for headers and footers. This causes the custom script to be invoked multiple times - for main page, header and footer. To determine calls from header or footer use `req.options.isChildRequest` property.

```js
function afterRender(req, res, done) {
    //filter out script execution for phantom header
    if (req.options.isChildRequest)
      return done();

    //your script
    done();
}
```

## Rendering another template from script

Script can be used also to invoke rendering of another template, to do that you can require special `jsreport-proxy` module and use the `render` method in there.

```js
const proxy = require('jsreport-proxy')

async function beforeRender(req, res) {
  console.log('starting rendering from script')
  await proxy.render({ template: { shortid: 'xxxxxx' } })
  console.log('finished rendering')
}
```

## Logging

If you want to save some logs information that are relevant when executing your scripts, then you can use normal `console.log`, `console.warn`, `console.error` calls in your scripts code to achieve this, these methods match with the `info`, `warn`, `error` levels of your [logging configuration](https://jsreport.net/learn/configuration#logging-configuration) so how these information is going to be stored will depend on the logging configuration that you have in your [jsreport configuration source](https://jsreport.net/learn/configuration).

```js
function beforeRender(req, res, done) {
  console.log('i\'m generating logs with info level')
  console.warn('i\'m generating logs with warn level')
  console.error('i\'m generating logs with error level')
  done()
}
```

## Configuration

Add `scripts` node to the standard config file:

```js
"extensions": {
  "scripts": {
    "timeout": 30000,
    "allowedModules": "*"  
  }
}
```

## API
You can use standard OData API to manage and query script entities. For example you can query all scripts using
> `GET` http://jsreport-host/odata/scripts

A custom script is physically linked to the stored report template using its shortid or name. In this cases there is nothing to do in the API call, because the script is automatically applied.

```js
POST: { template: { name: 'My Template with script' }, data: { }
```

If you are rendering anonymous template you can identify the script by its name or shortid

```js
POST: {
  template : {
	  content: "foo",
	  scripts: [{
		  shortid: "sd543fds"		  
	  }, {
		  name: "My Script"  
	  }]  	
  }
}  
```

The last option is to specify the script content directly
```js
POST: {
  template : {
	  content: "foo",
	  scripts: [{
		  content: "function beforeRender(req, res, done) { req.template.content='hello'; done(); }"
	  }]  	
  }
}  
```


## Using script to load data
Some people prefer to push data into jsreport from the client and some people prefer to use `scripts` extension to actively fetch them. Where pushing data to the jsreport is more straight forward, using `scripts` can provide better architecture with fully separated reporting server where report itself is responsible for defining input as well as fetching them. The choice is up to you.

## Examples
- [Sending reports periodically in email article](https://jsreport.net/blog/sending-reports-periodically-in-email)
- [Downloading report data using script in playground](https://playground.jsreport.net/#/playground/lyWJuycgAc)
- [Using scripts to create template layouts](https://jsreport.net/blog/template-layouts)
