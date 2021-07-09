


The easiest way to adapt jsreport to your needs is to change its configuration. jsreport configuration provides many options like changing an HTTP port, setting store provider to a different mechanism, and many others.

> `Hint:` You can get the list of supported configuration options using command<br> 
```
jsreport help config
```

## Configuration sources

jsreport merges configuration from the config file, environment variables, the command line arguments, and also directly from the application code in this exact order.

### Configuration file
The configuration file is the most common way to adapt jsreport. The default `jsreport.config.json` is usually pre-created for you if you follow [installation instructions](/on-prem).
The config file can be explicitly specified using `configFile=path` with both relative or absolute paths. This means you can have different config files for different environments and distinguish using the `configFile` environment variable which one should be used.

`Hint:` You should see the currently applied configuration file name in the first lines of the startup logs.
```
info: Initializing jsreport (version: 2.11.0, mode: production, configuration file: jsreport.config.json, nodejs: 14.17.0)
```

### Environment variables
The environment variables are collected and merged into the final configuration during the jsreport startup as well. You can use it, for example, to change the port:

Unix:

```sh
httpPort=3000 jsreport start
```

Windows:

```sh
set httpPort=3000&&jsreport start
```

This will start jsreport on port 3000 even if you have the `httpPort` entry in the config file because the environment variables have the higher priority.


If you want to use an environment variable for configuring a complex object you should separate the nested path in the key using `_` (or `:`):

Unix:

```sh
extensions_authentication_admin_username=john jsreport start
```

Windows:

```sh
set extensions_authentication_admin_username=john
jsreport start
```

Many systems like Kubernetes don't like `-` in the environment variables. Therefore jsreport supports using camel casing instead of the `-` in the configuration environment variables.
In other words the following json configuration:
```js
"extensions": {
  "fs-store": {
    "dataDirectory": "/mydata"
  }
}
```
Can be transformed into environment variable config this way:
```
extensions_fsStore_dataDirectory=/myData
```


### Arguments
The command-line arguments are parsed as well. This is very convenient for changing the port for example:

```sh
jsreport start --httpPort=3000
```

The configuration for complex objects should use the `.`  as a separator

```sh
jsreport start --extensions.authentication.admin.username=john
```

### Application code

The last option is to edit the `server.js` file which is part of the default installation. This is usually common when integrating jsreport into the existing node.js application. Note this approach cannot be used if you use precompiled jsreport binary.
```js
const jsreport = require('jsreport')({
  httpPort: 3000
})
```

## Configuring extensions

jsreport extensions typically provide some configuration options which are nested in the config node `extensions.[extension name]`. The [authentication](/learn/authentication) can be,
for example, configured like this:

```js
"extensions": {
  "authentication": {     
    "admin": {
      "username" : "admin",
      "password": "password"
    }
  }
}   
```


Please refer to the particular extension's documentation to find what configuration options you have. There is usually a `Configuration` section where you can find it.

### Disabling extensions

You can disable an extension by setting `enabled: false` in the configuration of a particular extension. You can, for example, disable jsreport studio, scheduling, and authentication using this config.

```js
{
  "extensions": {    
    "authentication": {      
      "enabled": false
    },
    "studio": {    
      "enabled": false
    },
    "scheduling": {
      "enabled": false
    }
  }
}
```

## Web server configuration

**httpPort** `(number)` - http port on which is jsreport running, if both `httpPort` and `httpsPort` are specified, jsreport will automaticaly create http redirects
from http to https, if any of `httpPort` and `httpsPort` is specified default process.env.PORT will be used

**httpsPort** (number) - https port on which jsreport is running

**certificate** `object` - path to `key` and `cert` file used by https

```js
"certificate": {
  "key": "certificates/jsreport.net.key",
  "cert": "certificates/jsreport.net.cert"
}
```

or if your certificate is a `.pfx` file then you can use the `pfx` and `passphrase` options

```js
"certificate": {
  "pfx": "certificates/jsreport.net.pfx",
  "passphrase": "<if pfx file is protected specify the password here>"
}
```

**hostname** `(string)` - hostname to be used for the jsreport server (`optional`)

**extensions.express.inputRequestLimit** (`string`) - optional limit for incoming request size, default is `2mb`

**appPath** (`string`)  - optionally set application path, if you run an application on http://appdomain.com/reporting then set "/reporting" to the `appPath`. The default behavior is that it is assumed that jsreport is running behind a proxy, so you need to do URL rewrite /reporting -> / to make it work correctly, See mountOnAppPath when there is no proxy + URL rewrite involved in your setup.

**mountOnAppPath** (`boolean`) - use this option along with `appPath`. It specifies if all jsreport routes should be available with `appPath` as the prefix, therefore making `appPath` the new root URL of application

## Store configuration

**store** (`object`) - jsreport supports multiple implementations for storing templates. The particular implementation is distinguish based on `store.provider` attribute. The predefined value in the pre-created configuration file is `fs` which employs [fs store](/learn/fs-store) to store report templates on the file system.  Alternatively, you can install an additional extension providing a template store and change `store` to reflect it. You can find the list of available store drivers and further details on how to configure them [here](/learn/template-stores).

**blobStorage** (`object`) - optional, specifies type of storage used for storing [blobs](/learn/blob-storages). The particular implementation is distinguish based on `blobStorage.provider` attribute. You can find the list of available blob storage drivers and further details how to configure them [here](/learn/blob-storages)

## Directories configurations

**rootDirectory** (`string`) - optionally specifies where's the application root and where jsreport searches for extensions

**tempDirectory** (`string`) - optionally specifies the absolute or relative path to the directory where the application stores temporary files (defaults to [OS TEMP]/jsreport/)

**configFile** (`string`) - relative or absolute path to the configuration JSON file to be used

## Allow local files and local modules

**allowLocalFilesAccess** (`boolean`) - general configuration to allow user code in templates to access the local file system. This allows users to `require` custom modules in templating engines and scripts or use assets extension to reference local files. It's reasonable to enable this when the templates are developed by trustest internal developers, but not when the templates are developed by end-users.

## Report timeouts

**reportTimeout** (`number`)  - total timeout in ms for a one report rendering

**enableRequestReportTimeout** (`boolean`) - opt-in for letting rendering requests use `options.timeout` to override general `reportTimeout` configuration

## Sandbox

**sandbox** (`object`) - configure the sandboxing of user code like templating engine evaluation or evaluation of custom [jsreport script](/learn/scripts) (`optional`)

**sandbox.allowedModules** (`array`) - the array of the npm modules (imported with `require`) allowed inside sandboxed code. For example: `allowedModules: ["axios", "lodash"]`. Alternatively, you can enable importing any external module using `allowedModules: "*"`.

## Encryption configuration
jsreport core provides general functions for encrypting/decrypting sensitive data like passwords or certificates. The API for encryption is then eventually used by extensions.
The encryption is disabled when the `encryption` node is missing in the config.

**encryption.secretKey** (`string`) - must be exactly 16 chars long string    

**encryption.enabled** (`boolean`) - enable/disable encryption

## Workers

**workers** (`object`) - configure the worker threads used to process rendering requests and other heavy tasks (`optional`)

**workers.numberOfWorkers** (`number`) - number of worker threads allocated. Every worker can process only one request. This means the config `numberOfWorkers` also specifies how many reports can jsreport process in parallel. defaults to 2 (`optional`)

## Logging configuration

> Logging in jsreport is implemented using the [winston](https://github.com/winstonjs/winston) package. Many of its concepts apply the same for jsreport logging configuration.


**logger** (`object`) - optional logging configuration which defaults to:

```js
{
    "logger": {
        "console": { "transport": "console", "level": "debug" },
        "file": { "transport": "file", "level": "debug", "filename": "logs/log.txt" },
        "error": { "transport": "file", "level": "error", "filename": "logs/error.txt" }
}
```

Explanation: 
- configure an output named `"console"` sending all logs with level `debug`, and all levels with lower priority, to the console

- configure and output named `"file"` sending all logs with `debug`, and all levels with lower priority, to the file `"logs/log.txt"`

- configure and output named `"error"` sending all logs with level `error`, and all levels with lower priority, to the file `"logs/error.txt"`

Supported values for `transport`:
- `debug` -> pipes to console when environment variable `DEBUG=jsreport`
- `console` -> pipes to console, [available additional options here](https://github.com/winstonjs/winston/blob/master/docs/transports.md#console-transport)
- `file` -> pipes to file, [available options here](https://github.com/winstonjs/winston/blob/master/docs/transports.md#file-transport)
- `http` -> pipes to an http endpoint, [available options here](https://github.com/winstonjs/winston/blob/master/docs/transports.md#http-transport)

Supported values for `level`, ordered by priority:
- `silly`
- `debug`
- `verbose`
- `info`
- `warn`
- `error`

You can override all or just some part of the predefined configuration using:

```js
{
    "logger": {
        "console": {
            "level": "error" // log just error level to the console and keep the rest transport configuration
        }
    }
}
```

A custom `transport` can be specified using the `module` property. Refer to the [winston documentation](https://github.com/winstonjs/winston) to see available external transport packages.
The following example demonstrates how to use [winston-loggly](https://github.com/winstonjs/winston-loggly) transport and pipe logs to the popular cloud-based logging service.
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

- **logger.silent** (`boolean`): handy option to silence (logs will not be stored) all outputs configured. default: false


## Example of the config file

```javascript
{   
    "store": { "provider": "fs" },   
    "httpPort": 3000,
    "allowLocalFilesAccess": true,
    "blobStorage": { "provider": "fs" },
    "reportTimeout": 60000,
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
    "extensions":  {  
      "authentication"  :  {  
        "cookieSession":  {  
          "secret":  "dasd321as56d1sd5s61vdv32"  
        },  
        "admin":  {  
          "username": "admin",
          "password": "password"  
        }  
      }  
   }
}
```
