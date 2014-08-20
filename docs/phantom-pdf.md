
`Phantom-pdf` recipe is using [phantomjs](http://phantomjs.org/) screen capture feature to print html content into pdf files. This approach is very productive in defining report templates and also the most used one with jsreport.

`Pantom-pdf` recipe is capable of rendering any html and javascript you provide. This means you can also use external javascript libraries or canvas to print visual charts.

##Basic settings

- Margin - px or cm specification of margin used from page borders
- Paper Format - predefined page sizes containing A3, A4, A5, Legal, Letter
- Paper Width - px or cm page width, takes precedence over paper format
- Paper Heigth - px or cm page height, takes precedence over paper format
- Orientation - portrait or landscape orientation
- Header Height - px or cm height of the header in the page
- Header - header html content
- Footer Height - px or cm height of the footer in the page
- Footer - footer html content

##Page breaks

Css contains styles like `page-break-before` you can use to specify html page breaks. This can be used as well with phantom-pdf to specify page breaks inside pdf files.

```html
<h1>Hello from Page 1</h1>

<div style='page-break-before: always;'></div>

<h1>Hello from Page 2</h1>

<div style="page-break-before: always;"></div>

<h1>Hello from Page 3</h1>
```

##Page numbers
`Phantom-pdf` is providing special tags `{#pageNum}` and `{#numPages}` to header and footer which can be used to print page numbers.
```html
<div style='text-align:center'>{#pageNum}/{#numPages}</div>
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

