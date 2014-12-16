`html-to-xlsx` recipe generates excel xslx files from html tables. This is not a full html -> excel conversion but rather pragmatic and fast way to create excel files from jsreport. Recipe will read input table and extract couple of computed style properties which will be then applied to the excel cells.

Following properties are supported:

- `background-color` - cell background color
- `color` - cell foreground color
- `border-left-style` - as well as positions will be transformed into excel cells borders
- `text-align` - text horizontal align in the excel cell
- `vertical-align` - vertical align in the excel cell
- `width` - the excel column will get the highest width, it can be little bit inaccurate because of pixel to excel points conversion
- `height` - the excel row will get the highest height
- `font-size` - font size

> See the example in jsreport [**playground**](https://playground.jsreport.net/#/playground/Y3BG0fnPa)

```html
<style>
    td {
        background-color: yellow;
        color: green;
        border-style: solid;
    }
</style>

<table>
    <tr>
        <td style="width: 250px; height: 50px; font-size: 35px">Hello world</td>
    </tr>
    <tr>
        <td style="text-align:right">right</td>
        <td>bb</td>
    </tr>
    <tr>
        <td>aaaaaa</td>
        <td>bb</td>
    </tr>
</table>
```