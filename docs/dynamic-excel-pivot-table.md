> **[See the live example here](https://playground.jsreport.net/studio/workspace/HyQH-eKv/6)**

This tutorial shows how to dynamically create an excel with pivot table using [jsreport](https://jsreport.net) and its [xlsx](https://jsreport.net/learn/xlsx) recipe. 

We won't create the whole excel from scratch, much easier is to prepare a template directly in excel and then fill it with data using jsreport. Let's start with the excel part first.

##Excel template

We're going to need two sheets for the tutorial, one with the actual pivot and the second containing the raw data.

Add the second sheet, rename it to `Data` and add a simple two columns table.

![pivot data](https://jsreport.net/screenshots/pivot-data.png)

Then switch to the first sheet and create a pivot table based on the second sheet and select both `Product` and `Amount` as `Pivot Chart Fields`.

![pivot](https://jsreport.net/screenshots/pivot.png)

Now we have static pivot table. You may notice that the pivot doesn't automatically refresh if you try to add another product to the `Data` sheet. This need to be changed and we need to make the pivot data range dynamic before inserting rows from jsreport.

Dynamic data range can be specified using the new custom excel formula. Switch to `FORMULAS` ribbon tab and open `Name Manager`.

![pivot name manager](https://jsreport.net/screenshots/pivot-name-manager.png)

Now create new formula named `MyData` using `OFFSET` function
```
=OFFSET(Data!$A$1,0,0,COUNTA(Data!$A:$A),2)
```

![pivot name](https://jsreport.net/screenshots/pivot-name.png)

This formula dynamically selects all filled rows. Now we need to change pivot data source to use it. Select pivot table and find `Change Data Source` in the ribbon.

![pivot datasource](https://jsreport.net/screenshots/pivot-datasource.png)

Choose the previously created `MyData` formula as `Range`.

![pivot select datasource](https://jsreport.net/screenshots/pivot-select-datasource.png)

Now you can try to add a row to the `Data` sheet and click `Refresh All` from the ribbon. You should see the pivot is correctly refreshed.

![pivot refresh](https://jsreport.net/screenshots/pivot-refresh.png)

Additionally it's nice to have the pivot automatically refreshed after the excel is loaded. This can be enabled from the pivot context menu.

![pivot options](https://jsreport.net/screenshots/pivot-options.png)

![pivot refresh options](https://jsreport.net/screenshots/pivot-refresh-options.png)

At the end remove the rows from the `Data` sheet and keep only the header.

##Render excel in jsreport

Now you can switch to the jsreport studio and upload the previously created excel template.

![pivot studio template](https://jsreport.net/screenshots/pivot-studio-template.png)

Create also a data entity with some test data for the report

```js
{
    "food": [{
        "Name": "Cucomber",
        "Amount": 8
    },{
        "Name": "Apple",
        "Amount": 7
    }
}
```

Afterwards create a report template with [xlsx](https://jsreport.net/learn/xlsx) recipe, selected the excel template and the test data.

![pivot properties](https://jsreport.net/screenshots/pivot-properties.png?v=2)

Now it's time to define the excel report content. This gonna be quite easy because we've already prepared the excel template and the only thing to do is to iterate over the input data and add rows to the second sheet. This can be done using  [handlebars](https://jsreport.net/learn/handlebars) templating engine and helper function `xlsxAdd` provided by [xlsx](https://jsreport.net/learn/xlsx)  recipe.
```
{{#each food}}
{{#xlsxAdd "xl/worksheets/sheet2.xml" "worksheet.sheetData[0].row"}}
<row>
    <c t="inlineStr"><is><t>{{Name}}</t></is></c>
    <c><v>{{Amount}}</v></c>
</row>
{{/xlsxAdd}}
{{/each}}

{{{xlsxPrint}}}
```

##Live example to play with

<iframe src='https://playground.jsreport.net/studio/workspace/HyQH-eKv/6?embed=1' width="100%" height="400" frameborder="0"></iframe>