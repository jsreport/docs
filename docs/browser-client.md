
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
report.openInWindow()
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
Promise<Window> openInWindow({ windowName, windowFeatures, cleanInterval})

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
````


