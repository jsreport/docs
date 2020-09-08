

> Run custom javascript to modify report inputs/outputs or to send a report

## Basics

Define global functions `beforeRender`  or (and) `afterRender` in script and use parameters  `req` and `res` to reach your needs. script functions expect parameters to be `req, res, done` or `req, res`.

```js
async function beforeRender(req, res) {
  // merge in some values for later use in engine
  // and preserve other values which are already in
  req.data = Object.assign({}, req.data, {foo: "foo"})
  req.data.computedValue = await someAsyncComputation()
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
      "allowedModules": ["axios"]
    }
  }
}
```

Alternatively you can enable all modules using `extensions.scripts.allowedModules="*"` or using config `allowLocalFilesAccess=true`

The following example uses popular [axios](https://github.com/axios/axios) module to make a rest call and [nodemailer](https://github.com/nodemailer/nodemailer) to email the output service.

```js
//load some data from the remote API
const axios = require('axios')
async function beforeRender(req, res) {
    const r = await axios.get('http://localhost:5488/odata/templates')    
    req.data = { ...req.data, ...r.data}
}

//send the pdf report by mail
const nodemailer = require('nodemailer')
async function afterRender(req, res) { 
    const smtpTransport = nodemailer.createTransport({...})

    const mail = {
        from: '..>',
        to: '...',
        subject: '...',
        text: '...',       
        attachments: [
        {  
            filename: 'Report.pdf',
            content: new Buffer(res.content)
        }],
    }

    try {
        await smtpTransport.sendMail(mail)
    } finally {
        smtpTransport.close()
    }
}
```

## request, response
* `req.template` - modify report template, mostly `content` and `helpers` attributes
* `req.data` - json object used to modify report input data
* `req.context.http` - object which contain information about input http headers, query params, etc. (this is opt-in and you need to enable it using config `extensions.express.exposeHttpHeaders=true`)
* `res.content` - buffer with output report
* `res.meta.headers` - output headers

## Multiple scripts
You can associate multiple scripts to the report template. The scripts are then serially executed one after one in the order specified in the jsreport studio. The `req` and `res` parameters are properly passed through the scripts chain. This means you can use for example `req.data` to pass information between scripts.

## Global scripts
You can set up a script to run for every single template by adding flag `isGlobal`. This can be done for example in jsreport studio script's properties. Global script can be useful for common tasks like adding common helpers or settings to templates.

## Environment variables and configuration
You can reach environment variables using node.js [process module](https://nodejs.org/api/process.html).

```js
const process = require('process')
function beforeRender(req, res) {
   const myEnv = process.env.MY_ENV
}
```

You can also read your config file if you prefer it.

```js
const path = require('path')  
const promisify= require('util').promisify
const readFileAsync = promisify(require('fs').readFile)

async function beforeRender(req, res)  {  
    const configPath = path.join(__appDirectory,  'myConfig.json')
    const config = (await readFileAsync(configPath)).toString()
    console.log(config)
}
```

## Rendering another template from script

Script can invoke rendering of another template. To do this you need to `require` special module `jsreport-proxy` and call `render` function on it.

```js
const jsreport = require('jsreport-proxy')

async function beforeRender(req, res) {
  console.log('starting rendering from script')
  const result = await jsreport.render({ template: { shortid: 'xxxxxx' } })  
  console.log('finished rendering with ' + result.content.toString())
}
```

## Query an entity from script

Script can query the jsreport store and load an asset with config for example.

```js
const jsreport = require('jsreport-proxy')
async function beforeRender(req, res) {
  const assets = await jsreport.documentStore.collection('assets').find({name: 'myConfig'})
  const config = JSON.parse(assets[0].content.toString())
  req.data.config = config
}
```

## Logging

The `console` calls are propagated to the debug calls from the studio as well to the standard jsreport log.

```js
function beforeRender(req, res) {
  console.log('i\'m generating logs with debug level')
  console.warn('i\'m generating logs with warn level')
  console.error('i\'m generating logs with error level')  
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
POST: { template: { name: 'My Template with script' }, data: { } }
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
