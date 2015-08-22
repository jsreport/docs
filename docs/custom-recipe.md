 > **Sources on GitHub [jsreport-html-to-text](https://github.com/jsreport/jsreport-html-to-text)**

 This tutorial shows how you can add a custom recipe into jsreport. If you haven't already read [introduction into jsreport custom extensions](/learn/custom-extension), please do it first.

 jsreport is quite often used to render and send emails. Where the email body is usually in html format but sometimes it is required to send the body just in the plain text. In this tutorial you will create a recipe which converts html into nicely structured text which can be
then used for plain text emails.

 ***
##Node project
 
Recipes are added into jsreport through a [custom extension](/learn/custom-extension) so lets prepare one first. Extensions are written in [node](http://nodejs.org) so you should prepare a basic project for it.

![node project](http://jsreport.net/img/html-to-text.png)

 ##jsreport.config.js
 Every extension needs to contain specific file `jsreport.config.js`. In this file you define extension's name and main javascript entry point.

```js
 module.exports = {   
	"name": "html-to-text",   
	"dependencies": ["templates"],
	"main": "lib/main.js" 
}
```
##Main 
 Extension's main server javascript file has to export function which is then called by jsreport during initialization. In the function's body you should add the recipe into the collection.

Recipe is identified by its name and function for actual rendering. We won't implement the html -> text conversion by hand but rather use already existing [html-to-text](https://github.com/werk85/node-html-to-text) package.

```bash
 npm install html-to-text 
```

 Recipe execution function accepts `request` and `response` parameters. There is already a buffer with output from templating engine in `response.content` which we just convert into the text using previously installed package and we are done.

```js
var convert = require("html-to-text");

module.exports = function (reporter, definition) {
    reporter.extensionsManager.recipes.push({
        name: "html-to-text",
        execute: function(request, response) {
            response.content = new Buffer(convert.fromString(response.content.toString(), { }));
        }
    }); 
 };
```



##Testing

To test the new recipe you just need to copy it somewhere into the folder where is  jsreport installed. After restarting jsreport and opening studio you should see the `html-to-text` in the recipes combo box.

##Distributing

The easiest way to distribute the new recipe into the public audience is through the [npm](https://www.npmjs.com/). Afterwards adding new recipe into jsreport is just matter of a single command.

```bash
npm install jsreport-html-to-text
```

##Adding options
[html-to-text](https://github.com/werk85/node-html-to-text) conversion accepts several parameters like number of characters on the line. Lets attach this configuration option into the jsreport template and expose it into database as well as API.

You can reach the facade over jsreport storage as well as [odata](http://www.odata.org/) API through `reporter.documentStore`. First you use `registerComplexType` to register new custom object structure `HtmlToText` and then add it as a property into the jsreport template type. The loaded values or values from the API call can be then find on the`request.template.htmlToText.wordWrap`. The complete recipe source looks following.

```js
var convert = require("html-to-text");

module.exports = function (reporter, definition) {
    reporter.documentStore.registerComplexType("htmlToText", {
        wordWrap: {type: "Edm.Number"}
    });

    reporter.documentStore.model.entityTypes["TemplateType"].htmlToText = {type: "jsreport.htmlToText"};

    reporter.extensionsManager.recipes.push({
        name: "html-to-text",
        execute: function(request, response) {
            response.content = new Buffer(convert.fromString(response.content.toString(), {
                wordwrap: (request.template.htmlToText || {}).wordWrap || 130
            }));
        }
    });
};

```

##Adding UI
The final missing piece is to let the user to change the `wordWrap` number in the studio UI. Lets add the same experience as setting a header and other options when using `phantom-pdf` recipe.

First you need to add html template which will be displayed in the left panel menu when the `html-to-text` recipe is selected. The html template has to be stored in the folder `public/templates`. jsreport then automatically finds these templates and provides them to the client side js. The template can use jsrender templating engine with two-way binding out of the box.

```html
<a class="expandable-header"><i class="glyphicon glyphicon-sm glyphicon-file"></i>html-to-text<b class="caret caret-right"></b></a>
<div class="expandable-body properties" style="display: none">
    <span class="side-title">Word wrap</span>
    <input type="text" class="form-control area-mini" placeholder="130" name="wordWrap" value="{{:wordWrap}}">
</div>
```



jsreport studio automatically loads file `public/js/main.js` from every extension during startup. This file is the main entry to the extensions's client side. It should use [requirejs](http://requirejs.org/) to load dependencies and [marionettejs](http://marionettejs.com/) to render views and bind models. In this recipe you listen to event `template-extensions-render` and render view from previously defined html template into the provided region. For deeper understanding please check the documentation for  [marionettejs](http://marionettejs.com/). Here is the final piece of code.

```js
//public/js/main.js 
define(["jquery", "app", "marionette",
        "core/view.Base", "core/basicModel"],
    function ($, app, Marionette, ViewBase, ModelBase) {

        var TemplateView = ViewBase.extend({
            tagName: "li",
            template: "html-to-text-template",
        });

        var Model = ModelBase.extend({
            setTemplate: function (templateModel) {
                this.templateModel = templateModel;

                if (templateModel.get("htmlToText")) {
                    if (templateModel.get("htmlToText").isModel)
                        this.set(templateModel.get("htmlToText").toJSON());
                    else
                        this.set(templateModel.get("htmlToText"));
                }

                templateModel.set("htmlToText", this, {silent: true});

                this.listenTo(this, "change", function () {
                    templateModel.trigger("change");
                });
            }
        });

        app.on("template-extensions-render", function (context) {
            var view;

            function renderRecipeMenu() {
                if (context.template.get("recipe") === "html-to-text") {
                    var model = new Model();
                    model.setTemplate(context.template);
                    view = new TemplateView({model: model});

                    context.extensionsRegion.show(view, "htmlToText");
                } else {
                    if (view != null)
                        $(view.el).remove();
                }
            }

            renderRecipeMenu();

            context.template.on("change:recipe", function () {
                renderRecipeMenu();
            });
        });
    });
```

