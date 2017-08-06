
##Installation
```bash
npm install jsreport-wkhtmltopdf
```

##Basics

This recipe is using [wkhtmltopdf](http://wkhtmltopdf.org/) to transform html into pdf. Wkhtmltopdf includes more advanced pdf features like clickable links, table of contents or outlines(bookmarks).

##Options
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

##Configuration

You can globally wkhtmltopdf recipe to add wkhtmltopdf supported options this way:
```js
"wkhtmltopdf": {
  "proxy": "url",
  "any other option supported by wkhtmltopdf": true || "value",
  //options like env variables passed to wkhtmltopdf child process
  "execOptions": { ... }
},
```

See the list of supported options [here](http://wkhtmltopdf.org/usage/wkhtmltopdf.txt).

##Local file access
Accessing local files through wkhtmltopdf is disabled by default.  You can enable it using this config:
```js
"wkhtmltopdf": {
	"allowLocalFilesAccess": true
}
```

##API

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
