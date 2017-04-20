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

## Logging

> Note: Logging in jsreport is implemented using the [winston](https://github.com/winstonjs/winston) package and many of its concepts apply the same for jsreport logging configuration

**logger** (`object`) - To have complete control about logging in jsreport you can declare output (where should logs be sent) and log level using an object:

```js
{
    "logger": {
        "console": { "transport": "console", "level": "debug" },
        "file": { "transport": "file", "level": "info", "filename": "logs/log.txt" },
        "error": { "transport": "file", "level": "error", "filename": "logs/error.txt" }
    }
}
```

For example the above config specifies the following:

- configure an output named `"console"` which sent all logs with level `debug`, and all levels with low priority than `debug` level (`"level": "debug"`) to the console (`"transport": "console"`)

- configure and output named `"file"` which sent all logs with level `info`, and all levels with low priority than `info` level (`"level": "info"`) to the file system (`"transport": "file"`) storing them at `"logs/log.txt"` (`"filename": "logs/log.txt"`)

- configure and output named `"error"` which sent all logs with level `error`, and all levels with low priority than `error` level (`"level": "error"`) to the file system (`"transport": "file"`) storing them at `"logs/error.txt"` (`"filename": "logs/error.txt"`)

Each output object specifies where to send the logs using a `transport` property and which is the level that should taken in consideration using a `level` property.

As you can see in the previous logging configuration each output object can take additional properties that let you configure the functionality of a `transport`, for example in the case of the output named "file", we are using the `filename` property to tell the file `transport` where to save the logs, each `transport` type supports a different set of properties to configure its behaviour.

Values for the `transport` property:

- `debug` -> specifies that logs should be sent to console but they only be visible when using `DEBUG=jsreport` env var
- `console` -> specifies that logs should be sent to console, [available options here](https://github.com/winstonjs/winston/blob/master/docs/transports.md#console-transport)
- `file` -> specifies that logs should be sent to the file system, [available options here](https://github.com/winstonjs/winston/blob/master/docs/transports.md#file-transport)
- `http` -> specifies that logs should be sent to and http endpoint, [available options here](https://github.com/winstonjs/winston/blob/master/docs/transports.md#http-transport)

Available log `level` ordered by priority (top ones have more priority):

- `silly`
- `debug`
- `verbose`
- `info`
- `warn`
- `error`

For advanced use cases we provide a way to configure output which can use a `transport` available from external modules using the `module` property, since logging in jsreport is implemented using the [winston](https://github.com/winstonjs/winston) package any external module that is compatible with winston transports will work in jsreport, for example to tell jsreport to use the third-party [winston-loggly](https://github.com/winstonjs/winston-loggly) transport you can create a configuration like the following:

```js
{
    "logger": {
        "loggly": {
            "module": "winston-loggly", // module should be the name of the third-party module
            "transport": "Loggly",
            "level": "info",
            // custom loggly transport options, see https://github.com/winstonjs/winston-loggly
            "subdomain": "test",
            "inputToken": "<your token here>",
            "auth": {
                "username": "<your-username>",
                "password": "<your-password>"
            }
        }
    }
}
```

**Default logger configuration in jsreport:**

```js
{
    "logger": {
        "debug": {
            "transport": "debug",
            "level": "debug"
        },
        "console": {
            "transport": "console",
            "level": "debug" // "info" in production mode
        },
        "file": {
            "transport": "file",
            "level": "debug" // "info" in production mode
        },
        "error": {
            "transport": "file",
            "level": "error"
        }
    }
}
```

Note that you can override all or just some part of the predefined configuration using:

```js
{
    "logger": {
        "console": {
            "level": "error" // now only logs with level "error" will be sent to console, the rest of predefined outputs are still configured, we are only overriding the "level" option for the predefined console output here
        }
    }
}
```

**Special options:**

- **logger.silent** (`boolean`): handy option to silence (logs will not be stored) all outputs configured. default: false

**Deprecated logger options (still present just for back compatibility with older versions, but that will be removed in next major versions):**

- **logger.providerName** (`string`): valid values are `console` (to only send logs to console), `winston` (to sends logs to console and files), `dummy` (logs are not available unless `DEBUG=jsreport` env var is set). default: `winston`

- **logger.logDirectory** (`string`): specifies the base directory where logs will be stored, only used when there is no output configured explicitly (like `"console": { "transport": "console", "level": "debug" }`). default: `your project directory (rootDirectory)/logs`

## Advanced

**hostname** `(string)` - hostname to be used for the jsreport server (`optional`)

**ga** `object`: google analytics settings, example
`"ga": { "name" : "jsreport.net", "id" : "UA-xxxxx-2" }`

**daemon** (`true/false`) - default `false`, non windows only, jsreport will run as [daemon](https://www.npmjs.org/package/daemon) and will not block command line

**express.inputRequestLimit** (`string`) - optional limit for incoming request size, default is `2mb`

**appPath** (`string`)  - optionally set application path, if you run application on http://appdomain.com/reporting then set `/reporting` to `appPath`

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
