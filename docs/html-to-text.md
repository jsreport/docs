## Installation
```bash
npm install jsreport-html-to-text
```

## Basics

`html-to-text` recipe is using [html-to-text](https://github.com/werk85/node-html-to-text) node package to convert html into readable plain text. This can be used for sending html and plain text emails just by changing the recipe.

## Options

The list of options as well as the detail description can be found in the transformation package [html-to-text](https://github.com/werk85/node-html-to-text) readme.

You can specify the options in the studio or pass them in the API call as

```js
{
	template: {
		htmlToText: {
			wordWrap: 130
			...
		}
	}
}
```

You can also set the default option values globally in the configuration file as

```js
"extensions": {
	"html-to-text": {
    "wordWrap": 12
	}
}
```
