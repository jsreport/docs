

# Basics
`Chrome-pdf` recipe is using [headless chrome](https://developers.google.com/web/updates/2017/04/headless-chrome) to print html content into pdf files.

## Options

The settings reflects the [headless chrome API](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#pagepdfoptions) where you can also find detail information.

- scale
- displayHeaderFooter
- headerTemplate
- footerTemplate
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
- waitForNetworkIddle


These basic settings are typically stored with the template, but you can also send them through [API calls](/learn/api)  inside the `template.chrome` property.

The options can be also dynamically set from within the page javascript using:

```js
<script>
	...
	window.JSREPORT_CHROME_PDF_SETTINGS = {
		landscape:  true
	}
</script>
```


## Configuration

Use `chrome-pdf` node in the standard [config](/learn/configuration) file.

```js
"extensions": {
  "chrome-pdf": {  
    "timeout": 30000,
    "launchOptions": {...}
  }
}
```

to find more information about what is available in `launchOptions` configuration object you can check the [docs here](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#puppeteerlaunchoptions).

you can also use top level `chrome` property in configuration, the difference is that this configuration will be shared with any other extension that uses chrome and the configuration snippet above is specifically for options in `chrome-pdf` extension.

```js
"chrome": {
  "timeout": 30000
}
```

## Fonts

The fonts can be easily embedded into PDF reports using the [assets](https://jsreport.net/learn/assets) extension. You can find the tutorial on how to do it [here](https://jsreport.net/blog/fonts-in-pdf).

## Page breaks

CSS contains styles like `page-break-before` that you can use to specify html page breaks. This can be used as well with chrome-pdf in order to specify page breaks inside pdf files.

```html
<h1>Hello from Page 1</h1>

<div style='page-break-before: always;'></div>

<h1>Hello from Page 2</h1>

<div style="page-break-before: always;"></div>

<h1>Hello from Page 3</h1>
```

## Printing triggers
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

## Native headers and footers
The header and footer are evaluated as if they were a full jsreport template. This means you can add, for example, a [child template](/learn/child-templates) reference into a header and it will be extracted. You can also use main template helpers or data in the header/footer. Remember that in order to show the header/footer you need to also activate the `displayHeaderFooter` option first. As a final note about native header/footer with chrome, currently it has some scaling issues, so you need to modify some font sizes to make the content in there big enough to be visible, in most cases it is better to use [pdf-utils](/learn/pdf-utils) instead to have more powerful header/footer and without the scaling issues.

## Complex headers and footers
The [pdf-utils](/learn/pdf-utils) extension provides features to merge dynamic header, footer, or print page numbers into the chrome pdf output.
