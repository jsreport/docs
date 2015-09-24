<a class="button large bg-emerald bg-hover-gray fg-white" id="createFlatWindow">Quick Start Video<i class="icon-arrow-right-3 on-right fg-white"></i></a>

jsreport is an open source reporting server running cross platforms on [node.js](http://nodejs.org/). It allows  to create various reports in various formats like [pdf](/learn/phantom-pdf), [excel](/learn/html-to-xlsx), [xml](/learn/text) or [html](/learn/html). The main idea of jsreport is to let users define reports without lame  designers but using code, mostly html and javascript templating engines. This approach gives great power and flexibility to the software developers and let them use the knowledge they already have. 

jsreport is fully capable reporting server including advanced features like scheduling, sending reports in emails, authentication, authorization, sub reports, report designer, API, embedded widgets and many other features. It is also greatly extensible what proves growing list of extensions.

This article shows you the most simple use case with jsreport which is creating a pdf invoice and covers basic principles you need to understand to get started.

##Basic workflow

1. get jsreport server
2. define sample input data
3. define report template
4. test and preview report
5. send real input data into API and get back report

##Getting jsreport server
First you need [download and install](/on-prem) jsreport into your environment or register for [jsreport as a service solution](/online).

No matter what option you choose you get access to jsreport html based studio you're going to need for designing and testing reports in this tutorial.

<a href="http://jsreport.net/screenshots/studio.png?v=1" target="_blank">
<img src="http://jsreport.net/screenshots/studio.png?v=1" alt="studio" style="width: 600px;"/>
</a>


##Defining sample data

Before you actually create a pdf invoice layout you should prepare some sample data you will later use to preview it. You can create sample data in jsreport studio using `ACTIONS/Create Data` menu. The sample data needs to be defined in `json` format and for our super simple invoice it can contain the following:
```js
{
	"to": "Pavel Sladek",
	"from": "Jan Blaha",
    "price": 500
}
```

##Defining report template

Report template is together with the rendering process the heart of jsreport. Template defines how the result report is going to look like and is used together with input data every time you render a new report. You can create report template in jsreport studio using `ACTIONS/Create Template` menu. This action will open jsreport designer you will use to define and test the template as well. 

For the invoice tutorial the first thing you need to do is associate sample data you previously defined with the newly created template.

![invoice-data](http://jsreport.net/screenshots/invoice-data.png)

### Recipes

As it was mentioned in the introduction, jsreport supports various reports and various output formats. To specify output format you choose jsreport [recipe](/learn/recipes). Recipe doesn't only define the output format but also how it is produced. You can for example define html template and then just with changing recipe decide if the output should be a pdf, html or excel. Or you don't have to stick with html and use some advanced recipes to create pdf using Apache FOP or excel file from Open XML.

The easiest way to create a pdf invoice is to use html to pdf conversion provided by [phantom-pdf](/learn/phantom-pdf) recipe, so lets switch to that.

### Templating engines

Templates are defined using common javascript [templating engines](/learn/templating-engines) like [jsrender](/learn/jsrender) or [handlebars](/learn/handlebars). Templating engines lets you assemble reports dynamically using loops, conditions, javascript helpers or data binding. Templating engines basically provides you a way to define any custom report you like.

Every template can use different templating engine and it's up to you which you will choose. They have very similar feature scope and only use different syntax, but every one has different preferences.

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

You can see it is just html with handlebars binding to the sample data you previously created. Now you can click the `Run` button in the jsreport designer and it should preview a pdf in the right pane. This is because you previously selected `phantom-pdf` which automatically converts html into pdf.

To make it a little bit more complex lets add a tax calculation to the template. To do it you will need to define a custom javascript function calculating tax from the original price. Such a function can be added to the `helpers` tab in jsreport studio:

```js
function tax(price) {
    return Math.round(price * 0.19);
}
```

Then back in the `content` tab you can use the `tax` function to print the price.
```html
<div>
    <span>tax: {{#tax price}}{{/tax}}$</span></span>
</div>
```

##Test and preview report
Now it's time to finish the invoice and preview it from the sample data until you are satisfied. You can find a styled and more complex invoice [here in the playground](https://playground.jsreport.net/#/playground/l1DbOPsN5) if you are lazy.

You might find helpful several features jsreport ships out of the box in the form of extensions during this time. You can for example [upload images](/learn/images) to report or split bigger template into small [child templates](/learn/child-templates). Checkout all jsreport extensions [here](/learn/extensions).

##Use API to render report

Now you are ready to integrate your application with jsreport API. It's very simple and in the most cases you just need to do one REST call. You can even try it from your browser's REST plugin. Only one thing needed before you start is report template `shortid`. You can find it for example in jsreport studio url (`https://localhost/#extension/templates/eygi2w2axR`).

Following request will invoke rendering previously defined template with shortid `eygi2w2axR`. Response will be stream to a pdf report.
> `POST:` https://localhost/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js 
   { 
      "template": { "shortid" : "eygi2w2axR" },
      "data" : { "to": "Pavel Sladek", 	"from": "Jan Blaha",  "price": 800 }
   } 
>```


More information about jsrepotr API can be found [here](/learn/api).

##Further study

To further study I recommend to checkout jsreport [playground](/playground) which provides online set of examples demonstrating the most of the jsreport rendering capabilities. Then you can continue with the [next tutorials](/learn) and explore more advanced features.
