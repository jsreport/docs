
`Phantom-pdf` recipe is using [phantomjs](http://phantomjs.org/) screen capture feature to print html content into pdf files. This approach is very productive in defining report templates and also the most used one with jsreport.

`Pantom-pdf` recipe is capable of rendering any html and javascript you provide. This means you can also use external javascript libraries or canvas to print visual charts.

##Basic settings

- margin - px or cm specification of margin used from page borders
- format- predefined page sizes containing A3, A4, A5, Legal, Letter
- width - px or cm page width, takes precedence over paper format
- height - px or cm page height, takes precedence over paper format
- orientation - portrait or landscape orientation
- headerHeight - px or cm height of the header in the page
- header- header html content
- footerHeight - px or cm height of the footer in the page
- footer - footer html content
- printDelay - delay between rendering a page and printing into pdf, this is useful when printing animated content like charts
- blockJavaScript - block executing javascript

##Configuration

Use `phantom` node int the standard [config](https://github.com/jsreport/jsreport/blob/master/config.md) file.
```js
phantom: {
  numberOfWorkers: 1
  timeout: 180000,
  allowLocalFilesAccess: false
}
```

##Page breaks

Css contains styles like `page-break-before` you can use to specify html page breaks. This can be used as well with phantom-pdf to specify page breaks inside pdf files.

```html
<h1>Hello from Page 1</h1>

<div style='page-break-before: always;'></div>

<h1>Hello from Page 2</h1>

<div style="page-break-before: always;"></div>

<h1>Hello from Page 3</h1>
```

##Headers and footers
Header and footer is evaluated as it would be a full jsreport template. This means you can add for example an [child template](/learn/child-templates) reference into a header and it will be extracted. You can also use main template helpers or data in the header/footer.

##Page numbers
`Phantom-pdf` is providing special tags `{#pageNum}` and `{#numPages}` to header and footer which can be used to print page numbers.
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


##National characters

`Phantom-pdf` is currently not able to print some national characters by default. To be able to print correct national characters into pdf you need to set `utf-8` charset in your html first.

```html
<html>
  <head>
    <meta content="text/html; charset=utf-8" http-equiv="Content-Type">
  </head>
  <body>
     Česko není Čečensko
  </body>
</html>

```

##Images in header and footer
You may notice image won't show up when you try to add it into phantom header in a common way:

```html
<img src='http://domain.com/foo.jpg'>
```
This is because header and footer is printed into pdf in a synchronous way. This mean any asynchronous request like getting image won't finish in time. This is current limitation of phantom.js

Solution:    
Add the same image to template content and hide it with style display:none. Then you can add it to the header and it will show up because it is already cached and no asynchronous request is needed. This is required to do for both image referenced with url as well for Data URI scheme base64 image.

