

> Command line interface for jsreport

## Basics

`cli` extension will give you a command line interface that will let you execute some tasks, known as commands. These commands will mainly help you to quickly start server or invoke report rendering right from the command line, but the feature list served by the `cli` is much longer.

The `cli` is also integrated directly into the jsreport distributed in the single file executable form. Please see also its [documentation](/learn/single-file-executable) if this distribution is convenient for you.

## Installation

We recommend installing `cli` extension globally first, doing this will give you a global `jsreport` command available everywhere in your command line.

```sh
npm install jsreport-cli -g
jsreport --help
```

Please follow to [node.js project integration section](#node-js-project-integration) if you are installing `cli` into an existing node.js application, because this requires some additional steps.

## Usage

### Starting jsreport
As mentioned in the [download](/on-prem) page, the first use-case you touch `cli` is usually the jsreport installation and server startup.
```sh
jsreport init
jsreport start --httpPort=6000
```

### Rendering
The main functionality provided by `cli` is the report rendering invocation which is implemented in the [render](#-render-) command. This command has many variations and switches however the main usage can look like this:
```sh
jsreport render
    --template.name=MyTemplate
    --data=mydata.json
    --out=myreport.xlsx
```

### Remote jsreport
The commands like `render` and others can target also remote jsreport instance. This can be done using `serverUrl` switch. You can also optionally specify authentication.

```
jsreport render --serverUrl=http://jsreport.com --user=admin --password=password
```


## Commands

To get help about a particular command, you can type `jsreport <command> -h` to get some instructions about usage and available options for a command.

The following are the supported commands.

* [`help`](#help)
* [`init`](#-init-)
* [`repair`](#-repair-)
* [`configure`](#-configure-)
* [`win-install`](#-win-install-)
* [`win-uninstall`](#-win-uninstall-)
* [`start`](#-start-)
* [`render`](#-render-)
* [`kill`](#-kill-)

### `help`

`Command available globally`

Prints information about a command or a specific topic, you can run "jsreport help -h" to get the list of available topics

### `init`

`Command available globally`

Initializes the current working directory to start a jsreport application (creates server.js, jsreport.config.json, package.json and install jsreport if necessary).

To see available options and usage example type `jsreport init -h`.

### `repair`

`Command available globally`

Repairs current working directory to start a jsreport application (overrides server.js, jsreport.config.json and package.json if any of them exists and install jsreport if necessary).

To see available options and usage example type `jsreport repair -h`.

### `configure`

`Command available globally`

Generates a jsreport configuration file (jsreport.config.json) based on some questions.

To see available options and usage example type `jsreport configure -h`.

### `win-install`

`Command only available when local version is installed in your project`

Install your project as a windows service (WINDOWS ONLY)

> To install the service in production mode make sure to have the `NODE_ENV` env var set to `production` before running the command

To see available options and usage example type `jsreport win-install -h`.

The installed service wraps jsreport using [nssm](https://nssm.cc/) which adds auto-restarting logic. The [nssm](https://nssm.cc/) additionally writes unexpected errors to the windows event logs where you can try to search if you wan't find relevant info in the standard jsreport logs.

### `win-uninstall`

`Command only available when local version is installed in your project`

Stop and uninstall your project as windows service (WINDOWS ONLY)

To see available options and usage example type `jsreport win-uninstall -h`.

### `start`

`Command only available when local version is installed in your project`

Starts a jsreport process/server present in current working directory.

To see available options and usage example type `jsreport start -h`.

### `render`

`Command only available when local version is installed in your project`

Invoke a rendering process.

With this command you can render pdf, excel, etc directly from your command line, also if you are planning to invoke multiple renders we recommend the usage of `--keepAlive` option that will start jsreport in background (daemon process) and reuses the same process for future renders (the best performance).

To see available options and usage example type `jsreport render -h`.

### `kill`

`Command only available when local version is installed in your project`

Kill a daemon jsreport process.

Use this command to kill any jsreport process running in background (for example a jsreport process created by `jsreport render --keepAlive ...`)

To see available options and usage example type `jsreport kill -h`.

## node.js project integration
The jsreport `cli` is by default available in full form if you follow the official jsreport [download](/on-prem) instructions. However if you are integrating jsreport into a node.js application, you need to set up the following:

1. **Declare a jsreport entry point in the `package.json` of your project**

  A jsreport entry point is the file where you require and create a jsreport instance, usually the `server.js` file. You will need to add a `jsreport.entryPoint` field in `package.json` with the path to your project's jsreport entry point.

  Example of a `package.json` configured correctly, where `server.js` is the jsreport entry point of your proyect:

  ```json
  {
    "name": "jsreport-server",
    "dependencies": {
      "jsreport": "2.0.0"
    },
    "main": "server.js",
    "jsreport": {
      "entryPoint": "server.js"
    }
  }
  ```

2. **Export a jsreport instance in the jsreport entry point of your project**

  In your jsreport entry point file (usually `server.js`) you will need to export the jsreport instance of your project, but since you probably were using the same file to start the jsreport server (with `node server.js`) you will need to add a special condition to support both cases (server initialization and exporting the jsreport instance).

  Example of a jsreport entry point supporting both cases:

  ```js
  // creating a jsreport instance
  const jsreport = require('jsreport')()

  if (process.env.JSREPORT_CLI) {
    // when the file is required by jsreport-cli, export
    // jsreport instance to make it possible the usage of jsreport-cli
    module.exports = jsreport
  } else {
    // when the file is started with node.js, start the jsreport server normally
    jsreport.init().then(() => {
      console.log('server started..')
    }).catch((e) => {
      // error during startup
      console.error(e.stack)
      process.exit(1)
    })
  }
``` 