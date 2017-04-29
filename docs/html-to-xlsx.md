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


<iframe src='https://playground.jsreport.net/studio/workspace/Y3BG0fnPa/1201?embed=1' width="100%" height="400" frameborder="0"></iframe>
