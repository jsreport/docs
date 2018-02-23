

##Basics

jsreport handlebars engine uses [handlebars](http://handlebarsjs.com) library and therefore is fully compatible with it. This page contains only some examples. Full documentation is located at [http://handlebarsjs.com](http://handlebarsjs.com)

##Data binding

You can use values from report input data using `{{...}}` tags.

Assuming following input data object
```js
{
    "title": "Hello world"
}
```

you can use `{{title}}` to pring `Helo world`
```html
<h1>{{title}}</h1>
```
##Conditions

Assuming folowing input object:
```js
{
    "shouldPrint" : true,
    "name" : "Jan Blaha"
}
```

Then you can use `shouldPrint` bool value in the `if` condition. For more sophisticated condition see `Helpers` section.

```html
{{#if shouldPrint}}
    <h1>{{name}}</h1>
{{/if}}
```

##Loops

Assuming following input data object
```js
{
    "comments": [ {"title": "New job", "body": "js developers wanted at... " }]
}
```

You can simply iterate over `comments` using `each`
```html
{{#each comments}}
  <h2>{{title}}</h2>
  <div>{{body}}</div>
{{/each}}
```

##Helpers

jsreport report template contains `content` filed with javascript templating engines tags and `helpers` field where you can place some javascript functions and then use them.

For example you want to have an upper case helper function. You can register a global function inside a `helpers` field with the following code:

```javascript
function toUpperCase(str) {
    return str.toUpperCase();
}
```

And then you can call function in handlebars using:
```html
say hello world loudly: {{{toUpperCase "hello world"}}}
```

##Thirdparty helper libraries
There are plenty of thirdparty libraries providing additional handlebars helpers like [handlebars-helpers](https://github.com/assemble/handlebars-helpers) or [handlebars-intl](http://formatjs.io/handlebars/). To use such a library in jsreport, you need to install it from npm and then `require` it at the top of the template's helpers.

`npm install handlebars-intl`
```js
var handlebars = require('handlebars');

var HandlebarsIntl = require('handlebars-intl');
HandlebarsIntl.registerWith(handlebars);
```

`npm install handlebars-helpers`
```js
var handlebars = require('handlebars');

var helpers = require('handlebars-helpers')({
  handlebars: handlebars
});
```

