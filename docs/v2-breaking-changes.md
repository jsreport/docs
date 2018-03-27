
jsreport v2 is major release which on top of great new features also includes several breaking changes. It is not in-place replacement for jsreport v1 and you should carefully review how the changes affect your application. To make this task easier we have prepared migration utility that analyses your jsreport application, process some automatic migration tasks and print warning with help for the rest.

You can install and run the migration utility in the following way.
```
npm install jsreport/jsreport-migration
jsreport-migration
```

The rest of the document provides the list of the breaking changes in jsreport v2. I case we were able to automate the change for you using the migration utility the chapter includes note about it.

## Node 8
The jsreport v2 requires at least node.js >= 8.6

## Configuration changes

> The migration utility should automatically convert your configuration files.

### extensions
The extensions config is now nested in extra property called `extensions`.  So for example `scripts` can be configured like this.

```js
{ 
  extensions: { 
    scripts: { timeout: 50000, strategy: 'dedicated-process'}
  }
}
```

### connectionString
The `connectionString` property was renamed to the `store` and looks the following now.
```js
{ store: { provider: 'fs' } }
```
### tasks
The `tasks` property was renamed to `templatingEngines`.
```
{ templatingEngines: { timeout: 1000, allowedModules: ['request'] } }
```

### blobStorage
The `blobStorage` property should be now object with structure as `store`.
```js
{ blobStorage: { provider: 'fs } }
```

### renderingSource
jsreport v2 introduce new config property `renderingSource`.
```js
{ renderingSource: 'trusted | untrusted' }
```
This property specifies if jsreport is running in a trusted environment. When the environment is trusted it will allow access to the local file system and use of custom nodejs modules during rendering execution.

## phantomjs
The [phantomjs](http://phantomjs.org/) project is no longer active and we decided to switch to headless chrome in the default installation. This doesn't mean you can't continue using phantomjs. You just need to additionally install it if you don't want to migrate your templates.

```
npm install jsreport-phantom-pdf --save
npm install phantomjs-prebuilt --save
```

If you want to skip chrome download completely you can set this environment variable before npm install.

```
ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD true
```


## Images
Images extension is no longer part of the standard jsreport installation and it is also not being maintained. The more powerful [assets](/learn/assets) extension takes its place.

> The migration from images to assets is automated by the migration utility

## Fop
[fop-pdf](/learn/fop-pdf) rcipe is no longer part of the standard jsreport installation, however you can easily additionally install it.

```
npm install jsreport-fop-pdf --save
```

## Scripts

### Entity
Rendering request with script strictly accepts format
```js
{ template: { scripts: [{shortid: 'xxx', content: 'xxx'}]}
```
Where each script needs to be identified by `shortid` or contain direct `content`.

The long time deprecated format `request.template.scriptId` or
`request.template.script` is no longer supported.

### Script content
The custom script hook needs to expect parameters `req, res, done` or `req, res`.

```js
function beforeRender(req, res, done) {
  done()
}
```

The long time deprecated hooks with single `done` parameter or scripts declared as code in global scope is no longer supported. Note you can now also exclude `done` the last parameter and return a value from a promise from the hook.

### Rendering from script

Using `req.reporter.render` is no longer supported. Instead you need to use:

```js
// TODO promises
const proxy = require('jsreport-proxy')
function beforeRender(req, res, done) {
  proxy.render({ template: {...}}, function (err, res) {
    done()
  })
}
```

> The migration utility validates if there are stored script in deprecated format and prints warnings.

## Public templates
The public templates supports only read public links. Let us know if you use the write links.

## jsreport commands
`--install`,`--uninstall`, `--init`, `--repair` commands are no longer available to be called using `node node_modules/jsreport --init`, use [jsreport-cli](https://github.com/jsreport/jsreport-cli) instead.

## Data
The data extension doesn't parse long time deprecated value from `req.template.data.dataJson` .

## Scheduling
Month format of cron expressions is changed from `0-11` to `1-12` to match standard cron expressions.

Day of Week now also supports specifying `7` as Sunday, format changed from `0-6` to `0-7`, where both `0` and `7` represent Sunday.

Cron expressions are now validated to contain at least 5 or 6 parts, so expressions like `* *` or `* * *` are invalid now.

Internal cron parser changed from [node-cron](https://github.com/kelektiv/node-cron) to [cron-parser](https://github.com/harrisiirak/cron-parser) package.

> The migration utility automatically convert cron expressions from old month format `0-11` to new format `1-12` (`PLANNED`)

## Logging
The logging no longer supports deprecated structure with `providerName`. The only currently supported structure is

```js
"logger": {
	"console": {
		"transport": "console",
		"level": "debug"
	},
	"file": {
		"transport": "file",
		"level": "info",
		"filename": "logs/reporter.log"
	},
	"error": {
		"transport": "file",
		"level": "error",
		"filename": "logs/error.log"
	}
},
```
> The migration utility should fix your old logger format.

## Node.js Client
`render` method now returns a promise instead of accepting a callback.

`render` no longer resolves to an instance of [node.js http response stream](https://nodejs.org/dist/latest-v8.x/docs/api/http.html#http_class_http_incomingmessage), instead it resolves to a stream with the same properties of a [node.js http response stream](https://nodejs.org/dist/latest-v8.x/docs/api/http.html#http_class_http_incomingmessage), it is identical with the same properties, just that it is no longer a real instance of [node.js http response stream](https://nodejs.org/dist/latest-v8.x/docs/api/http.html#http_class_http_incomingmessage).

