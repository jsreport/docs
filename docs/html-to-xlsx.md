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
- `colspan`- numeric value that merges current column with columns to the right
- `rowspan` - numeric value that merges current row with rows below.
- `overflow` - the excel cell will have text wrap enabled if this is set to scoll.

## Issues with row height being more larger than actual content

There are cases when a row height ends with a bigger height than the actual content, this is caused by a phantomjs bug that retrieves bigger height when the content of cells have white space characters.

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

<iframe src='https://playground.jsreport.net/studio/workspace/Y3BG0fnPa/1201?embed=1' width="100%" height="400" frameborder="0"></iframe>
