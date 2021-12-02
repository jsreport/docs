

`html-to-xlsx` recipe generates excel xslx files from html tables. This isn't a full html -> excel conversion but a rather pragmatic and fast way to create excel files from jsreport. The recipe reads input table and extract a couple of css style properties using a specific html engine (which defaults to chrome), and finally uses the styles to create the excel cells.

** This recipe doesn't work with nodejs 16. You need to either use nodejs 14 (or lower) or upgrade to jsreport v3. **    

## Examples

- [Basic table](https://playground.jsreport.net/w/admin/h45L49Dp)
- [Cells with data types](https://playground.jsreport.net/w/admin/BPOGdSI3)
- [Cell with line break](https://playground.jsreport.net/w/admin/JzHWDVNP)
- [Cell with format](https://playground.jsreport.net/w/admin/G~OEgI3B)
- [Cell with formula](https://playground.jsreport.net/w/admin/dsHkhCbv)
- [Merged cells](https://playground.jsreport.net/w/admin/8TGX6GW~)
- [Multiple sheets](https://playground.jsreport.net/w/admin/Ds9ngUSx)
- [Conversion JS trigger](https://playground.jsreport.net/w/admin/c5~LtyXi)
- [Insert output into xlsx template](https://playground.jsreport.net/w/admin/QiHIBqsq)

The following css properties are supported:

- `background-color` - cell background color
- `color` - cell foreground color
- `border` - all the `border-[left|right|top|bottom]-width`, `border-[left|right|top|bottom]-style`, `boder-[left|right|top|bottom]-color` will be transformed into excel cells borders.
- `text-align` - text horizontal align in the excel cell
- `vertical-align` - vertical align in the excel cell
- `width` - the excel column will get the highest width, it can be little bit inaccurate because of pixel to excel points conversion
- `height` - the excel row will get the highest height
- `font-family` - font family, defaults to `Calibri`
- `font-size` - font size, defaults to `16px`
- `font-style` - `normal`, and `italic` styles are supported
- `font-weight` - control whether the cell's text should be bold or not
- `text-decoration` - `underline` and `line-through` are supported
- `colspan`- numeric value that merges current column with columns to the right
- `rowspan` - numeric value that merges current row with rows below.
- `overflow` - the excel cell will have text wrap enabled if this is set to scoll.

## Options

- `htmlEngine` - `String` (supported values here depends on the html engines that you have available in your jsreport installation, by default just `chrome` is available but you can additionally install better performing cheerio as html engine too)
- `waitForJS` - `Boolean` whether to wait for the js trigger to be enabled before trying to read the html tables on the page or not. defaults to `false`.
- `insertToXlsxTemplate` - `Boolean` controls if the result of the html to excel tables conversion should be added as new sheets of existing xlsx template, it needs that you set an xlsx template in order to work. defaults to `false`.

## Sheets

Each table detected on the html source is converted to a new sheet in the final xlsx file. The sheets names are by default  `Sheet1`, `Sheet2` etc. However, you can specify a custom sheet name using the `name` or `data-sheet-name` attribute on the `table` element where the `data-sheet-name` has precedence.

```html
<table name="Data1">
    <tr>
        <td>1</td>
    </tr>
</table>
<table data-sheet-name="Data2">
    <tr>
        <td>2</td>      
    </tr>
</table>
```

## Cells with data types

To produce a cell with specific data type you need to use the `data-cell-type` on the `td` element. The supported data types are `number`, `boolean`, `date`, `datetime` and `formula` (which will be explained in next sections)

```html
<table>
    <tr>
        <td data-cell-type="number">10</td>
        <td data-cell-type="boolean" style="width: 85px">1</td>
        <td data-cell-type="date">2019-01-22</td>
        <td data-cell-type="datetime">2019-01-22T17:31:36.000-05:00</td>
    </tr>
</table>
```

## Format

Excel supports setting cell string format. This can be done using `data-cell-format-str` (to specify the raw string format) or `data-cell-format-enum` (to select an existing format) on the `td` element.

Possible values of the `data-cell-format-enum` are:

- `0` -> format equal to `general`
- `1` -> format equal to `0`
- `2` -> format equal to `0.00`
- `3` -> format equal to `#,##0`
- `4` -> format equal to `#,##0.00`
- `9` -> format equal to `0%`
- `10` -> format equal to `0.00%`
- `11` -> format equal to `0.00e+00`
- `12` -> format equal to `# ?/?`
- `13` -> format equal to `# ??/??`
- `14` -> format equal to `mm-dd-yy`
- `15` -> format equal to `d-mmm-yy`
- `16` -> format equal to `d-mmm`
- `17` -> format equal to `mmm-yy`
- `18` -> format equal to `h:mm am/pm`
- `19` -> format equal to `h:mm:ss am/pm`
- `20` -> format equal to `h:mm`
- `21` -> format equal to `h:mm:ss`
- `22` -> format equal to `m/d/yy h:mm`
- `37` -> format equal to `#,##0 ;(#,##0)`
- `38` -> format equal to `#,##0 ;[red](#,##0)`
- `39` -> format equal to `#,##0.00;(#,##0.00)`
- `40` -> format equal to `#,##0.00;[red](#,##0.00)`
- `41` -> format equal to `_(* #,##0_);_(* (#,##0);_(* "-"_);_(@_)`
- `42` -> format equal to `_("$"* #,##0_);_("$* (#,##0);_("$"* "-"_);_(@_)`
- `43` -> format equal to `_(* #,##0.00_);_(* (#,##0.00);_(* "-"??_);_(@_)`
- `44` -> format equal to `_("$"* #,##0.00_);_("$"* (#,##0.00);_("$"* "-"??_);_(@_)`
- `45` -> format equal to `mm:ss`
- `46` -> format equal to `[h]:mm:ss`
- `47` -> format equal to `mmss.0`
- `48` -> format equal to `##0.0e+0`
- `49` -> format equal to `@`

```html
<style>
    td {
        width: 60px;
        padding: 5px;
    }
</style>
<table>
    <tr>
        <td data-cell-type="number" data-cell-format-str="0.00">10</td>
        <td data-cell-type="number" data-cell-format-enum="3">100000</td>
        <td data-cell-type="date" data-cell-format-str="m/d/yyy">2019-01-22</td>
    </tr>
</table>
```

Setting the format is also required when the cell needs to have a specific format category which depends on the particular computer locale. The cell is otherwise categorized by excel as "General". 

For example using `data-cell-type="date"` makes the cell a date and you can use it in the date based calculations. However, the cell format category in excel is displayed as "General" and not the "Date". To reach this you need to edit `data-cell-format-str` to match your locale.

## Formula

A formula cell can be specified using `data-cell-type="formula"`on the `td` element.

```html
<table>
    <tr>
        <td data-cell-type="number">10</td>
        <td data-cell-type="number">10</td>
        <td data-cell-type="formula">=SUM(A1, B1)</td>
    </tr>
</table>
```

## Font family

You can use the following css styles to change the default font-family for all cells in table.

```css
td  { 
  font-family: 'Verdana'; 
  font-size: 18px; 
}
```

## Insert output into xlsx template

The table to xlsx conversion can be enough for some cases. However for more complex cases (like producing pivot tables or complex charts using excel) there is an option to insert the produced tables into an existing xlsx template (as new sheets) instead of producing a new xlsx file.

The flow is the following. Open your desktop excel application and prepare file with pivot tables and charts on the one sheet and with static data on the second. Upload the xlsx to jsreport studio and link it with your `html-to-xlsx` template generating dynamic table. Just make sure the table name matches with the data sheet name in your excel. Running the template now produces dynamic excel with charts or pivots based on the data assembled by jsreport.

[See this example to get an idea of what can be possible with this feature.](https://playground.jsreport.net/w/admin/QiHIBqsq)

## Conversion triggers

You may need to postpone conversion of tables until some javascript async tasks are processed. If this is your case set the `htmlToXlsx.waitForJS=true` in the API options or `Wait for conversion trigger` in the studio menu. Then the conversion won't start until you set `window.JSREPORT_READY_TO_START=true` inside your template's javascript.

```html
...
<script>
    // do some calculations or something async
    setTimeout(function() {
        window.JSREPORT_READY_TO_START = true; //this will start the conversion and read the existing tables on the page
    }, 500);
    ...
</script>
```

## Issues with row height being more larger than actual the content

When using `phantomjs` as engine there are cases when a row height ends with a bigger height than the actual content. This is caused by a phantomjs bug that retrieves bigger height when the content of cells have white space characters.

There are two possible workarounds if this bigger height of row is problematic for your excel file:

- use `"letter-spacing"` css property with some negative value ([demo](https://playground.jsreport.net/studio/workspace/H1c7vr8Cb/30))

```html
<!-- without "letter-spacing" row would be more larger -->
<table style="letter-spacing: -4px">
    <tr>
        <td> From Date: NA</td>
        <td> To Date: NA </td>
        <td> Search Text: NA </td>
        <td> Sort Order: NA </td>
        <td> Sort Key: NA </td>
        <td> Filter: NA </td>
    </tr>
</table>
```

- use `"line-height: 0"` with a specific `"height"` ([demo](https://playground.jsreport.net/studio/workspace/H1c7vr8Cb/31))

```html
<!-- without "line-height" and "height" row would be more larger -->
<table style="line-height: 0">
    <tr style="height: 20px">
        <td> From Date: NA</td>
        <td> To Date: NA </td>
        <td> Search Text: NA </td>
        <td> Sort Order: NA </td>
        <td> Sort Key: NA </td>
        <td> Filter: NA </td>
    </tr>
</table>
```

## Performance
The chrome engine can have performance problems when evaluating huge tables with many cells. For this cases the recipe provides additional helper which splits long table into chunks and runs evaluation in the batches. The usage looks the same as when using the handlebars `each` or jsrender `for` helpers.

```html
<table>
    {{#htmlToXlsxEachRows people}}
      <tr>
        <td>{{name}}</td>
        <td>{{address}}</td>
      </tr>
    {{/htmlToXlsxEachRows}}
</table>
```

### Cheerio html engine
Although `htmlToXlsxEachRows` helper prevents chrome from hanging, the rendering can still be too slow. This is because chrome needs to create dom for the whole table and evaluate every single cell. Fortunately, there is a better option for long tables - using custom html engine [cheerio-page-eval](https://github.com/jsreport/cheerio-page-eval).

This custom engine needs to be additionally installed because it is experimental for now.

```
npm i cheerio-page-eval
restart jsreport
```

Afterward, you can select it in the studio html to xlsx menu and start using it. This engine doesn't create dom representation like chrome so it is much better performing. However, the lack of dom also introduces some limitations.

- The cheerio engine doesn't support global css styles in the `<style>` tag. You need to use inline styles on particular cells.
- It also doesn't evaluate javascript in the `<script>` tags. The helpers and templating engines aren't limited.

`htmlToXlsxEachRows` helper works also with cheerio engine and can significantly improve rendering memory footprint on long tables.

## Preview in studio
See general documentation for office preview in studio [here](/learn/office-preview).

## API
You can specifty template the standard way using name/shortid or you can also send it fully in the API call. In case you have the excel template stored as an asset you can also reference it in the request.

```js
{  
  "template":  {  
    "recipe":  "html-to-xlsx",  
    "engine":  "handlebars",  
    "content": "<table></table>",
    "htmlToXlsx":  {  
      "templateAssetShortid":  "xxxx"  
    }  
  },  
  "data":  {}
}

```

In case you don't have the xlsx template stored as an asset you can send it directly in the API call.

```js
{  
  "template":  {  
    "recipe":  "html-to-xlsx",  
    "engine":  "handlebars",  
    "content": "<table></table>",
    "htmlToXlsx":  {  
      "templateAsset":  {  
        "content": "base64 encoded word file",
        "encoding":"base64"
       }
    }  
  },  
  "data":  {}
}
```