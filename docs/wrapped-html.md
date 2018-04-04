`wrapped-html` basically just executes [html](/learn/html) recipe and wraps the output into the html page with exposed template definition through global javascript object and linked `embed.js` jsreport library. This is particularly helpful in various integration usescases and when providing self editable or self exportable template.

## Self exportable template
Live html report rendered using `wrapped-html` can use `jsreport.render` function to export itself for example into pdf.
```js
$("#printCommand").click(function() {
  var printTemplate = $.extend({}, jsreport.template);
  printTemplate.recipe = "phantom-pdf";
  printTemplate.content = document.documentElement.outerHTML;
  jsreport.render(printTemplate);
});
```

Using these technique you can also export just selected part into excel or fulfill various other scenarios.

## Self editable template
You can also add editing capability into the output of `wrapped-html`. Just use `jsreport.openEditor` function for it. See [embedding](/learn/embedding) extension for details.

```js
$("#editCommand").click(function() {
  jsreport.openEditor(jsreport.template)
    .on("close", function(template) {
      jsreport.refresh(template);
    });
});
```

## Security
Note that jsreport server will reject anonymous requests for exporting or editing template as unauthorized when the [authentication](/learn/authentication) and [authorization](/learn/authorization) extension is enabled. To provide this functionality to the public users you need to use [public-template](/learn/public-templates) extension and share the template in the designer or grant the access during rendering. In this case you should always clone the original template when assembling content for exporting because the original template contains required security token which allows you to do the exporting.

```js
$("#printCommand").click(function() {
  //clone the original template to add the security token into the request
  var printTemplate = $.extend({}, jsreport.template);
  printTemplate.recipe = "phantom-pdf";
  printTemplate.content = "some html";
  jsreport.render(printTemplate);
});
```
