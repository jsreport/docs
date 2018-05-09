<a class="button large bg-emerald bg-hover-gray fg-white" id="createFlatWindow">Quick Start Video<i class="icon-arrow-right-3 on-right fg-white"></i></a>

jsreport is an open source reporting server running cross-platform on [node.js](http://nodejs.org/). It allows you to create various reports in various formats like [pdf](/learn/phantom-pdf), [excel](/learn/html-to-xlsx), [xml](/learn/text) or [html](/learn/html). The main idea of jsreport is to let users define reports without lame designers but instead by using code, mostly html and javascript templating engines. This approach gives great power and flexibility to the software developers and let them use the knowledge they already have.

jsreport is a fully capable reporting server including advanced features like scheduling, sending reports in emails, authentication, authorization, sub reports, report designer, API, embedded widgets, and many other features. It is also greatly extensible with a growing list of extensions.

This article shows you the most simple use case with jsreport, which is creating a pdf invoice. It covers the basic principles you need to understand to get started.

## Basic workflow

1. Get jsreport server
2. Define sample input data
3. Define report template
4. Test and preview report
5. Send real input data into API and get back report

## Getting jsreport server
First, you need to [download and install](/on-prem) jsreport into your environment, register for [jsreport as a service solution](/online), or start right away in [jsreport playground](https://playground.jsreport.net).

No matter which option you choose, you get access to the jsreport html-based studio you're going to need for designing and testing reports in this tutorial.

![studio](https://jsreport.net/screenshots/studio.png?v=3)

## Defining sample data

Before you actually create a pdf invoice layout, you should prepare some sample data you will later use to preview it. You can create sample data in jsreport studio from the entity tree panel positioned in the top left. You just need to click the plus button next to the `data` entry.  The sample data needs to be defined in `json` format and for our super simple invoice it can contain the following:
```js
{
	"to": "Pavel Sladek",
	"from": "Jan Blaha",
    "price": 500
}
```

![sample-data](https://jsreport.net/screenshots/sample-data.png)

## Defining report template

The report template is, together with the rendering process, the heart of jsreport. A template defines what the result report is going to look like and is used together with the input data every time you render a new report. You can create a report template again from the entity tree panel on the top left.

For the invoice tutorial, the first thing you need to do is associate the sample data you previously defined with the newly created template.

![invoice-data](https://jsreport.net/screenshots/invoice-data.png?v=4)

### Recipes

As it was mentioned in the introduction, jsreport supports various reports and various output formats. To specify output format you choose a jsreport [recipe](/learn/recipes). Recipe doesn't only define the output format, but also how it is produced. You can for example define an html template and then, just by changing the recipe, decide if the output should be a pdf, html or excel. Or you don't have to stick with html and use some advanced recipes to create pdf using Apache FOP or excel file from Open XML.

The easiest way to create a pdf invoice is to use html to pdf conversion provided by [chrome-pdf](/learn/chrome-pdf) recipe, so let's stay with it.

![recipe](https://jsreport.net/screenshots/recipe.png?v=2)

### Templating engines

Templates are defined using common javascript [templating engines](/learn/templating-engines) like [jsrender](/learn/jsrender) or [handlebars](/learn/handlebars). Templating engines let you assemble reports dynamically using loops, conditions, javascript helpers or data binding. Templating engines basically provide you with a way to define any custom report you like.

Each template can use a different templating engine, and it's up to you which you will choose. They have very similar feature scope and only use different syntax, but everyone has different preferences.

Now you can fill the invoice template with html and [handlebars](/learn/handlebars). In a very simple way it can look the following:

```html
<h1> Invoice </h1>
<div>
    <span>from: {{from}}</span>
</div>
<div>
    <span>to: {{to}}</span>
</div>
<div>
    <span>price: {{price}}$</span>
</div>
```

You can see it is just html with handlebars binding to the sample data you previously created. Now you can click the `Run` button in the jsreport designer and it should preview a pdf in the right pane. This is because you previously selected `phantom-pdf`, which automatically converts html into pdf.

To make it a little bit more complex, let's add a tax calculation to the template. To do so, you will need to define a custom javascript function calculating tax from the original price. Such a function can be added to the bottom panel in jsreport studio:

```js
function tax(price) {
    return Math.round(price * 0.19);
}
```

Then back in the top panel you can use the `tax` function to print the price.
```html
<div>
    <span>tax: {{#tax price}}{{/tax}}$</span></span>
</div>
```

Finally it should be like this:
<iframe src='https://playground.jsreport.net/studio/workspace/HJVhE0QP/3?embed=1' width="100%" height="400" frameborder="0"></iframe>

## Test and preview report
Now it's time to finish the invoice and preview it from the sample data until you are satisfied. You can find a styled and more complex invoice [here in the playground](https://playground.jsreport.net/studio/workspace/SyUrRILTg/9172) if you are lazy.

You might find useful several features jsreport ships out of the box in the form of extensions during this time. You can for example [upload images](/learn/images) to report or split bigger template into small [child templates](/learn/child-templates). Check out all the jsreport extensions [here](/learn/extensions).

## Use API to render report

Now you are ready to integrate your application with jsreport API. It's very simple and in the most cases you just need to do one REST call. You can even try it from your browser's REST plugin.

The following request will invoke rendering a previously defined template and response pdf stream back
> `POST:` https://localhost/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js
   {
      "template": { "name" : "invoice-template" },
      "data" : { "to": "Pavel Sladek", 	"from": "Jan Blaha",  "price": 800 }
   }
>```

More information about the jsreport API can be found [here](/learn/api).

## Further study

For further study, I recommend checking out the jsreport [playground](/playground), which provides a built-in set of examples demonstrating most of the jsreport rendering capabilities. Then you can continue with the [next tutorials](/learn) and explore more advanced features.
