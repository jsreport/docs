#Basics
`Chrome-pdf` recipe is using [headless chrome](https://developers.google.com/web/updates/2017/04/headless-chrome) to print html content into pdf files. 

**PREVIEW** This recipe is in the preview and you should not use it in production.

##Installation

You need to have [node.js](https://nodejs.org/en/) at least in version 8.
```bash
npm install jsreport-chrome-pdf
```

##Options

The settings reflects the [headless chrome API](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#pagepdfoptions) where you can also find detail information.

- scale
- displayHeaderFooter
- printBackground
- pageRanges
- format
- width
- height
- marginTop
- marginRight
- marginBottom
- marginLeft
- waitForJS


These basic settings are typically stored with the template, but you can also send them through [API calls](/learn/api)  inside the `template.chrome` property.


##Configuration

Use `chrome` node in the standard [config](/learn/configuration) file.
```js
phantom: {  
  timeout: 30000
}
```

##Printing triggers
You may need to postpone pdf printing until some javascript async tasks are processed. If this is your case set the `chrome.waitForJS=true` in the API or `Wait for printing trigger` in the studio menu. Then the printing won't start until you set `window.JSREPORT_READY_TO_START=true` inside your template's javascript.
```html
...
<script>
    // do some calculations or something async
    setTimeout(function() {
        window.JSREPORT_READY_TO_START = true; //this will start the pdf printing
    }, 500);
    ...
</script>
```
