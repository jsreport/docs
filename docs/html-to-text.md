
##Installation
```bash
npm install jsreport-html-to-text
```

##Basics

`html-to-text` recipe is using [html-to-text](https://github.com/werk85/node-html-to-text) node package to convert html into readable plain text. This can be used for sending html and plain text emails just by changing the recipe.

##API

In the rendering request you can set the maximum number of characters on the line.

```js
{
	template: {
		htmlToText: {
			wordWrap: 130
		}
	}
}
```


