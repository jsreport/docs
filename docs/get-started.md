<a class="button large bg-emerald bg-hover-gray fg-white" id="createFlatWindow">Quick Start Video<i class="icon-arrow-right-3 on-right fg-white"></i></a>

jsreport is an open source reporting server running cross platform using [node.js](http://nodejs.org/). It allows to create any pdf report you can think of without lame designers just using html and javascript. This article is kind of `Hello world` into jsreport and covers basic principles you need to understand to get started.

##Basic workflow

1. get jsreport server
2. define sample input data
3. define report template
4. test and preview report
5. send real input data into API and get back report

##Getting jsreport server
You need to have access to jsreport server to be able to render reports. You can use [jsreport as a service solution](http://jsreport.net/online) or you can [download](http://jsreport.net/on-prem) and run it to on own on premise server.

No matter what option you will choose you will get access to jsreport html based studio you're going to need for designing and testing reports.

<a href="http://jsreport.net/screenshots/studio.png" target="_blank">
<img src="http://jsreport.net/screenshots/studio.png" alt="studio" style="width: 600px;"/>
</a>


##Defining sample data
Now when you have access to [jsreport online](https://jsreportonline.net) or have your own jsreport server running you can start preparing reports. But before actually designing report's layout it's good to define some sample data you will use for previewing and testing reports first.

You can create sample data in jsreport studio using `actions/create data` menu. Whole jsreport including API is based on javascript so you guess right that sample data are based on `json` format. Sample data can in fact contain any `json` object.

<a href="http://jsreport.net/img/sample-data.png" target="_blank">
<img src="http://jsreport.net/img/sample-data.png" alt="sample data" style="width: 600px;"/>
</a>


##Defining report template

Report template is together with the rendering process the heart of jsreport. Template defines how the result report is going to look like and is used together with input data every time you render a new report. You can create report template again in jsreport studio using `actions/create template` menu. When the template is created you can immediately add sample data you previously created.

<a href="http://jsreport.net/img/sample-data-selection.png" target="_blank">
<img src="http://jsreport.net/img/sample-data-selection.png" alt="sample data selection" style="width: 150px;"/>
</a>

### Templating engines

The templates are defined using common javascript templating engines (like [jsrender](https://github.com/BorisMoore/jsrender) or [handlebars](http://handlebarsjs.com/)). You can bind input data, use loops, conditions or javascript helpers... Templating engines basically provides you a way to define any custom report you like. So for example the following code will print table of all books we defined previously.

```html
<table style='border-style:solid'>
  <tr>
    <th>Name</th>
    <th>Author</th>
    <th>Sales</th>
  </tr>
  {{#each books}}
  <tr>
    <td>{{name}}</td>
    <td>{{author}}</td>
    <td>{{sales}}</td>
  </tr>  
  {{/each}}    
</table>
</div>
```

Every template can use different templating engine. And it's up to you which you will choose. They have very similar feature scope and only use different syntax, but every one has different prefferences. You can find more informations about jsreport templating engines [here](http://jsreport.net/learn/templating-engines).

![engine specification](http://jsreport.net/screenshots/engine.png)

### Recipes

Specifying report output format is done using feature called recipes. For example setting report template recipe to phantom-pdf will make report output a pdf file printed using [phantomjs](http://phantomjs.org) screen capture feature.

![recipe specification](http://jsreport.net/screenshots/recipe.png)

You can find more informations about jsreport recipes [here](http://jsreport.net/learn/recipes).

##Test and preview report
Now it's time to finish report template and preview it from sample data until you are satisfied.

You might find helpful several features jsreport ships out of the box in the form of extensions during this time. You can for example [upload images](http://jsreport.net/learn/images) to report or split bigger template into small [child templates](http://jsreport.net/learn/child-templates). Checkout all jsreport extensions [here](http://jsreport.net/learn/extensions).

##Use API to render report

Now you are ready to integrate your application with jsreport API. It's very simple and in the most cases you just need to do one REST call. You can even try it from your browser's REST plugin. Only one thing needed before you start is report template `shortid`. You can find it for example in jsreport studio url (`https://localhost/#extension/templates/eygi2w2axR`).

Following request will invoke rendering previously defined template with shortid `eygi2w2axR`. Response will be stream to a pdf report.
> `POST:` https://localhost/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js 
   { 
      "template": { "shortid" : "eygi2w2axR" },
      "data" : { "books" : [...]},
   } 
>```


More information about jsrepotr API can be found [here](http://jsreport.net/learn/api).