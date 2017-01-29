> *Note this application was designed mainly for software developers audience*
 
jsreport application for SharePoint integrates both platforms together. Where SharePoint provides access to the company data and jsreport acts as a stateless service providing report editor and report rendering processes. Everything nicely fits together so the user does not need to leave the SharePoint page when creating or editing reports. 

jsreport brings to SharePoint many reporting capabilities. Let's just mention couple of them like support for many [output formats](/learn/recipes) including pdf or excel files. Dynamic content assembling using standard [javascript templating engines](/learn/templating-engines) or growing open source community support.

## Quick start

First find jsreport application in the [office store](https://store.office.com/) and install it to the site collection where you want to store the report templates.

jsreport SharePoint app will add custom list to the site collection during installation. The list called *jsreport Templates* is used for storing report templates as well as rendering reports. Let's wait for application to be installed, refresh the page and navigate to the list.

![report templates](https://jsreport.net/img/sharepoint1.png)

You will find a sample report template prepared for you. Navigate to it's detail.

![list of templates](https://jsreport.net/img/sharepoint2.png)

You should find a *Render report* button on the display form. This button is used for generating report to the new browser tab. You can try to render sample report and you should see output pdf.

![rendering report](https://jsreport.net/img/sharepoint3.png)

To edit the report you need to navigate to the edit form first.

![edit item](https://jsreport.net/img/sharepoint4.png)

You can edit basic SharePoint properties like *Title* and *Description* in the common way. However if you want to edit report template itself you need to use *Open editor* button.

![open editor](https://jsreport.net/img/sharepoint5.png)

This should pop up jsreport editor where you can edit the report layout, preview, close it and save it using standard SharePoint buttons afterwards. Note that everything is serialized and stored back to `jsreport Templates` list so you don't need to worry about data safety.

![jsreport editor](https://jsreport.net/img/sharepoint6.png)

##Editor

Examining `Sample Report` you find html template defining report layout in the main left pane a custom javascript under the last toolbar button and pdf in the right preview pane. Every time you click `Run` jsreport processes the template and preview the output. For the `Sample Report`  it means to invoke the custom javascript which fetches input data from the SharePoint, process the html template, convert the result into pdf and preview it in the right pane.

##Recipes

jsreport is not limited to produce just pdf. It supports various output formats including Excel or csv. To specify one you need to choose the right jsreport recipe. The recipe not only defines what will be the output but also how it will be rendered. For example `phantom-pdf` recipe render pdf from html or `html-to-xlsx` render Excel xlsx table from html.

![recipes](https://jsreport.net/img/sharepoint7.png)

Every recipe has it's purpose and input format and you cannot just switch them and send for example html into xlsx recipe. This would result into a wrong xlsx file and error warning in the preview pane. In this case rather check recipe specification in the [documentation](/learn/recipes).


##Layout and engines
You can define a report layout in the middle pane of the jsreport editor. Examining `Sample Report` you find its visual representation is described using html. But the problem of html is it's a static language and you can't assemble for example a table from the input data just using plain html. For this reason jsreport supports [javascript templating engines](/learn/templating-engines). The templating engine use special tags like `{{for}}` or `{{if}}` to extend html template which is later interpreted and outputs dynamically assembled html.  This process requires next to a template also input data, but for this you need to wait for the next chapter. 

The following example shows assembling a html table using [jsrender](/learn/jsrender) templating engine.

```html
<table>
   {{for #data.value}}
   <tr>
      <td>{{:Title}}</td>
      <td>{{:ItemCount</td>
   </tr>   
   {{/for}}
</table>
```

Next to jsrender engine you can choose also [handlebars](/learn/handlebars) in the toolbar if you like. Both are very similar in the syntax however you can't just switch between engines without adapting the syntax in the main pane. 

Note that templating engines are core part of the jsreport and it's crucial to understand them. It's recommended to carefully check examples in the [documentation](/learn/templating-engines) before proceeding further. You won't use templating engines only to assemble html. Templating engines are used to assemble any other report like xml, csv or xlsx file.

##Using script to load data

A business report is typically used to visualize data and also templating engine mentioned in the previous chapter expects it. You will use [scripts extension](/learn/scripts) to fetch data from the SharePoint. This extension allows you to write custom javascript code that can for example call SharePoint API, obtain data and forward them to the report rendering process. To make it easier you can use `sharepoint-request` module inside script that will automate authentication and parsing process. Fetching data from the SharePoint then means just to specify correct [OData](http://msdn.microsoft.com/en-us/library/office/fp142385%28v=office.15%29.aspx) URL.

Examining  custom script in the `Sample Report` you find following:
```js
require("sharepoint-request")(request, "/sites/develop_apps/_api/web/lists?$top=10",
function (err, data) {
    if (err) {
        return done(err);
    }
    //take the response data and put it to the report rendering inputs
    request.data = data;
    //you need to call done because it is async
    done();
});
```
This simply gets first 10 lists from the SharePoint site and forwards them to templating engines where it's used to assemble html. Using this technique you can load almost everything you want from the SharePoint. Even join data from multiple sites together. See [SharePoint OData reference](http://msdn.microsoft.com/en-us/library/office/fp142385%28v=office.15%29.aspx) for  supported url formats and further examples.

##Further study

You can use standard jsreport documentation for further study because SharePoint integration provides just different UI but the the main concept is the same. You can find examples or design your reports online for free without any further installation in the jsreport [playground](/playground). You may find also interesting [youtube quick start](https://www.youtube.com/watch?v=L7MZqwDCxP8) or official [documentation](/learn).


##Conclusion
jsreport should solve the common pain during defining report. It allows to define any report you can think of just using javascript and templating engines. The most standard report format like pdf is very well supported and designing it using html is very effective.

Every report template is stored inside common list. This means you don't have to be afraid about data safety. You can also easily define permission rules over `jsreport Templates` list to restrict user groups generating reports. The main report rendering runs in the scope of current user which means it does not corrupt any access rules you may have.




