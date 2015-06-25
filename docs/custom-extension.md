##Introduction
jsreport is built with extensibility in mind since the very start. Whole platform is build from many independent pieces called **extensions** which can be plugged or unplugged as needed. There is for example an extension allowing to store report templates or an extension for running custom scripts. The extensions are written same as the whole platform in javascript. This article describes how you can write and plug in your custom extension. Such an extension can be then easily distributed through npm and help the community with solving common tasks.

> **[How to write custom a engine](/learn/custom-engine)**

> **[How to write custom a recipe](/learn/custom-recipe)**

##jsreport.config.js
A jsreport extension can be stored anywhere in the application directory tree, but needs to contain specific file `jsreport.config.js` in its root directory. This file defines the basic extension attributes like its name, dependencies or main entry point.

```js
module.exports = {
  "name": "myCustomExtension",
  "main": "lib/main.js",
  "dependencies": [ "templates", "data", "scripts" ]
}
```
An extension needs to contain the server side code identified with the `main` attribute as the relative path to the javascript file. The client side code doesn't require any configuration in `jsreport.config.js`. The listed dependencies  currently only guaranties the order in which are extensions initialized.

##Server side

Extension's server side main entry point needs to export a function which is called during the initialization process. This function can accept two parameters. The first is an instance of `Reporter` which is kind of a facade covering whole jsreport. The second parameter is a plain object containing attributes from `jsreport.config.js` merged with options provided to the jsreport in the attribute with the same name as the extension.  

```js
module.exports = function (reporter, definition) {
    console.log("My custom extension is initialized.").
};
```

###Listeners and promises
jsreport core provides hooks into the most of the operations through event listeners. You just need to choose the desired listener and add the hook function.  Such a function can run in the synchronous way or return a promise when running in asynchronous way.

```js
reporter.validateRenderListeners.add("myCustomListener", function(req, res) {
	 console.log("Processing template " + request.template.name);
	 throw new Error("Invalid rendering requests!");
});
```

####Reporter listeners
- `initializeListener()`- called when all extensions are initialized
- `beforeRenderListeners(req, res)` - very first in the rendering pipeline, used to load templates and parse input data
- `validateRenderListeners(req, res)` - possible to reject rendering before it starts, jsut return failed promise or exception
- `afterTemplatingEnginesExecutedListeners(req, res)` - engine like handlebars or jsrender extracted the content, the `res.result` contains `Buffer` with extracted content
- `afterRenderListeners(req, res)` - recipes are executed, `res.result` contains final buffer which will be returned as a stream back, the last change to modify the output or send it elsewhere


###Rendering pipeline
The most common scenario for  the custom extensions is to hook into the rendering process and adapt the inputs before the html is actually printed into pdf and so on.  This can be achieved by adding the function into `beforeRenderListeners`.

Quite often you want to execute your code just after the template is loaded but before the child templates are extracted. For this purpose you can use `insert` method on the listeners collection instead of `add`.  This method accept a condition as the first parameter in which you can specify more precisely

```js
reporter.beforeRenderListeners.insert({ 
		after: "templates", 
		before: "childTemplates" 
	}, "myCustomExtension", this, function(req, res) {
	req.template.content = "<p>" + req.template.content + "</p>";
});
```

###Document store
Access to the stored jsreport data is provided through abstraction `DocumentStore`. This abstraction is then routing requests to the mongodb or nedb based on the configuration.

```js
reporter.documentStore.collection("templates")
	.find({ "shortid": "foo"})
	.then(funciton(res) {
		console.log(res[0]);
	});
```

`DocumentStore` provides similar API as the mongo client. First you need to get the desired document collection and then  call obvious methods `find`, `update`, `insert` and `remove`. Note that every method returns a promise as everything else in jsreport.

Every custom extension can define new entity/document types as well as modify existing one. This is provided again through `DocumentStore` abstraction. The definitions of the types follows the [OData](http://www.odata.org/) terminology because everything is then exposed through external OData API.

The type is defined and exposed in the following way:
```js
//define the type properties first
reporter.documentStore.registerEntityType("CustomType", {
	_id: {type: "Edm.String", key: true},
    "test": {type: "Edm.String"},    
});

//expose entity set through internal API as well as external OData
reporter.documentStore.registerEntitySet("custom", 
	{ 
		entityType: "jsreport.CustomType", 
	});
``` 

To modify the existing type you just need to know its name:
```js
reporter.documentStore.model.entityTypes["TemplateType"].foo = {type: "Edm.String"};
```

####DocumentStore listeners
- `beforeFindListeners(query)`
- `beforeUpdateListeners(query, update)`
- `beforeInsertListeners(doc)`
- `beforeRemoveListeners(query)`

##Client side
Every extension can contain its browser client part. This can get little bit more complicated but it is possible to create even a complex use-cases with some copy paste and learning from the current extensions.

###main.js
The main entry point to the extension client side is through `main.js` which needs to be stored at the specific location `[custom-extension]\public\js\main.js`. In fact jsreport studio in the development mode tries to load first `main_dev.js`, but this is because of performance and you can stick with just `main.js`. 

jsreport studio uses [requirejs](http://requirejs.org/) to load scripts and therefore `main.js` needs to contain module definition. The simplest example can look the following way

```js
define(["app"],  function (app) {
	console.log("Hello from custom extension. ");
});
```

The extension typically requires the `app` module which is kind of the `reporter` instance but on the client side. The `app` instance is the application instance from [marionette](http://marionettejs.com/) what is a framework based on [backbone](http://backbonejs.org/). 

###Models, views, templates
You will find that the most of the jsreport studio is based on marionette, backbone and [jsrender](https://github.com/borismoore/jsrender) templating engine. This means the client side application is spitted into models, views and templates.

####Models

The models are responsible for loading data through jsreport API. 

```js
define(["app", "core/basicModel"], function(app, ModelBase) {
    return ModelBase.extend({
        odata: "custom",
        url: "odata/custom"          
    });
});
```

####Views

The views are in fact controllers from the MVC concept as I understand it. View specifies visual representation using html template and also binds to the event fired by the user.

```js
define(["app", "core/view.base"], function(app, ViewBase) {
	return ViewBase.extend({        
        template: "custom-view-template"
    });
});
```

####Templates
Templates are html files stored in fixed location `custom-extension\public\templates\*.html`. Templates are using jsrender to dynamically represent model state.

```html
<input type="text" value="{{:name}}" name="name"></input>
```

####Together
Now you need to require view and model and show it the marionette region. This does the magic rendering into the browser and provides you two-way binding between model and view template.

```js
define(["app", "./custom.view.js", "./custom.model.js"],  function (app, View, Model) {
	var model = new Model();
	var view = new View({ model: model});
	app.content.show(view);
	model.fetch();
});
```

jsreport client API is not that stable to be completely documented yet. This was just description of the basic terms and technologies. To get deeper knowledge of the client side take look for example on the [resources extension](https://github.com/jsreport/jsreport/tree/master/extension/resources) and try to modify it to your purpose.



##Distributing extension
The preferred way of distributing a custom extension is through public [npm](https://www.npmjs.com/). This is very convenient because jsreport searches for extensions in the whole application directory including `node_modules` directory where `npm` modules are dropped. 

When the custom extension is published in `npm` user just need to type single command or specify dependency in the `package.json`.

>npm install jsreport-custom-extension

##Further study
This is article is far away from being a full reference to creating custom extension. It's rather a quick start to the basic architecture. If you are interested in creating advanced extensions check out the source code of the existing ones. There are 25 extensions available on [github](https://github.com/jsreport/jsreport/tree/master/extension) right now to give you examples you need.

> **[How to write a custom engine](/learn/custom-engine)**

> **[How to write a custom recipe](/learn/custom-recipe)**