
jsreport is an open-source reporting server side tool running cross-platform on [node.js](http://nodejs.org/). It can dynamically produce reports in various formats like [pdf](/learn/chrome-pdf), [excel](/learn/html-to-xlsx), [docx](/learn/docx), [pptx](/learn/pptx) and also many other [text](/learn/text) based formats. The main idea of jsreport is to let users define reports without lame designers but instead by using code, mostly html and javascript templating engines. This approach gives great power and flexibility to the software developers and let them use the knowledge they already have.

This article shows you the most simple use case with jsreport, which is creating a pdf invoice. It covers the basic principles you need to understand to get started.

## Basic workflow

1. Get jsreport
2. Define sample input data
3. Define report template
4. Test and preview report
5. Call API with real input data

## Get jsreport
First, you need to [download and install](/on-prem) jsreport on your PC, register for [jsreport as a service solution](/online), or start right away in [jsreport playground](https://playground.jsreport.net).

No matter which option you choose, you get access to the jsreport browser-based studio you're going to need for designing and testing reports in this tutorial.

![studio](https://jsreport.net/screenshots/studio.png?v=4)

## Define sample data

Before you create a pdf invoice layout, you should prepare some sample data you will later use to preview it. 

You can create sample data in the jsreport studio from the entity tree panel.<br>
Right-click the panel -> new entity -> sample data.

![studio](https://jsreport.net/img/sample-data-create.png)

The sample data needs to be defined in `json` format and for our simple invoice it can contain the following:

![sample-data](https://jsreport.net/img/invoice-sample-data.png?v2)

## Define report template

The report template is, together with the rendering process, the heart of the jsreport. A template defines what the report is going to look like and is used together with the input data every time you render a new report. You can create a report template again from the entity tree panel on the top left.<br>
Right-click the panel -> new entity -> template.

### Select sample data

For the invoice tutorial, the first thing you need to do is associate the sample data you previously defined with the newly created template.

![invoice-data](https://jsreport.net/img/invoice-data-select.png?v2)


### Recipes

The report output format is defined by the template's [recipe](/learn/recipes). The recipe is an algorithm used by jsreport to convert the output of templating engines into the desired format. Every report template needs to specify exactly one recipe from many of them available.

The easiest way to create a pdf invoice is to use html to pdf conversion implemented in [chrome-pdf](/learn/chrome-pdf) recipe, so let's stay with it. Note that not every recipe uses html conversion and it is always recommended to read its documentation in the  [recipes](/learn/recipes) section.

![recipe](https://jsreport.net/img/recipe.png?v=2)

### Templating engines

Templates are defined using common javascript [templating engines](/learn/templating-engines) like [jsrender](/learn/jsrender) or [handlebars](/learn/handlebars). Templating engines let you assemble reports dynamically using loops, conditions, javascript helpers, or data binding. Templating engines provide you with a way to define any custom report you like.

Each template can use a different templating engine, and it's up to you which you will choose. They have a very similar feature scope and only use different syntax, but everyone has different preferences.

This time lets use [handlebars](/learn/handlebars) and assemble the invoice html. In a very simple way it can look the following way:

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

You can see it is just html with handlebars binding to the sample data you previously created. Now you can click the `Run` button in the jsreport studio and it should preview a pdf in the right pane. This is because you previously selected `chrome-pdf`, which automatically converts html into pdf.

![recipe](https://jsreport.net/img/invoice-simple.png?v=2)

> Tip: You can use also the F8 shortcut to preview the template. Or you can click `Run->Run and undock preview` button to render into a new tab. Then you can move this tab to the second screen of your PC and comfortably develop on two of them.

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
<iframe src='https://playground.jsreport.net/w/anon/u47GCzJc?embed=1' width="100%" height="400" frameborder="0"></iframe>

### Assets

jsreport ships out of the box with several [extensions](/learn/extensions) you can find useful during the development. The most commonly used is the [assets extension](/learn/assets). This extension is typically used when you want to extract parts of the template like styles or scripts into separate entities and reuse them. Or when you want to upload images to jsreport and reference them in the template.

Let's add to the invoice some css styles. Start with creating an asset entity, name it for example `styles.css`<br>
Right-click the entity tree panel -> new entity -> asset.

Now you can fill some css and save it:
```css
h1 {
	color: red
}
```

> Tip: You can save the active entity using CTRL+s shortcut

The main trick comes now. You want to reference the asset inside the template. Open the invoice template and add to the top the following code.

```html
<style>
  {#asset styles.css}
</style>
```

![recipe](https://jsreport.net/img/invoice-asset.png)

The `{#asset xxx}` isn't a handlebars syntax but the syntax used by the assets extension. The extension hooks into the rendering pipeline and replaces this specific string with the actual asset content. In this case, it is just plain text but it can be also an image for example.

> Tip: You can also drag & drop any file to the entity tree and it gets automatically created as an asset entity.

## Test and preview report
Now it's time to finish the invoice and preview it from the sample data until you are satisfied. In case you want to skip it and see the result right away, you can find it [here in the playground](https://playground.jsreport.net/w/admin/hBfqC7af).



## Call API with real input data

Now you are ready to integrate your application with jsreport API. It's very simple and in most cases, you just need to do one REST call. You can even try it from your browser's REST plugin.

The following request will invoke rendering of the previously defined template and response pdf stream back:
> `POST:` https://localhost/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js
   {
      "template": { "name" : "invoice" },
      "data" : { "to": "John Lennon", "from": "George Harrison", "price": 800 }
   }
>```

More information about the jsreport API can be found [here](/learn/api).

## Further study

For further study is recommend checking out the jsreport [playground](/playground), which provides a built-in set of examples demonstrating most of the jsreport rendering capabilities. Then you can continue with the [next tutorials](/learn) and explore more advanced features.
