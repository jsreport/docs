The easiest way to adapt jsreport to your needs is to change its configuration. jsreport configuration provides many options like changing http port, setting connection string to mongo and many others.

jsreport merges configuration from file, environment variables, command line arguments and also directly from the application code. The configuration file needs to be stored at the root of the application with the name `prod.config.json`. There should be already pre created for you the default one.

> **Performance note**
> Since version 0.8 jsreport uses by default dedicated processes for rendering pdf or scripts.  This solution works better in some cloud and corporate environments with proxies. However for other cases it is better to reuse phantomjs and nodejs workers over multiple requests. Please see `phantom.strategy` and `tasks.strategy` for details.

## Web

**httpPort** `(number)` - http port on which is jsreport running, if both `httpPort` and `httpsPort` are specified, jsreport will automaticaly create http redirects
from http to https, if any of `httpPort` and `httpsPort` is specified default process.env.PORT will be used

**httpsPort** (number) - https port on which jsreport is running

**certificate** `object` - path to key and cert file used by https

## Basics

**connectionString** `object` - jsreport supports multiple implementations for storing templates. The particular implementation is distinguish based on `connectionString.name` attribute. The predefined value in the precreated configuration file is `fs` which employs [jsreport-fs-store](https://github.com/jsreport/jsreport-fs-store) to store report templates on the file system.  Alternatively you can install additional extension providing template store and change `connectionString` to reflect it. You can for example install [jsreport-mongodb-store](https://github.com/jsreport/jsreport-mongodb-store), change `connectionString.name` to `mongoDB` and start storing templates in mongodb.

 **extensions** `string array` - this attribute is `optional`. jsreport will load all
all extensions located under root directory if it's undefined or null. If the attribute is defined, jsreport will only load specified extensions. All specified extensions must be present somewhere in the jsreport directory. Order is not relevant because extensions are reordered by it's dependencies.

**rootDirectory** (`string`)  - optionally specifies where's the application root and where jsreport searches for extensions

**dataDirectory** (`string`) - optionally specifies absolute path to directory where the application stores images, reports and database files

**tempDirectory** (`string`) - optionally specifies absolute path to directory where the application stores temporary files

**blobStorage** (`string`) - optional, specifies type of storage used for storing reports. It can be `fileSystem`, `inMemory` or `gridFS`. Defaults to `fileSystem` in full jsreport or to `inMemory` when integrating jsreport into existing node.js application.

## Studio

**entityTreeOrder** `string array` - this `optional` attribute will let you customize the order in which entity sets are shown in studio's entity tree, items in the array should be valid entity sets names, and its ordering will reflect the order of sets in studio's entity tree.

## Phantom

**phantom** (`object`) - this attribute is `optional` and is used to configure phantomjs which is used in various recipes

**phantom.strategy** (`dedicated-process | phantom-server`) - The first strategy uses a new phantomjs instance for every task.  The second strategy reuses every instance over multiple requests. Where `phantom-server` has better performance, the default `dedicated-process` is more suitable to some cloud and corporate environments with proxies

**phantom.numberOfWorkers** (`int`) - specify how many phantomjs instances will phantom-pdf recipe use. If the value is not filled, jsreport will use number of cpus by default

**phantom.timeout** (`int`) - specify default timeout for pdf rendering using phantomjs

**phantom.allowLocalFilesAccess** (`bool`) - default is `false`. When set to true you can use local paths to get resources.

**phantom.host** (`string`) - Set a custom hostname on which phantomjs server is started, useful is cloud environments where you need to set specific IP.

**phantom.portLeftBoundary** (`number`) - set a specific port range for phantomjs server

**phantom.portRightBoundary** (`number`) - set a specific port range for phantomjs server

## Script tasks

**tasks** (`object`) - this attribute is `optional` and is used to configure component executing custom scripts. This component is use to excute javascript templating engines during rendering or in scripts extension.

**tasks.strategy** (`dedicated-process | http-server | in-process`) - The first strategy uses a new nodejs instance for every task.  The second strategy reuses every instance over multiple requests. Where `http-server` has better performance, the default `dedicated-process` is more suitable to some cloud and corporate environments with proxies.  The last `in-process` strategy simply runs the scripts and helpers inside the same process. This is the fastest, but it is **not safe** to use this strategy whit users' templates which can have potentially endless loops or other critical errors which could terminate the application.

**tasks.numberOfWorkers** (`number`) - how many child nodejs instances will be used for task execution

**tasks.timeout** (`number`) -  specify default timeout in ms for one task execution

**tasks.host** (`string`) - Set a custom hostname on which script execution server is started, useful is cloud environments where you need to set specific IP.

**tasks.portLeftBoundary** (`number`) - set a specific port range for script execution server

**tasks.portRightBoundary** (`number`) - set a specific port range for script execution server

## Advanced

**hostname** `(string)` - hostname to be used for the jsreport server (`optional`)

**ga** `object`: google analytics settings, example
`"ga": { "name" : "jsreport.net", "id" : "UA-xxxxx-2" }`

**daemon** (`true/false`) - default `false`, non windows only, jsreport will run as [daemon](https://www.npmjs.org/package/daemon) and will not block command line

**express.inputRequestLimit** (`string`) - optional limit for incoming request size, default is `2mb`

**appPath** (`string`)  - optionally set application path, if you run application on http://appdomain.com/reporting then set `/reporting` to `appPath`

**logger** (`object`) - optional, object should contain `providerName` property with values `console`, `dummy` or `winston` to specify particular logger (Default is `winston`). An optionally `logDirectory` property will specify a directory where should `winston` store logs. , It can also optionally contain a `silent` property which will silent all transports in `winston` logger.

## Disabling an extension

You can disable any installed extension using `enabled: false` option in the options of the corresponding extension. When an extension is disabled jsreport will not load the extension when initializing

```js
{
    // ..other options here..
    "authentication": {
      // disabling authentication extension
      "enabled": false
    },
    "handlebars": {
      // disabling handlebars extension
      "enabled": false
    },
    // ..other options here..
}
```

## Example of the config file

```javascript
{
    "certificate": {
        "key": "certificates/jsreport.net.key",
        "cert": "certificates/jsreport.net.cert"
    },
    "connectionString": { "name": "fs" },
    "extensions": ["excel-parser", "express", "templates", "html", "phantom-pdf", "scripts", "data", "images", "statistics", "reports", "childTemplates", "sample-template"],
    "httpPort": 3000,
	"blobStorage": "fileSystem",
	"phantom": {
        "numberOfWorkers" : 2,
        "timeout": 180000
    },
    "tasks": {
        "numberOfWorkers" : 2,
        "timeout": 10000,
        "strategy": "http-server"
    },
    "studio": {
        "entityTreeOrder": ["templates", "data", "scripts", "assets", "images"]
    }
}
```
