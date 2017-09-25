
##Installation
```bash
npm install jsreport-html-embedded-in-docx
```

##Basics
The `html-embedded-in-docx` recipe takes the `html` output and embeds it into the docx based report. This is very straight forward way to generate docx/word reports from jsreport. However please note that such output docx file cannot be opened in some Office implementations like `Open Office`. 

## Preview in studio
The report preview in the studio uses Microsoft Office Online service which requires a public access to the displayed file. This is why the recipe by default uploads the docx file to our publicly running server. This is happening only during the preview and you should not be leaking any production data through here. However you can disable or change this behavior in the configuration.

```js
...
"html-embedded-in-docx": {
  "previewInOfficeOnline": false,
  "publicUriForPreview": "http://mypublicSever"
}
```

The first options `previewInOfficeOnline` will force the recipe to always return a file stream to the studio instead of the office online preview.  The second option `publicUriForPreview` allows you to set url to the public server which should be used instead of `jsreport.net`. Such a server need to accept POST with the file stream, return a string id of the file which needs to be accessible afterwards with GET.

## Configuration

```js
"html-embedded-in-docx": {
  "previewInOfficeOnline": false,
  "publicUriForPreview": "http://mypublicSever"
}
```
