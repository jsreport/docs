
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

- `src` (`string`) -> specifies the base64 dataURI string representation of the image to load
- `usePlaceholderSize` (`boolean`) -> when true the dimensions of the image will be set to the same dimensions than the placeholder image defined on the docx file. Ex: `{{docxImage src=src usePlaceholderSize=true}}`
- `width` (`string`) -> specifies the width of the image, value can be in `px` or `cm`. when only `width` is set, the `height` will be automatically generated based on the aspect ratio of the image. Ex: `{{docxImage src=src width="150px"}}`
- `height` (`string`) -> specifies the height of the image, value can be in `px` or `cm`. when only `height` is set, the `width` will be automatically generated based on the aspect ratio of the image. Ex: `{{docxImage src=src height="100px"}}`


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
```
