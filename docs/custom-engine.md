> **Sources on GitHub [jsreport-ejs](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-ejs)**

This tutorial shows how you can add a custom [templating engine](https://jsreport.net/learn/templating-engines) into jsreport.  Adding a custom engine is not such common as adding a custom recipe but still can become handy if you prefer to use a specific engine or have some special requirements that already supported engines doesn't fulfill.

One of the templating engines you may like to add as a custom engine is [EJS](http://www.embeddedjs.com/). With EJS you can assemble html or xml in a quite convenient way using special tags inlining javascript.

```ejs
<ul>
<% for(var i=0; i<supplies.length; i++) {%>
   <li> <%= supplies[i] %> </li>
<% } %>
</ul>
```

This tutorial shows you how to add support for EJS into jsreport as a custom extension. Before you continue it is recommended to read general information for writing jsreport [custom extensions](/learn/custom-extension).

***

## Get started

The first thing to do is downloading and installing [extension starter kit](https://github.com/jsreport/jsreport-extension-starter-kit). As the second step, you can rename the extension in  `jsreport.config.js`.

```js
module.exports = {
  "name": "ejs",
  ...  
}
```

## Register engine
Extension's main server javascript file has to export function which is then called by jsreport during initialization. In the function's body, you should add the new EJS engine into the engine's collection.

```js
//main.js
module.exports = function (reporter, definition) {
  reporter.extensionsManager.engines.push({
    name: 'ejs'
  })
}
```

The worker part, the part that for security reasons runs in a separate thread, is more complex. 
At first, you need to do two things at the worker entry point. Allow `require` for `ejs` module from the sandbox and register new engine's compilation and execution function.
```js
//worker.js
const createEngine = require('./ejsEngine')

module.exports = (reporter, definition) => {
  const { compile, execute } = createEngine()

  // allow require('ejs') in the sandbox
  reporter.options.sandbox.modules.push({
    alias: 'ejs',
    path: require.resolve('ejs')
  })

  // register templating engine compilation and execution
  reporter.extensionsManager.engines.push({
    name: 'ejs',
    compile,
    execute
  })
}
```

## EJS rendering

Now it's time to finally implement the EJS compilation and execution function, but first, you need to install two packages the function will require.
```bash
npm install ejs --save
npm install node.extend --save
```

The `compile` function is using `ejs.compile` to compile the input html. The important thing is to use `require` from the options parameter to prevent polluting the sandbox with an outside instance.
The `execute` function mixes helpers and data into the single context which accepts the `ejs` and lets it execute.

```js
//ejsEngine.js
const extend = require('node.extend')
module.exports = (opts = {}) => {
  return {
    compile: (html, { require }) => {
      const ejs = require('ejs')
      return ejs.compile(html)
    },
    execute: (templateSpec, helpers, data) => {
      const ejsMix = extend(true, helpers, data)
      delete ejsMix.filename
      return templateSpec(ejsMix)
    }
  }
}

```

## Testing

Now you can hit `npm start` and you should be able to reach the jsreport studio on `http://localhost:5488` and test the engine.

## Distributing

The best way to distribute the new engine into the public audience is through the [npm](https://www.npmjs.com/). To prepare the package for publishing you should change the package name, author, and other attributes in `package.json` file. Afterward you can simply type

```bash
npm publish
```

and the package will be publicly available in npm.

## Syntax highlighting in studio

jsreport studio uses [monaco editor](https://microsoft.github.io/monaco-editor/) for code editing. This is the same editor component you can find in the [VS Code](https://code.visualstudio.com/).
This editor is very extensible and you can also add custom code highlighting for your new engine. jsreport studio includes hooks that are designed just for that.
This topic is a bit more complex, please check the [ejs studio code](https://github.com/jsreport/jsreport/blob/master/packages/jsreport-ejs/studio/main_dev.js) for the inspiration.