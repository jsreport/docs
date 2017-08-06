> Automatically inject [html base tag](https://www.tutorialspoint.com/html/html_base_tag.htm) to allow relative referencing of local styles, images or scripts

## Usage

### Set base address in config

```js
"base": {
  "url": "${cwd}/myAssets"
},
"phantom-pdf": {
  "allowLocalFilesAccess": true
}
```

The extension then injects [html base tag](https://www.tutorialspoint.com/html/html_base_tag.htm) into every valid html:

```html
<html>
  <head>
    <base href="file:///jsreportFolder/myAssets" />
    <script src="js/jquery.min.js"></script>
  </head>
  <body>
  </body>
</html>
```

The base tag assures that relatively linked scripts, images or styles gets loaded from the specified folder without additional extra work. Only note that you may need to enable local file access in the pdf rendering recipe like [phantom-pdf](/learn/phantom-pdf) using `phanton.allowLocalFilesAccess: true` option.

### Send base address when running through cli

```
jsreport render
  --template.engine=none
  --template.recipe=phantom-pdf
  --template.content=test.html
  --options.base=%cd%
  --out=out.pdf 
```


### Send base address as part of the api request

> `POST`: http://localhost:5488/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
```js
{
  "template": {
    "content": "<html><head></head><body></body></html>",
    "recipe": "html",
    "engine": "handlebars"
  },
  "options": {
    "base": "http://foo.com"
  }
}
```