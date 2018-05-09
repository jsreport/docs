
## Installation
```bash
npm install jsreport-wkhtmltopdf
```

## Basics

This recipe is using [wkhtmltopdf](http://wkhtmltopdf.org/) to transform html into pdf. Wkhtmltopdf includes more advanced pdf features like clickable links, table of contents or outlines(bookmarks).

## Options
Options reflects the wkhtmltopdf options. See its [docs](http://wkhtmltopdf.org/usage/wkhtmltopdf.txt) for details.  Currently supported options are:

- orientation
- header
- footer
- headerHeight
- footerHeight
- cover
- dpi
- javascriptDelay
- windowStatus
- marginBottom
- marginLeft
- marginRight
- marginTop
- pageSize
- pageHeight
- pageWidth
- toc
- tocHeaderText
- tocLevelIndentation
- tocTextSizeShrink
- title
- keepRelativeLinks
- printMediaType
- disableSmartShrinking

## Configuration

You can globally wkhtmltopdf recipe to add wkhtmltopdf supported options this way:
```js
"extensions": {
  "wkhtmltopdf": {
    "proxy": "url",
    "any other option supported by wkhtmltopdf": true || "value",
    //options like env variables passed to wkhtmltopdf child process
    "execOptions": { ... }
  }
}
```

See the list of supported options [here](http://wkhtmltopdf.org/usage/wkhtmltopdf.txt).

## Headers footers
The `header` and `footer` parameter requires full html. For example

```html
<!DOCTYPE html>
<html>
<body>
    Header...
</body>
</html>
```
See the [header with page number demo in the playground](https://playground.jsreport.net/studio/workspace/bkBXJqNOae/119).

## Local file access
Accessing local files through wkhtmltopdf is disabled by default.  You can enable it using this config:
```js
"extensions": {
  "wkhtmltopdf": {
  	"allowLocalFilesAccess": true
  }
}
```

## Different sizes on Windows vs Unix
Unfortunately wkhtmltopdf will produce different sizes of pdf elements when rendered on Windows vs Unix platform. We recommend to design your reports on the same OS where you plan to run your production jsreport instance because of this. If this is not an option for you, you may try to use the wkhtmltopdf's `zoom` property with an approximate value of `0.78125` to match windows size in linux/macOS machines, but you probably will need to update also the `margin` property too because it seems that in some cases changing the `zoom` has an impact in the `margin`.

Another option is to apply the following css in your template to adapt the sizes on your local or production templates.

```css
body {
  transform-origin: 0 0;
  -webkit-transform-origin: 0 0;
  transform: scale(0.654545);
  -webkit-transform: scale(0.654545);
}
```

## API

Rendering request template can include `wkhtmltopdf` property with specific options.
```js
{
	template: {
		wkhtmltopdf: {
			orientation: "landscape"
		}
	}
}
```
