> **Sources on GitHub [jsreport-ejs](https://github.com/jsreport/jsreport-ejs)**

This tutorial shows how you can add a custom templating engine into jsreport.  Adding a custom engine is not such common as adding a custom recipe but still can become handy if you prefer to use a specific engine or have some special requirements that already supported engines doesn't fulfill. 

One of the templating engines you may like to add as a custom engine is [EJS](http://www.embeddedjs.com/). With EJS you can assemble html or xml in the quite convenient way using special tags inlining javascript.

```html
<ul>
<% for(var i=0; i<supplies.length; i++) {%>
   <li><%= supplies[i] %></li>
<% } %>
</ul>
```

This tutorial shows you how to add support for EJS into jsreport as a custom extension. Before you continue it is recommended to read general information for writing jsreport [custom extensions](/learn/custom-extension).

***

##Node project

Engines are added into jsreport through a [custom extension](/learn/custom-extension) so lets prepare one first. Extensions are written in [node](http://nodejs.org) so you should prepare a basic project for it.

![node project](http://jsreport.net/img/ejs.png)

##jsreport.config.js
Every extension needs to contain specific file `jsreport.config.js`. In this file you define extension's name and main javascript entry point.

```js
module.exports = {
  "name": "ejs",
  "main": "lib/ejs.js" 
}
```

##Main entry point
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
npm install ejs
npm install node.extend
```

The rendering function is then very simple. Only thing you need to do is to merge `helpers` object and `data` object into one because EJS accepts the single context object with both functions and local attributes on it.
```js
//engine.js
var ejs = require('ejs');
var extend = require("node.extend");

module.exports = function (content, helpers, data) {
    return ejs.render(content, extend(true, helpers, data));
};
```

##Testing in jsreport

To test the new extension you just need to copy it somewhere into the folder where is  jsreport installed. After restarting jsreport and opening studio you should see the ejs in the engines combo box.


##Distributing

The easiest way to distribute the new engine into the public audience is through the [npm](https://www.npmjs.com/). Afterwards adding EJS into jsreport is just matter of a single command.

```bash
npm install jsreport-ejs
```


