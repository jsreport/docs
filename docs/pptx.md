**This recipe is eperimental and its API can be changed in the future** 

`pptx` recipe generates office powerpoint presentations based on the uploaded pptx template  with [handlebars](/learn/handlebars) tags filled inside using Powerpoint application.

1. Open Powerpoint and create pptx file using handlebars templating engine. 
2. Upload created pptx file as an asset to the jsreport studio
3. Create template, select pptx recipe and link the previously uploaded asset
4. Attach sample input data or scripts if needed
5. Run the template, you get back dynamically assembled pptx report

![pptx](/img/pptx.png)

## Examples

- [Basic](https://playground.jsreport.net/w/admin/Jix3rnoQ)

## Built-in helpers

### pptxSlides
Main helper used to multiply slides. The helper call should be placed on the slide and the system iterates over provided data and creates extra slide based on item's context.
```
{{pptxSlides item}}
 ```
 
[Example - Slides](https://playground.jsreport.net/w/admin/Jix3rnoQ)

### pptxList
Create a list with single item using Word and call the `pptxList` helper. It will iterate over provided data and create another list item for every entry.
```
 - {{#pptxList people}}{{name}}{{/pptxList }}
 ```
 
[Example - List](https://playground.jsreport.net/w/admin/QCStNYjG)

### pptxImage

1. Prepare image placeholder using Powerpoint- place any image to the desired position and format it to your needs.
2. Insert to the slide a new text box with content `{{pptxImage src=myDataURIForImage}}`
3. Move the text box over previously created image
4. Select both image and text box and click group from the "Picture Tools/Format" toolbar
5. Run the template with `myDataURIForImage` prop in the input data and you should see the image replaced in the output. 

[Example - Image](https://playground.jsreport.net/w/admin/MBHWcK~B)
