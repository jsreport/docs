> Add meta information into the pdf report

## Installation

```
npm i jsreport-pdf-meta
```

## Fill meta using studio

![pdf-meta](/img/pdf-meta.png)

## Fill meta using API

```js
POST http://jsreportserver:5488/api/report

{ 
  "template": {
    "name": "my template",
    "pdfMeta": {
        "title": "title",
        "author": "author",
        "subject": "subject",
        "keywords": "keywords",
        "creator": "creator",
        "producer": "producer"
    }
  }
}
```