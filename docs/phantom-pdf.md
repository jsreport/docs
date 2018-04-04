The `Phantom-pdf` recipe uses the [phantomjs](http://phantomjs.org/) screen capture feature to print HTML content into PDF files. This approach is very productive in defining report templates, and also the most used one with jsreport.

The `Phantom-pdf` recipe is capable of rendering any HTML and JavaScript you provide. This means you can also use external JavaScript libraries or canvas to print visual charts.

## Installation

```bash
npm install jsreport-phantom-pdf
```

## Basic settings

- `margin` - px or cm specification of margin used from page borders, you can also pass an `Object` or `JSON object string` for better control of each margin side. ex: `{ "top": "5px", "left": "10px", "right": "10px", "bottom": "5px" }`
- `format` - predefined page sizes containing A3, A4, A5, Legal, Letter
- `width` - px or cm page width, takes precedence over paper format
- `height` - px or cm page height, takes precedence over paper format
- `orientation` - portrait or landscape orientation
- `headerHeight` - px or cm height of the header in the page
- `header` - header html content
- `footerHeight` - px or cm height of the footer in the page
- `footer` - footer html content
- `printDelay` - delay between rendering a page and printing into pdf, this is useful when printing animated content like charts
- `blockJavaScript` - block executing javascript
- `waitForJS` - true/false

These basic settings are typically stored with the template, but you can also send them through [API calls](/learn/api)  inside the `template.phantom` property.

## Page breaks

CSS contains styles like `page-break-before` that you can use to specify html page breaks. This can be used as well with phantom-pdf in order to specify page breaks inside pdf files.

```html
<h1>Hello from Page 1</h1>

<div style='page-break-before: always;'></div>

<h1>Hello from Page 2</h1>

<div style="page-break-before: always;"></div>

<h1>Hello from Page 3</h1>
```

## Headers and footers
The header and footer are evaluated as if they were a full jsreport template. This means you can add, for example, a [child template](/learn/child-templates) reference into a header and it will be extracted. You can also use main template helpers or data in the header/footer.

## Page numbers
`Phantom-pdf` provides the special tags `{#pageNum}` and `{#numPages}` to header and footer which can be used to print page numbers.
```html
<div style='text-align:center'>{#pageNum}/{#numPages}</div>
```

Note that `Phantom-pdf` also evaluates javascript in the header and footer which you can use to modify the paging start for example:

```html
<span id='pageNumber'>{#pageNum}</span>
<script>
    var elem = document.getElementById('pageNumber');
    if (parseInt(elem.innerHTML) <= 3) {
        elem.style.display = 'none';
    }
</script>
```


## National characters

`Phantom-pdf` is currently not able to print some national characters by default. To be able to print correct national characters into pdf you need to set `utf-8` charset in your html first.

```html
<html>
  <head>
    <meta content="text/html; charset=utf-8" http-equiv="Content-Type">
  </head>
  <body>
     ščřžý
  </body>
</html>
```

## Images in header and footer
You may notice image won't show up when you try to add it into phantom header in a common way:

```html
<img src='http://domain.com/foo.jpg'>
```
This is because the header and footer are printed into PDF format in a synchronous way. This means that any asynchronous request like getting image won't finish in time. This is a current limitation of phantom.js

Solution:    
Add the same image to template content and hide it with the style `display:none`. Then, you can add it to the header and it will show up because it is already cached and then no asynchronous request is needed. This is required to do for both images referenced by url as well for Data URI scheme base64 image.

## Styles in header and footer
phantomjs doesn't let you link an external style to header and footer. You need to always inline it using `<style>` tag. If this becomes tedious, you can use [child template](https://jsreport.net/learn/child-templates) to extract and reuse it.

## Printing triggers
You may need to postpone PDF printing until some JavaScript async tasks are processed. In this case, set the `phantom.waitForJS=true` in the API or enable `Wait for printing trigger` in the studio menu. Then, the printing won't start until you set `window.JSREPORT_READY_TO_START=true` inside your template's javascript.
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

## macOS sierra
The default phantomjs@1.9 currently doesn't work on the macOS sierra update, you need to use the phantomjs2. See the next chapter.

## phantomjs2
The recipe default installation uses phantomjs@1.9. You can additionally install other versions and use them in parallel.

1. Install additional phantomjs using
`npm install phantomjs-exact-2-1-1`
2. Use jsreport studio to switch phantomjs version in properties or set `"template.phantom.phantomjsVersion":"2.1.1"` in api call

You can also set the default phantomjs version globally in the config:

```js
"phantom": {   
  "defaultPhantomjsVersion": "2.1.1"
}
```

Note that phantomjs2 produces different sizes of fonts versus 1.9. Also, it doesn't support repeating `thead` when the table spawns multiple pages.

## Twitter Bootstrap
Using a responsive CSS framework such as Bootstrap for printing to PDF may not be the best idea. However, it still works. You need to keep in mind that the output PDF typically won't look the same as HTML, because Bootstrap includes different printing styles under `@media print`.

## Fonts
The fonts can be easily embedded into PDF reports using the [assets](https://jsreport.net/learn/assets) extension. You can find the tutorial on how to do it [here](https://jsreport.net/blog/fonts-in-pdf).

## Printing content in each page for advanced cases
Normally, if you need that some content to be shown on each page, you would use [headers or footers](#headers-and-footers).

However, since headers and footers are rendered like new templates and don't have any context about what is being evaluated in your main template, sometimes you will find that headers and footer are very limited for your actual requirements. In that case, you can use an approach similar to the one described in this [post](https://jsreport.net/blog/phantomjs-pdf-watermark).

The approach uses some magic numbers (values that will need to be tweaked depending on your machine and content and by your best judgment) to get the total number of pages rendered and do some calculations to be able to emulate headers and footers for each page, or just to render some content in a specific position for each page (like a watermark).

## Printing content in each page for advanced cases (pdf-utils)
Another option for printing content in each page is to use the [pdf-utils](/learn/pdf-utils) extension, it provides features to merge dynamic content into the pdf output.

## Different sizes on Windows vs Unix
Both phantomjs 1.9.8 as well as 2.1.1 are producing different sizes of PDF elements when rendered on Windows versus a Unix platform. This issue can be tracked [here](https://github.com/ariya/phantomjs/issues/12685). We recommend to design your reports on the same OS where you plan to run your production jsreport instance because of this. If this is not an option for you, you may try to apply the following css to adapt the sizes on your local or production templates.

```css
body {
  transform-origin: 0 0;
  -webkit-transform-origin: 0 0;
  transform: scale(0.654545);
  -webkit-transform: scale(0.654545);
}
```

## Configuration

Use the `phantom` configuration node in the standard [config](/learn/configuration) file.

```js
"extensions": {
  "phantom-pdf": {
    "numberOfWorkers": 1
    "timeout": 180000,
    "allowLocalFilesAccess": false,
    "defaultPhantomjsVersion": "1.9.8"
  }
}
```

you can also use top level `phantom` property in configuration, the difference is that this configuration will be shared with any other extension that uses phantomjs and the configuration snippet above is specifically for options in `phantom-pdf` extension.

```js
"phantom": {
  "numberOfWorkers": 1
  "timeout": 180000,
  "allowLocalFilesAccess": false,
  "defaultPhantomjsVersion": "1.9.8"
}
```

available options for configuration:

**phantom.strategy** (`dedicated-process | phantom-server`) - The first strategy uses a new phantomjs instance for every task.  The second strategy reuses every instance over multiple requests. Where `phantom-server` has better performance, the default `dedicated-process` is more suitable to some cloud and corporate environments with proxies

**phantom.numberOfWorkers** (`int`) - specify how many phantomjs instances will phantom-pdf recipe use. If the value is not filled, jsreport will use number of cpus by default

**phantom.timeout** (`int`) - specify default timeout for pdf rendering using phantomjs

**phantom.allowLocalFilesAccess** (`bool`) - default is `false`. When set to true you can use local paths to get resources.

**phantom.host** (`string`) - Set a custom hostname on which phantomjs server is started, useful is cloud environments where you need to set specific IP.

**phantom.portLeftBoundary** (`number`) - set a specific port range for phantomjs server

**phantom.portRightBoundary** (`number`) - set a specific port range for phantomjs server

**phantom.defaultPhantomjsVersion** (`string`) - set the default phantomjs version to use, note that if you are going to set this value other than the default you must install manually the desired phantomjs version (either using packages like `phantomjs-prebuilt` or `phantomjs-exact-2-1-1`). default: 1.9.8
