
**This recipe is eperimental and its API can be changed in the future**

`docx` recipe generates office docx reports based on the uploaded docx template  with [handlebars](/learn/handlebars) tags filled inside using Word application.

1. Open Word and create docx file using handlebars templating engine.
2. Upload created docx file as an asset to the jsreport studio
3. Create template, select docx recipe and link the previously uploaded asset
4. Attach sample input data or scripts if needed
5. Run the template, you get back dynamically assembled docx report

![docx](/img/docx.png)

## Examples

- [Curriculum vitae (CV)](https://playground.jsreport.net/w/admin/dhuCc7eL)
- [Invoice](https://playground.jsreport.net/w/admin/yo9J3hvu)
- [Students](https://playground.jsreport.net/w/admin/d7o0nIWc)

## Built-in helpers

### docxList
Create a list with single item using Word and call the `docxList` helper. It will iterate over provided data and create another list item for every entry.
```
 - {{#docxList people}}{{name}}{{/docxList}}
 ```

[Example - List](https://playground.jsreport.net/w/admin/r~IBX3Bm)

### docxTable
Create a table with columns header and single row using Word. Call `{{#docxTable}}` in the first cell of the data row and end the call `{{/docxTable}}` at the last cell.

|columnA|columnB|
|---|---|---|---|---|
|{{#docxTable people}}{{name}}|{{email}}{{/docxTable}}|

[Example - Table](https://playground.jsreport.net/w/admin/q94T4AJa)

#### Vertical table
Use helper argument `vertical=true` for rendering vertical table

| | | |
|-|-|-|
| name | {{#docxTable people vertical=true}}{{name}} |
| email | {{email}}{{/docxTable}} |

#### Dynamic columns
The table can be fully dynamic and even nested. Use `rows=[[]]` and `columns=[]` helper arguments to render dynamic columns.

| |
|-|
| {{docxTable rows=rowsItems columns=columnsItems}} |
||

The `docxTable` helper call provides also `@rowIndex` and `@columnIndex` variables.

```
{{#docxTable rows=rowsItems columns=columnsItems}}
{{@rowIndex}}-{{@columnIndex}}
{{/docxTable}}
```


### docxStyle
Wrap block with `{{#docxStyle}}{{/docxStyle}}` and pass `textColor` parameter to dynamicaly specify text color.

```
{{#docxStyle textColor='0000FF'}}Simple text{{/docxStyle}}
```

[Example - Style](https://playground.jsreport.net/w/admin/Mc2Pdcyo)

### docxImage

1. Prepare image placeholder using word - place any image to the desired position and format it to your needs.
2. Select image, select Word tab "Insert", click on "Bookmark and create one
3. Right click image and click "Hyperlink"
4. Click bookmark and select previously created bookmark
5. Click ScreenTip still on the "Insert Hyperlink" modal.
6. Fill the docxImage helper call `{{docxImage src=myDataURIForImage}}`
7. Hit ok and close the hyperlink dialog. Now if you hower the image you should see the `docxImage` helper call
8. Run the template with `myDataURIForImage` prop in the input data and you should see the image replaced in the output.

[Example - Image](https://playground.jsreport.net/w/admin/UwTKeBUW)

`docxImage` supports the following configuration properties:

- `src` (`string`) -> specifies the base64 dataURI string representation of the image to load or an url from which to fetch the image
- `usePlaceholderSize` (`boolean`) -> when true the dimensions of the image will be set to the same dimensions than the placeholder image defined on the docx file. Ex: `{{docxImage src=src usePlaceholderSize=true}}`
- `width` (`string`) -> specifies the width of the image, value can be in `px` or `cm`. when only `width` is set, the `height` will be automatically generated based on the aspect ratio of the image. Ex: `{{docxImage src=src width="150px"}}`
- `height` (`string`) -> specifies the height of the image, value can be in `px` or `cm`. when only `height` is set, the `width` will be automatically generated based on the aspect ratio of the image. Ex: `{{docxImage src=src height="100px"}}`

### docxChart

Create a chart inside the desktop word application and use `docxChart` helper call inside the chart title.

[Example - Chart](https://playground.jsreport.net/w/admin/OQIYR_my)

The helper call in a title can look like this:

```html
A title{{docxChart data=fruits}}
```

With the following data on the input:
```js
{
    "fruits": {
        "labels": ["Q1", "Q2", "Q3", "Q4"],
        "datasets": [{
            "label": "Apples",
            "data": [100,50,10,70]
        }, {
            "label": "Oranges",
            "data": [20,30,20,40]
        }]
    }
}
```

The `data.labels` describes labels on the X axis. The `data.datasets` includes values for the Y axis.


### Forms

The form elements like checkboxes and inputs can be placed to the word file from the Developers tab. This tab is by default hidden so you need to enable it using:

**File** tab, go to **Options** > **Customize Ribbon**
Under **Customize the Ribbon** and under **Main Tabs**, select the **Developer** checkbox.

#### Text input
No need to use a special helper. Just add a text box and use handlebars syntax inside to fill a dynamic value.

#### Checkbox
Add checkbox control from the Developer tab, select Properties and use `docxCheckbox` helper call in the title. The only supported param is `value` which expects bool to toggle the checkbox.

```
{{docxCheckbox value=ready}}
```

#### Combobox
Add combobox control from the Developer tab, select Properties and use `docxCombobox` helper call in the title.

```
{{docxCombobox value=val}}
```
In case your items are static, you can prepare them upfront in the "Drop-Down List Properties" and use just a single parameter `value` to preselect desired items.

In case your items are dynamic, you can bind them using `items` parameter. This parameter expects the following array:
`[ { value: "a", text: "Some text to select" } ...]`
```
{{docxCombobox value=val items=items}}
```

## Development
Repetitive uploading of the docx template can get easily tedious. Fortunately, you can use the [fs store external editor editing](/learn/fs-store#editing-templates-in-custom-editor) feature with docx as with any other file. The first you need to change the configuration to enable automatic files reload on external changes.

```js
"extensions": {
  "fs-store": {
    "syncModifications": {
      "updateStudio": true
    }
  }
}
```

Afterward, make sure you have ready a template with docx recipe and associated docx template in the studio. Then open a word application (the best is on the second screen) and find file `data/adocxtemplate/content.docx` representing your associated template. Now, editing and saving this file in word app cause automatic reload of the template in the studio and re-render so you see immediately the reflected changes.

## Preview in studio
See general documentation for office preview in studio [here](/learn/office-preview).

## API

```js
{
  "template": {
    "recipe": "docx",
    "engine": "handlebars",
    "docx": {
       "templateAssetShortid": "xxxx"
    }
  },
  "data": {}
}
```


In case you don't have the office template stored as an asset you can send it directly in the API call.
```js
{
  "template": {
    "recipe": "docx",
    "engine": "handlebars",
    "docx": {
       "templateAsset": {
          "content": "base64 encoded word file",
          "encoding": "base64"
       }
    }
  },
  "data": {}
}
```
