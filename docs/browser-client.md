

> jsreport browser client

## Installing 

With bundler:    

```
npm i @jsreport/browser-client
```

```js
import jsreport from '@jsreport/browser-client'
```

From CDN:

```html
<script src="https://unpkg.com/@jsreport/browser-client/dist/jsreport.umd.js"></script>
<script> jsreport.serverUrl = '...' </script>
```

## Using

Render stored template with specified input data:    

```js
jsreport.serverUrl = 'http://myjsreport.com'
const report = await jsreport.render({
  template: {
    name: 'myTemplate'    
  },
  data: {
    someData: 'hello'
  }
})
// download the output to the file
report.download('myreport.pdf')

// open output in the new window
report.openInWindow({ title: 'myreport' })
```

You can also render a template specifying its whole definition. See the [API documentation](/learn/api) for details:    

```js
jsreport.serverUrl = 'http://myjsreport.com'
const report = await jsreport.render({
  template: {
    content: 'Hello from {{message}}',
    engine: 'handlebars',
    recipe: 'chrome-pdf'
  },
  data: {
    someData: 'browser client'
  }
})
report.download('myreport.pdf')
```

The custom headers, like authorization, can be globaly passed to `jsreport.headers`:    

```js
jsreport.headers['Authorization'] = "Basic " + btoa("admin:password")
```

The render output includes the following methods

```js
// download output content to file
download(fileName)

// open content in new window, optionaly specify javascript window.open paramaters
// the cleanInterval is used to for the resources cleanup after the window is closed and defaults to 5000ms
Promise<Window> openInWindow({ windowName, title, windowFeatures, cleanInterval})

// get output content as string
Promise<String> toString()

// get output content as Blob
Promise<Blob> toBlob()

// get output content as data URI
Promise<String> toDataURI()

// get output as object URL, see URL.createObjectURL for details
Promise<String> toObjectURL()

// get the original window.fetch response, this can be used to reach the output headers for example
Response response
```
## Limitation
When using the `openInWindow` to display pdf in the new tab, you can't set the pdf file name the browser will use when the user clicks the download button in the browser's pdf reader. This is the limitation that has no solution when using the async `jsreport.render` which relies on the javascript `fetch` and `Blob`.

For this reason the jsreport browser clients implements aslo synchronous approach using hidden form submit for opening output in the new tab. 
```js
jsreport.openInNewWindow({ 
    title: 'tab title' , 
    filename: 'myreport.pdf'
 }, { 
    template: { name: 'your template' }
})
```
However, on the other side, this approach doesn't support sending custom headers, so it doesn't work with the authenticated jsreport servers. For completeness, the client exposes also `download` function using hidden form submit.


```js
jsreport.download('myreport.pdf', { 
    template: { name: 'your template' }
})
```

## Pdf preview

<object id="pdfPreview" data="" width="800" height="500"> 
</object>

<script>
    async function preview() {        
        const report = await jsreport.render({
            template: {
                name: 'mytemplate'
                pdfMeta: {
                    title: 'my report'
                }       
            }             
        })
        document.getElementById('pdfPreview').data = await report.toObjectURL()
    }
    
    preview().catch(console.error)
</script>
