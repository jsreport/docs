



`html-to-xlsx` recipe generates Excel xslx files from HTML tables. This isn't a full HTML -> Excel conversion but a rather pragmatic and fast way to create Excel files from jsreport. The recipe reads input table and extract a couple of CSS style properties using a specific HTML engine (which defaults to chrome), and finally uses the styles to create the Excel cells.

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
- [Postprocess using xlsx](https://playground.jsreport.net/w/anon/tWGz8qVs)

The following CSS properties are supported:

- `background-color` - cell background color
- `color` - cell foreground color
- `border` - all the `border-[left|right|top|bottom]-width`, `border-[left|right|top|bottom]-style`, `border-[left|right|top|bottom]-color` will be transformed into Excel cells borders.
- `transform: rotate()` - rotate text at certain angles to produce vertical text (only specific set of angles work to replicate what the xlsx format supports).
- `text-align` - text horizontal align in the Excel cell
- `vertical-align` - vertical align in the Excel cell
- `width` - the Excel column will get the highest width; it can be little bit inaccurate because of pixel to Excel points conversion
- `height` - the Excel row will get the highest height
- `font-family` - font family, defaults to `Calibri`
- `font-size` - font size, defaults to `16px`
- `font-style` - `normal`, and `italic` styles are supported
- `font-weight` - control whether the cell's text should be bold or not
- `text-decoration` - `underline` and `line-through` are supported
- `overflow` - the Excel cell will have text wrap enabled if this is set to scroll.
- `writing-mode`, `text-orientation` - use a combination of these styles to produce different styles of vertical text
- `white-space` - to control how the white space characters are treated inside the cell

The following HTML attributes are supported:
- `colspan` - numeric value that merges current column with columns to the right
- `rowspan` - numeric value that merges current row with rows below.

The following data attributes are supported:
- `data-sheet-name` on table - specify a custom sheet name for the table
- `data-cell-type`, `data-cell-format-str`, `data-cell-format-enum` on cells - see [cells with data types](#cells-with-data-types)
- `data-cell-indent` on cells - numeric value that controls the indentation level of the cell content
- `data-sheet-page-paper-size` on table - what paper size to use, supported values are `Letter`, `Legal`, `Executive`, `A3`, `A4`, `A5`, `B5-JIS`, `Envelope-10`, `Envelope-DL`, `Envelope-C5`, `Envelope-B5`, `Envelope-Monarch`, `Double-Japan-Postcard-Rotated`, `16K-197x273`
- `data-sheet-page-orientation` on table - Orientation of the page, supported values are `portrait` and `landscape`
- `data-sheet-page-print-area` on table - the Print Area for a sheet. ex: `A1:G20`, `A1:G10&&A11:G20`
- `data-sheet-page-print-titles-row` on table - Repeat specific rows on every printed page. ex: `1:3`
- `data-sheet-page-print-titles-column` on table - Repeat specific columns on every printed page. ex: `A:C`
- `data-sheet-page-margin-left` on table - Whitespace on the left border of the page. Units are inches. ex: `0.4`
- `data-sheet-page-margin-right` on table - Whitespace on the right border of the page. Units are inches. ex: `0.4`
- `data-sheet-page-margin-top` on table - Whitespace on the top border of the page. Units are inches. ex: `0.4`
- `data-sheet-page-margin-bottom` on table - Whitespace on the bottom border of the page. Units are inches. ex: `0.4`
- `data-sheet-page-margin-header` on table - Whitespace on the header of the page. Units are inches. ex: `0.4`
- `data-sheet-page-margin-footer` on table - Whitespace on the footer of the page. Units are inches. ex: `0.4`
- `data-sheet-page-scale` on table - Percentage value to increase or reduce the size of the print. ex: `70`
- `data-sheet-page-order` on table - Which order to print the pages, supported values are `downThenOver` and `overThenDown`
- `data-sheet-page-black-and-white` on table - Print without color. ex: `0` or `1`
- `data-sheet-page-draft` on table - Print with less quality (and ink). ex: `0` or `1`
- `data-sheet-page-cell-comments` on table - Where to place comments, supported values are `None`, `atEnd`, `asDisplayed`
- `data-sheet-page-errors` on table - Where to show errors, supported values are `dash`, `blank`, `NA`, `displayed`
- `data-sheet-page-show-row-col-headers` on table - Whether to show the row numbers and column letters. ex: `0` or `1`
- `data-sheet-page-show-grid-lines` on table - Whether to show grid lines. ex: `0` or `1`
- `data-sheet-page-first-page-number` on table - Which number to use for the first page. ex: `2`
- `data-sheet-page-horizontal-centered` on table - Whether to center the sheet data horizontally. ex: `0` or `1`
- `data-sheet-page-vertical-centered` on table - Whether to center the sheet data vertically. ex: `0` or `1`

> Note: When using vertical text you need to set an explicit width and height if you want a predictable result. this is needed because taking dimensions from the document (either with browser based tools or  cheerio) is unpredictable, the default dimensions when vertical text is used may change between versions.

## Options

- `htmlEngine` - `String` (supported values here depends on the HTML engines that you have available in your jsreport installation, by default just `chrome` is available but you can additionally install better performing cheerio as HTML engine too)
- `waitForJS` - `Boolean` whether to wait for the JavaScript trigger to be enabled before trying to read the HTML tables on the page or not. defaults to `false`.
- `insertToXlsxTemplate` - `Boolean` controls if the result of the HTML to Excel tables conversion should be added as new sheets of existing xlsx template, it needs you to set an xlsx template to work. Default is `false`.

## Sheets

Each table detected on the HTML source is converted to a new sheet in the final xlsx file. The sheets names are by default `Sheet1`, `Sheet2` etc. However, you can specify a custom sheet name using the `name` or `data-sheet-name` attribute on the `table` element where the `data-sheet-name` has precedence.

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

To produce a cell with specific data type you need to use the `data-cell-type` on the `td` element. The supported data types are `number`, `boolean`, `date`, `datetime` and `formula`:

```html
<table>
    <tr>
        <td data-cell-type="number">10</td>
        <td data-cell-type="boolean" style="width: 85px">1</td>
        <td data-cell-type="date">2019-01-22Z</td>
        <td data-cell-type="datetime">2025-02-12T20:57:28.597Z</td>
    </tr>
</table>
```

The `datetime` and `date` should be specified in ISO format with `Z` and in UTC. Otherwise Windows servers will parse it in local time.

## Format

Excel supports setting cell string format. Add the following attributes to the `td` element:
- `data-cell-format-str` -> Specify the raw string format
- `data-cell-format-enum` -> Select an existing format

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
        <td data-cell-type="date" data-cell-format-str="m/d/yyy">2019-01-22Z</td>
    </tr>
</table>
```

Setting the format is also required when the cell needs to have a specific format category which depends on the computer locale. The cell is otherwise categorized by Excel as `General`.

For example, using `data-cell-type="date"` makes the cell a date and you can use it in the date-based calculations. However, the cell format category in Excel is displayed as `General` and not `Date`. To rectify this, you need to use `data-cell-format-str` to match your locale.

## Formula

A formula cell can be specified using `data-cell-type="formula"` on the `td` element.

```html
<table>
    <tr>
        <td data-cell-type="number">10</td>
        <td data-cell-type="number">10</td>
        <td data-cell-type="formula">=SUM(A1, B1)</td>
    </tr>
</table>
```

## Images

You can embed images into cells using the `asset` helper

```html
<table>
    <tr>
        <td><img src="{{asset "image.png" "dataURI"}}" /></td>
    </tr>
</table>
```

## Font family

You can use the following CSS styles to change the default font-family for all cells in table.

```css
td  {
  font-family: 'Verdana';
  font-size: 18px;
}
```

## Insert output into xlsx template
You can use the following flow to produce a complex xlsx with charts or pivot tables.

- open your desktop Excel application and prepare a file with a chart on one sheet and static data on the second
- upload the xlsx to jsreport studio and link it with your `html-to-xlsx` template
- generate `<table>` but with `name` attribute matching the existing sheet with data

See the example [Chart with html-to-xlsx recipe](https://playground.jsreport.net/w/admin/QiHIBqsq)

In this case, the data source for the chart is dynamic and needs to use a named formula.
To define such a formula, use "Name Manager" in the "Formulas" ribbon.
```
=OFFSET(Data!$A$2;;;COUNTIF(Data!$A$2:$A$999999;"<>"))
```
![name manager](/learn/static-resources/html-to-xlsx-name-manager.png)

This formula defines the range of non-empty cells starting `A2`  in column `A` .
To apply this named formula, you click on the chart's series and in the Excel formula box update the `SERIES` function
```
=SERIES("Sales";book1.xlsx!ChartMonths;book1.xlsx!ChartValues;1)
```
![series](/learn/static-resources/html-to-xlsx-series.png)

## Conversion triggers

You may need to postpone conversion of tables until some JavaScript `async` tasks are processed. If this is the case; set `htmlToXlsx.waitForJS = true` in the API options or `Wait for conversion trigger` in the studio menu. When set, the conversion won't start until you set `window.JSREPORT_READY_TO_START = true` inside your template's JavaScript.

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

## Issues with row height being larger than actual the content

When using `phantomjs` as the engine there are cases when a row height ends with a larger height than the actual content. This is caused by a `phantomjs` bug that retrieves a larger height when the content of cells contains white space characters.

There are two possible workarounds:

- use `letter-spacing` CSS property with some negative value ([demo](https://playground.jsreport.net/studio/workspace/H1c7vr8Cb/30))

```html
<!-- without "letter-spacing" the row would be more larger -->
<table style="letter-spacing: -4px">
    <tr>
        <td> From Date: N/A</td>
        <td> To Date: N/A </td>
        <td> Search Text: N/A </td>
        <td> Sort Order: N/A </td>
        <td> Sort Key: N/A </td>
        <td> Filter: N/A </td>
    </tr>
</table>
```

- use `line-height: 0` with a specific `height` ([demo](https://playground.jsreport.net/studio/workspace/H1c7vr8Cb/31))

```html
<!-- without "line-height" and "height" the row would be more larger -->
<table style="line-height: 0">
    <tr style="height: 20px">
        <td> From Date: N/A</td>
        <td> To Date: N/A </td>
        <td> Search Text: N/A </td>
        <td> Sort Order: N/A </td>
        <td> Sort Key: N/A </td>
        <td> Filter: N/A </td>
    </tr>
</table>
```

## Performance
The chrome engine can have performance problems when evaluating huge tables with many cells. For these cases the recipe provides an additional helper which splits large tables into chunks and runs evaluation in batches. Usage is like `each` or jsrender `for` handlebar helpers.

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

### Cheerio HTML engine
Although the `htmlToXlsxEachRows` helper prevents Chrome from hanging, the rendering can still be slow. This is because Chrome needs to create DOM elements for the whole table and evaluate every single cell. Fortunately, there is a better option for large tables - using the custom HTML engine [cheerio-page-eval](https://github.com/jsreport/cheerio-page-eval).

This custom engine is experimental and requires manual installation through NPM.

```
npm i cheerio-page-eval
restart jsreport
```

Afterward, you can select it in the studio HTML to xlsx menu and start using it. This engine doesn't create DOM representation like Chrome, so it has much better performance. However, the lack of DOM also introduces some limitations.

- The cheerio engine doesn't support global CSS styles in the `<style>` tag. You need to use in-line styles on cells.
- Since there is no DOM, the whitespace is not normalized and styles that affect it does not have any effect (like `white-space`), cheerio will take the whitespace present in the source html as it is.
- It also doesn't evaluate JavaScript in the `<script>` tags. The helpers and templating engines aren't limited.

`htmlToXlsxEachRows` helper also works with the cheerio engine and can significantly improve rendering memory footprint on long tables.

## Preview in studio
See general documentation for office preview in studio [here](/learn/office-preview).

## Postprocess using xlsx recipe
The `html-to-xlsx` will be always limited and you may miss some features that aren't yet implemented in it. In this case you can use [xlsx](/learn/xlsx) recipe and postprocess the `html-to-xlsx` and modify what you need using low level `xlsx` helpers.

[Demo in playground](https://playground.jsreport.net/w/anon/tWGz8qVs)

## API
You can specify the template the standard way by using `name` or `shortid`, or alternatively you can also send it in the API request. If you have the Excel template stored as an asset you can also reference it in the request.

```json
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

If you don't have the xlsx template stored as an asset you can send it directly in the API request.

```json
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
