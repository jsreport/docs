`xlsx` recipe generates excel xslx files from plain [Office Open XML SpreadsheetML File Format](http://msdn.microsoft.com/en-us/library/dd922181%28v=office.12%29.aspx). The input template content must contain valid `<worksheet>` node and optionally `<stylesheet>` node. `Xlsx` recipe will take the whole xml, add other required information and zip it into xlsx file which is provided and displayed.

This can be a little bit chatty but you should be able to reach readable templates using child templates extension and templating engines. The easiest way to discover  [Office Open XML SpreadsheetML File Format](http://msdn.microsoft.com/en-us/library/dd922181%28v=office.12%29.aspx) format is to create required document in excel, save it, rename to zip file, extract it and browse the files.

> See the example in jsreport [**playground**](https://playground.jsreport.net/#/playground/YBjmBsPFa)

Minimal example working in jsreport looks the following.

```xml
<worksheet
xmlns="http://schemas.openxmlformats.org/spreadsheetml/2006/main"
xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships">
    <dimension ref="A1"/>
    <sheetViews>
        <sheetView workbookViewId="0"/>
    </sheetViews>
    <sheetFormatPr defaultRowHeight="13.5"/>
    <cols>
        <col min="1" max="1" width="56" />
        <col min="2" max="2" width="18" />
    </cols>
    <sheetData>
        <row r="1">
            <c r="A1" t="inlineStr"><is><t>column 1</t></is></c>
            <c r="B1" t="inlineStr"><is><t>column 2</t></is></c>
        </row>
    </sheetData>
    <phoneticPr fontId="1" type="noConversion"/>
    <pageMargins left="0.7" right="0.7" top="0.75" bottom="0.75" header="0.3" footer="0.3"/>
    <pageSetup paperSize="9" orientation="portrait" horizontalDpi="200" verticalDpi="200"/>
</worksheet>
```