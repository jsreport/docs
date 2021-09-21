﻿



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
- waitForNetworkIdle


These basic settings are typically stored with the template, but you can also send them through [API calls](/learn/api)  inside the `template.chrome` property.

The options can be also dynamically set from within the page javascript using:

```js
<script>
	...
	window.JSREPORT_CHROME_PDF_OPTIONS = {
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
```

You can also use css property `page-break-inside` to for example avoid breaking an element to multiple pages.

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

See an example of using printing triggers when rendering charts [here](https://playground.jsreport.net/w/admin/O3R9I~xl).

## Native headers and footers
The header and footer are evaluated as if they were a full jsreport template. This means you can add, for example, a [child template](/learn/child-templates) reference into a header and it will be extracted. You can also use main template helpers or data in the header/footer. Remember that in order to show the header/footer you need to activate the `displayHeaderFooter` option first and add some top, bottom margin to the template in order to give the page some space to show the header/footer.

Inside the header/footer template you can use some special css classes to make chrome inject some content for you. the special css classes supported by chrome are the following:

- `date` -> injects formatted print date
- `title` -> injects the content of the document title
- `url` -> injects the document location
- `pageNumber` -> injects current page number
- `totalPages` -> insject the total pages

There are some issues with native header/footer you should be aware of:

-  images cannot be referenced link, you need to use base64 data URI
- javascript is not evaluated
- content has scaling issue and need to set font size css to make it big enough to be visible
- background color is not printed, use `-webkit-print-color-adjust: exact` as workaround

In the most of the cases it is better to use [pdf-utils](/learn/pdf-utils) instead which is less limiting and without these issues.

The chrome by default displays the current date in the header and file name in the footer when `displayHeaderFooterSelected`. You can place an empty tag `<span/>` to the header or footer to avoid this behavior.

Example showing how to use the special css classes and the workaround for the scaling issues.

```html
<!--header template content-->
<html>
  <head>
    <style>
      /* defining explicit font-size solves the scaling issue */
      html, body {
        font-size: 12px;
      }
    </style>
  </head>
  <body>
    <!--
      defining some elements with the special css classes makes chrome
      inject content in runtime
    -->
    Page&nbsp;<span class="pageNumber"></span>&nbsp;of&nbsp;<span class="totalPages"></span>
  </body>
</html>
```

## Complex headers and footers
The [pdf-utils](/learn/pdf-utils) extension provides advanced and more rich features to merge dynamic content into the chrome pdf output, like rich header/footer, print page numbers, watermarks, merge pages with different orientation, etc. make sure to check the [docs](/learn/pdf-utils) for some examples.

## CSS Media type and Bootstrap
Chrome by default uses `print` CSS media query when printing pdf. This impacts CSS frameworks like Bootstrap which usually produces different results for `print` media type. The pdf in this case applies different styles then html. You can adapt/unite this by changing media type settings from `print` to `screen` in the template's chrome settings.

## Reuse chrome instances
The recipe by default starts extra new chrome process every time you render a template. This behavior can be changed and recipe configured to reuse several chrome instances to improve rendering performance.

```js
{
  "extensions": {
    "chrome-pdf": {
      "strategy": "chrome-pool",
      "numberOfWorkers": 3
    }
  }
}
```

## Printing existing web pages
You can also print an existing webpage through `chrome-pdf` recipe without a need to define your templates in jsreport studio. Just send a request like this:

```js
{
  "template": {
    "recipe": "chrome-pdf",
    "engine": "none",
    "chrome": {
      "url": "https://jsreport.net"
    }
  }
}
```

Or you can create an empty template and define the url using [jsreport script](/learn/scripts).

```js
function beforeRender(req, res) {
  req.template.chrome = {
     "url": "https://jsreport.net"
  }
}
```

## Troubleshooting

self closing divs (`<div />`) are heavily slowing down chrome pdf rendering, don't use them
<hr>
some users experienced freezing chrome because of wrong indentation of source html, this may sounds strange but it can help to click code reformat
<hr>
chrome may do page breaks badly if you use images, it helps if you explicitly set image height in the wrapped div

```html
 <div style='height:500'>
   <img src='foo' />
 </div>
```
<hr>
tables with long content expand across multiple pages as needed, and if you have table headers/footer those will be replicated in each page which the table's content is present. however the headers/footers replicated in each page don't leave any kind of space by default, this result in having weird layout issues in which header content overlaps rows in other pages. The solution for this is to add the needed space (represented as padding) in an empty header/footer cell to properly separate the content that is split across pages. There is also issue with the borders when content is large, the solution for this is to not use `border-spacing: collapse` which does not work properly when content is split across pages. An example of both cases and solutions is available [here](https://playground.jsreport.net/w/admin/wsa4boBl)
<hr>
 chrome/puppeteer doesn't run by default in limited environment like docker and it usually asks to pass `--no-sandbox` argument. This can be achieved using the following config. See also [puppeteer troubleshooting](https://github.com/GoogleChrome/puppeteer/blob/master/docs/troubleshooting.md).

 ```js   
 "extensions": {  
  "chrome-pdf": {  
    "launchOptions": {  
      "args": ["--no-sandbox"]  
    }
  }
}  
```
