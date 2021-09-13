
**Standalone distributable jsreport browser sdk**

## Download

You can use npm
> npm install @jsreport/browser-client

Or download `jsreport.js` from the [release page](https://github.com/jsreport/jsreport/tree/master/packages/browser-client/releases)

The script itself should be compatible with [webpack](https://webpack.js.org/), [requirejs](http://requirejs.org/) and other script bundlers.

## Usage

### Rendering using ajax calls

```js
//add custom headers to ajax calls (renderAsync)
jsreport.headers['Authorization'] = "Basic " + btoa("admin:password")

//render through AJAX request and return promise with array buffer response
jsreport.renderAsync(request).then(function(res) {
  console.log(res);

  //open in new window
  window.open(res.toDataURI())

  //get the content as string
  res.toString()

  //open download dialog
  res.download('test.pdf')

  // get headers
  res.xhr.getResponseHeader('Content-Type')
});

jsreport.getTemplateByName(name).then(..)
jsreport.updateTemplate(template).then(..)
```

You can find more details about the `request` argument in the [jsreport-core](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-core) repository.

## Rendering based on submitting hidden form
This method has several limitations and it is recommended to use previous`renderAsync` if it is possible.

- it can send only small amount of the input data
- doesn't support sending headers and it won't work with jsreport server with enabled authentication
- the boolen values in the input data are serialized as strings

```js
jsreport.serverUrl = 'http://localhost:3000';

var request = {
  template: {
    content: 'foo', engine: 'none', recipe: 'chrome-pdf'
   }
};

//display report in the new tab
jsreport.render('_blank', request);

//display report in placeholder with id reportPlaceholder
jsreport.render('reportPlaceholder', request);

//display report in placeholder element
jsreport.render(document.getElementById('reportPlaceholder'), request);

//open download dialog for report
jsreport.download('myReport.pdf', request);
```

## Large report file

when using `renderAsync`, if the generated report is big (>=3MB) and when `res.toDataURI()` is used, it will generate a big data uri which browsers can't use normally to open and preview the report file (in `window.open` or when loading an iframe). the solution for this is to use `res.toObjectURL()` to create an [object url](https://developer.mozilla.org/es/docs/Web/API/URL/createObjectURL) which does not have the limitations of a data uri, then you can use the generated url in `window.open(res.toObjectURL())` or to load an iframe `iframe.src = res.toObjectURL()` and preview the report file normally. another method available is `res.toBlob()` which returns a [blob object](https://developer.mozilla.org/es/docs/Web/API/Blob) which you can use if you need to process the file somehow or in a custom way.

As the [object url docs](https://developer.mozilla.org/es/docs/Web/API/URL/createObjectURL) explains, remember that when using object urls you must release these objects when you don't need it anymore, which can be done using [`URL.revokeObjectURL`](https://developer.mozilla.org/es/docs/Web/API/URL/revokeObjectURL) method. an example of releasing an object url can be found [here](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications#Example_Using_object_URLs_to_display_images).

## window.open in chrome

The latest chrome currently throws error "Not allowed to navigate top frame to data URL" for call `windows.open(res.toDataURI)` with pdf. This change is documented [here](https://stackoverflow.com/a/45495974/1660996). The current workaround is to embed an iframe to the new tab:
```js
jsreport.renderAsync(request).then(function(res) {                        
    var html = '<html>' +
            '<style>html,body {padding:0;margin:0;} iframe {width:100%;height:100%;border:0}</style>' +
            '<body>' +                                
            '<iframe type="application/pdf" src="' +  res.toDataURI() + '"></iframe>' +
            '</body></html>';
    var a = window.open("about:blank", "Report")
    a.document.write(html)
    a.document.close()
})
``` 