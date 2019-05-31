> Recipe which loads pdf from static source

![static-pdf](https://jsreport.net/img/static-pdf.gif)

## Installation

```
npm i jsreport-assets
npm i jsreport-static-pdf
```

## Basics

This recipe lets you use an static PDF as the output result of a render execution. It is useful when you have an existing PDF file and you just want to modify or add content to it using [pdf-utils](https://jsreport.net/learn/pdf-utils), or maybe add a [signature](https://jsreport.net/learn/pdf-sign) or [password protection](https://github.com/jsreport/jsreport-pdf-password) to it, in this case you can create an [asset](https://jsreport.net/learn/assets) with a PDF file as its content, then you can create a template using the `static-pdf` recipe and add a reference to the pdf asset using the `static pdf` options, finally when you render this template the result will be the referenced pdf asset.

## API

```js
POST http://jsreportserver:5488/api/report

{
  "template": {
    "name": "my template",
    "staticPdf": {
      "pdfAssetShortid": "<asset shortid here>"
    }
  }
}
```

You may want to avoid storing your static PDF file inside jsreport store. In this case, you can send the encoded PDF inline in your rendering request.

```js
POST http://jsreportserver:5488/api/report

{
  "template": {
    "name": "my template",
    "staticPdf": {
      "rawContent": "<base64 PDF>"
    }
  }
}
```
