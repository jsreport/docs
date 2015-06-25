
##Installation
```bash
npm install jsreport-wkhtmltopdf
```

##Basics

This recipe is using [wkhtmltopdf](http://wkhtmltopdf.org/) to transform html into pdf. Wkhtmltopdf includes more advanced pdf features like clickable links, table of contents or outlines(bookmarks). 

Note that wkhtmltopdf recipe loads only resources specified with the absolute url including protocol like https://code.jquery.com/jquery-2.1.4.min.js . **This means you need to add the protocol to the sample template created by jsreport at the very first time**.

##Options
Options reflects the wkhtmltopdf options. See its [docs](http://wkhtmltopdf.org/usage/wkhtmltopdf.txt) for details.  Currently supported options are:

- orientation
- header
- footer
- headerHeight
- footerHeight
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


