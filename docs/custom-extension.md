## Introduction
jsreport is built with extensibility in mind since the very start. Whole platform is build from many independent pieces called **extensions** which can be plugged or unplugged as needed. There is for example an extension allowing to store report templates or an extension for running custom scripts. The extensions are written same as the whole platform in javascript. This article describes how you can write and plug in your custom extension. Such an extension can be then easily distributed through npm and help the community with solving common tasks.

> **[Starter kit for extensions development](https://github.com/jsreport/jsreport-extension-starter-kit)**

> **[How to write a custom engine](/learn/custom-engine)**

> **[How to write a custom recipe](/learn/custom-recipe)**

## jsreport.config.js
A jsreport extension can be stored anywhere in the application directory tree, but needs to contain specific file `jsreport.config.js` in its root directory. This file defines the basic extension attributes like its name, dependencies or main entry point.

```js
module.exports = {
  "name": "myCustomExtension",
  "main": "lib/main.js",
  "dependencies": [ "templates", "data", "scripts" ]
}
```

An extension needs to contain the server side code identified with the `main` attribute as the relative path to the javascript file. The client side code doesn't require any configuration in `jsreport.config.js`. The listed dependencies  currently only guaranties the order in which are extensions initialized.

## Server side

Extension's server side main entry point needs to export a function which is called during the initialization process. This function can accept two parameters. The first is an instance of `Reporter` which is kind of a facade covering whole jsreport. The second parameter is a plain object containing attributes from `jsreport.config.js` merged with options provided to the jsreport in the attribute with the same name as the extension.  

```js
module.exports = (reporter, definition) => {
  console.log("My custom extension is initialized.").
};
```

### Listeners and promises

jsreport core provides hooks into the most of the operations through event listeners. You just need to choose the desired listener and add the hook function. Such a function can run in the synchronous way or return a promise when running in asynchronous way.

```js
reporter.validateRenderListeners.add("myCustomListener", (req, res) => {
 	console.log("Processing template " + request.template.name);
 	throw new Error("Invalid rendering requests!");
});
```

#### Reporter listeners
- `initializeListeners()`- called when all extensions are initialized
- `beforeRenderListeners(req, res)` - very first in the rendering pipeline, used to load templates and parse input data
- `renderErrorListeners(req, res, err)` - called when there is an error when trying to render a report
- `validateRenderListeners(req, res)` - possible to reject rendering before it starts, just return failed promise or exception
- `afterTemplatingEnginesExecutedListeners(req, res)` - engine like handlebars or jsrender extracted the content, the `res.content` contains `Buffer` with extracted content
- `afterRenderListeners(req, res)` - recipes are executed, `res.content` contains final buffer which will be returned as a stream back, the last change to modify the output or send it elsewhere
- `closeListeners()` called when jsreport is about to be closed, you will usually put here some code that clean up some resource that your extension is using.

### Rendering pipeline
The most common scenario for  the custom extensions is to hook into the rendering process and adapt the inputs before the html is actually printed into pdf and so on.  This can be achieved by adding the function into `beforeRenderListeners`.

Quite often you want to execute your code just after the template is loaded but before the child templates are extracted. For this purpose you can use `insert` method on the listeners collection instead of `add`.  This method accept a condition as the first parameter which specifies more precisely when the hook should be invoked

```js
reporter.beforeRenderListeners.insert({
	after: "templates",
	before: "childTemplates"
}, "myCustomExtension", this, (req, res) => {
	req.template.content = "<p>" + req.template.content + "</p>";
});
```

### Document store
Access to the stored jsreport data is provided through abstraction `DocumentStore`. This abstraction is then routing requests to the registered store implementation like [ms sql store](https://github.com/jsreport/jsreport-mssql-store) or [mongodb sore](https://github.com/jsreport/jsreport-mongodb-store).

```js
reporter.documentStore.collection("templates")
.find({ "shortid": "foo"})
.then((res) => {
	console.log(res[0]);
});
```

`DocumentStore` provides similar API as the mongo client. First you need to get the desired document collection and then  call obvious methods `find`, `update`, `insert` and `remove`. Note that every method returns a promise as everything else in jsreport.

Every custom extension can define new entity/document types as well as modify existing one. This is provided again through `DocumentStore` abstraction. The definitions of the types follows the [OData](http://www.odata.org/) terminology because everything is then exposed through external OData API.

The type is defined and exposed in the following way:
```js
//define the type properties first
reporter.documentStore.registerEntityType("CustomType", {
	"_id": { type: "Edm.String", key: true },
	"test": {type: "Edm.String"}   
});

//expose entity set through internal API as well as external OData
reporter.documentStore.registerEntitySet("custom", {
	entityType: "jsreport.CustomType"
});
```

To modify the existing type you just need to know its name:
```js
reporter.documentStore.model.entityTypes["TemplateType"].foo = {
	type: "Edm.String"
};
```

#### DocumentStore listeners
- `beforeFindListeners(query)`
- `beforeUpdateListeners(query, update)`
- `beforeInsertListeners(doc)`
- `beforeRemoveListeners(query)`

## Studio

Extensions typically includes also adaptations for the browser based jsreport studio. This topic is fully covered in dedicated article [here](https://jsreport.net/learn/extending-studio).

## Distributing extension
The preferred way of distributing a custom extension is through public [npm](https://www.npmjs.com/). This is very convenient because jsreport searches for extensions in the whole application directory including `node_modules` directory where `npm` modules are dropped.

When the custom extension is published in `npm` user just need to type single command or specify dependency in the `package.json`.

>npm install jsreport-custom-extension

## Further study
This is article is far away from being a full reference to creating custom extension. It's rather a quick start to the basic architecture. If you are interested in creating advanced extensions check out the source code of the existing ones. There are plenty of them available on [github](https://github.com/jsreport/jsreport/tree/master/extension) and could be used as  examples you need.

> **[How to write a custom engine](/learn/custom-engine)**

> **[How to write a custom recipe](/learn/custom-recipe)**
