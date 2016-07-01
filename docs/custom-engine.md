> **Sources on GitHub [jsreport-ejs](https://github.com/jsreport/jsreport-ejs)**

This tutorial shows how you can add a custom templating engine into jsreport.  Adding a custom engine is not such common as adding a custom recipe but still can become handy if you prefer to use a specific engine or have some special requirements that already supported engines doesn't fulfill. 

One of the templating engines you may like to add as a custom engine is [EJS](http://www.embeddedjs.com/). With EJS you can assemble html or xml in the quite convenient way using special tags inlining javascript.

```ejs
<ul>
<% for(var i=0; i<supplies.length; i++) {%>
   <li> <%= supplies[i] %> </li>
<% } %>
</ul>
```

This tutorial shows you how to add support for EJS into jsreport as a custom extension. Before you continue it is recommended to read general information for writing jsreport [custom extensions](/learn/custom-extension).

***

##Get started

The first thing to do is downloading and installing [extension starter kit](https://github.com/jsreport/jsreport-extension-starter-kit). As the second step you can rename the extension in  `jsreport.config.js`. 

```js
module.exports = {
  "name": "ejs",
  "main": "lib/main.js" 
}
```

##Register engine
Extension's main server javascript file has to export function which is then called by jsreport during initialization. In the function's body you should add the new EJS engine into the engines collection.

Engine is identified by its name and path to the engine script. The engine implementation needs to be separated into the dedicated file because it will run in the different process for the security reasons.

```js
//ejs.js
module.exports  = function (reporter, definition) {
   reporter.extensionsManager.engines.push({
        name: "ejs",
        pathToEngine: require("path").join(__dirname, "engine.js")
    });
};
```

##EJS rendering

Now it's time to finally implement the EJS rendering function, but first you need to install two packages the function will require.
```bash
npm install ejs --save
npm install node.extend --save
```

The rendering function is then very simple. Only thing you need to do is to merge `helpers` object and `data` object into one because EJS accepts the single context object with both functions and local attributes on it.
```js
//engine.js
var ejs = require('ejs')
var extend = require('node.extend')

module.exports = function (html, helpers, data) {
  var template = ejs.compile(html)

  return function (helpers, data) {
    var ejsMix = extend(true, helpers, data)
    delete ejsMix.filename
    return template(ejsMix)
  }
}
```

##Testing

Now you can hit `npm start` and you should be able to reach the jsreport studio on `http://localhost:5488` and test the engine. 

##Distributing

The best way to distribute the new engine into the public audience is through the [npm](https://www.npmjs.com/). To prepare the package for publishing you should change the package name, author and other attributes in `package.json` file. Afterwards you can simply type 

```bash
npm publish
```

and the package will be publicly available in npm.

##Syntax highlighting in studio
 The last optional step is to add a syntax highlighting into the jsreport studio, but fortunately, this will be easier than it looks like.

jsreport studio uses [ace editor](https://ace.c9.io/) which supports syntax highlighting for the most of the currently used languages. The only thing we need to do is pack the syntax highlighting for ejs and configure the jsreport editor when to use it.

The first lets download and install [brace](https://github.com/thlorenz/brace) package which includes the syntax highlighting sources
```sh
npm install brace --save-dev
```

Then  use `studio/main_dev.js` to reference particular brace ejs mode and `Studio.templateEditorModeResolvers` to register it.

```js
import Studio from 'jsreport-studio'
import 'brace/mode/ejs'

Studio.templateEditorModeResolvers.push((template) => template.engine === 'ejs' ? 'ejs' : null)
```

