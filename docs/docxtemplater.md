
> Recipe using [docxtemplater](https://github.com/open-xml-templating/docxtemplater) to generate docx reports

## Installation

```
npm install jsreport-docxtemplater
```

## Usage

1. Prepare docx template with markup described in the [docxtemplater](https://github.com/open-xml-templating/docxtemplater) documentation
2. Upload docx template 
3. Prepare input data - use sample data or custom script
4. Create new template and switch recipe to the docxtemplater
5. Associate template asset and input data
6. Keep template content empty, it won't be used
7. Run template

![docxtemplater](https://jsreport.net/img/docxtemplater.gif)

## Preview in studio
See general documentation for office preview in studio [here](/learn/office-preview).

## API

```js
{
  "template": {
    "recipe": "docxtemplater",
    "engine": "handlebars",
    "docxtemplater": {
       "templateAssetShortid": "xxxx"
    }
  },
  "data": {}
}
```


In case you don't have the office template stored as an asset you can send it directly in the API call.

```js
{
  "template": {
    "recipe": "docxtemplater",
    "engine": "handlebars",
    "docxtemplater": {
       "templateAsset": {
          "content": "base64 encoded word file",
          "encoding": "base64"
       }
    }
  },
  "data": {}
}
```