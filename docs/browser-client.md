**Standalone distributable jsreport browser sdk**

##Download

You can use npm
> npm install jsreport-browser-client-dist

Or download `jsreport.js` from the [release page](https://github.com/jsreport/jsreport-browser-client-dist/releases)

The script itself should be compatible with [webpack](https://webpack.github.io/), [requirejs](http://requirejs.org/) and other script bundlers.

##Usage

**rendering based on submitting hidden form, this works on legacy browsers **

```js
jsreport.serverUrl = 'http://localhost:3000';

var request = {
  template: { 
    content: 'foo', engine: 'none', recipe: 'phantom-pdf'
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


**rendering using ajax calls**
```js
//add custom headers to ajax calls
jsreport.headers['Authorization'] = "Basic " + btoa("admin:password")

//render through AJAX request and return promise with array buffer response
jsreport.renderAsync(request).then(function(res) {
  console.log(res);
  
  //open in new window
  window.open(res.toDataURI())
  
  //open download dialog
  res.download('test.pdf')
});

jsreport.getTemplateByName(name).then(..)
jsreport.updateTemplate(template).then(..)
```

You can find more details about the `request` argument  [jsreport-core](https://github.com/jsreport/jsreport-core) repository.
