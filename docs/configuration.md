The easiest way to adapt jsreport to your needs is to change its configuration. jsreport configuration provides many options like changing http port, setting connection string to mongo and many others.

## Configuration sources

jsreport merges configuration from config file, environment variables, command line arguments and also directly from the application code in this exact order.

### Configuration file
The configuration file is the most common way to adapt jsreport. The default `jsreport.config.json` is usually pre-created for you if you follow [installation instructions](/on-prem). 

jsreport also loads `dev.config.json` or `prod.config.json` based on the `NODE_ENV=development or production` environment variable if such file exists.

The config file can be also explicitly specified using `configFile=path` option which can be passed using one of the following methods. The config file path can be both relative or absolute.

### Environment variables
The environment variables are collected and merged into the final configuration during the jsreport startup as well. You can use it to change the port for example:

```sh
set httpPort=3000
jsreport start
```
This will start jsreport on port 3000 even if you have the `httpPort` entry in the config file because the environment variables have the higher priority. 

If you want to use environment variable for configuring a complex object you should separate the nested path in the key using `_` :

```sh
set authentication_admin_username=john
```
### Arguments
The command line arguments are parsed as well. This is very convenient for changing the port for example:
```sh
jsreport start --httpPort=3000
```
The configuration for complex objects should use the same `_` separator as described for environment variables.

### Application code

The last option is to edit the `server.js` file which is part of the default installation. This is usually common when integrating jsreport into existing node.js application. Note this approach cannot be used if you use precompiled jsreport binary.
```js
const jsreport = require('jsreport')({
  httpPort: 3000
})
```

## Configuring extensions

Each extension (recipe, store...) usually provides some options you can apply and adapt its behavior. These options can be typically set through standard configuration under the property with extension's name. For example the [authentication](/learn/authentication) can be configured under the same named node in the config.
```js
"authentication" : {     
	"admin": {
		"username" : "admin",
		"password": "password"
	}
}   
```

Please refer to particular extension's documentation to find what configuration options you have. There is usually `Configuration` section where you can find it.

### Disabling extensions

You can disable an extension by setting `enabled: false` in the configuration of particular extension.

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

## Web server configuration

**httpPort** `(number)` - http port on which is jsreport running, if both `httpPort` and `httpsPort` are specified, jsreport will automaticaly create http redirects
from http to https, if any of `httpPort` and `httpsPort` is specified default process.env.PORT will be used

**httpsPort** (number) - https port on which jsreport is running

**certificate** `object` - path to key and cert file used by https

**hostname** `(string)` - hostname to be used for the jsreport server (`optional`)

**express.inputRequestLimit** (`string`) - optional limit for incoming request size, default is `2mb`

**appPath** (`string`)  - optionally set application path, if you run application on http://appdomain.com/reporting then set `/reporting` to `appPath`

## Store configuration

**connectionString** `object` - jsreport supports multiple implementations for storing templates. The particular implementation is distinguish based on `connectionString.name` attribute. The predefined value in the precreated configuration file is `fs` which employs [jsreport-fs-store](https://github.com/jsreport/jsreport-fs-store) to store report templates on the file system.  Alternatively you can install additional extension providing template store and change `connectionString` to reflect it. You can find the list of available store drivers and further details how to configure them [here](/learn/extensions).

**blobStorage** (`string`) - optional, specifies type of storage used for storing reports. It can be `fileSystem`, `inMemory` or `gridFS`. Defaults to `fileSystem` in full jsreport or to `inMemory` when integrating jsreport into existing node.js application.

## Directories configurations

**rootDirectory** (`string`)  - optionally specifies where's the application root and where jsreport searches for extensions

**dataDirectory** (`string`) - optionally specifies absolute path to directory where the application stores images, reports and database files

**tempDirectory** (`string`) - optionally specifies absolute path to directory where the application stores temporary files

## Rendering configuration

jsreport uses by default dedicated processes for rendering pdf or scripts. This solution works better in some cloud and corporate environments with proxies. However for other cases it is better to reuse phantomjs and nodejs workers over multiple requests. This can be achieved using this config options. 

```js
"phantom": {     
    "strategy": "phantom-server"
},
"tasks": {       
    "strategy": "http-server"
}
```

## Templating engines configuration

**tasks** (`object`) - this attribute is `optional` and is used to configure the component that executes rendering tasks. This component is used to execute javascript templating engines during rendering or in scripts extension.

**tasks.strategy** (`dedicated-process | http-server | in-process`) - The first strategy uses a new nodejs instance for every task.  The second strategy reuses every instance over multiple requests. Where `http-server` has better performance, the default `dedicated-process` is more suitable to some cloud and corporate environments with proxies.  The last `in-process` strategy simply runs the scripts and helpers inside the same process. This is the fastest, but it is **not safe** to use this strategy whit users' templates which can have potentially endless loops or other critical errors which could terminate the application.

**tasks.numberOfWorkers** (`number`) - how many child nodejs instances will be used for task execution

**tasks.timeout** (`number`) -  specify default timeout in ms for one task execution

**tasks.host** (`string`) - Set a custom hostname on which script execution server is started, useful is cloud environments where you need to set specific IP.

**tasks.portLeftBoundary** (`number`) - set a specific port range for script execution server

**tasks.portRightBoundary** (`number`) - set a specific port range for script execution server

**tasks.allowedModules** (`array`) - set the allowed external modules that can be used (imported with `require`) inside helpers of template engines. Ex: `allowedModules: ["lodash", "request"]`, alternatively you can enable importing any external module using `allowedModules: "*"`. If instead of helpers you want to control the allowed modules for scripts then check the corresponding [docs](https://jsreport.net/learn/scripts#use-external-modules)

## Logging configuration

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

## Studio configuration

**entityTreeOrder** `string array` - this `optional` attribute will let you customize the order in which entity sets are shown in studio's entity tree, items in the array should be valid entity sets names, and its ordering will reflect the order of sets in studio's entity tree.


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
