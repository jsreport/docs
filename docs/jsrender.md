

## Basics

jsreport jsrender engine uses [jsrender](http://www.jsviews.com/) library and therefore is fully compatible with it. This page will contain only some examples. Full documentation is located at [http://www.jsviews.com/](http://www.jsviews.com/)

## Data binding

You can use values from report input data using `{{:...}}` tags.

Assuming following input data object
```js
{
    "title": "Hello world"
}
```

you can use `{{:title}}` to print `Hello world`

```html
<h1>{{:title}}</h1>
```

## Conditions

Assuming folowing input object:
```js
{
    "age" : 18,
    "name" : "Jan Blaha"
}
```

Then you can use `age` in condition in a following way:

```html
{{if age >= 18}}
    <h1>{{:name}} is elligible to drink.</h1>
{{else}}
    <h1>{{:name}} is not elligible to drink.</h1>
{{/if}}
```

## Loops

Assuming following input data object
```js
{
    "comments": [{"title": "New job", "body": "js developers wanted at... " }]
}
```

You can simply iterate over `comments` using `for`

```html
{{for comments}}
  <h2>{{:title}}</h2>
  <div>{{:body}}</div>
{{/for}}
```

## Helpers

jsreport report template contains `content` filed with javascript tempalting engines tags and `helpers` field where you can place some javascript functions and then use them.

For example you want to have an upper case helper function. You can register a global function inside a `helpers` field with the following code:

```javascript
function toUpperCase(str) {
    return str.toUpperCase();
}
```

And then you can call function in jsrender using:
```html
say hello world loudly: {{:~toUpperCase("hello world")}}
```

## Sub templates
jsreport also supports jsrender [sub-templates](http://www.jsviews.com/#samples/jsr/composition/sub-templates) feature. That can be handy when you want to iterate over a collection of data and print a specific template for each of the item.

For this purpose you can define an item sub template inside a `content` field using following syntax:

```html
<script id="itemTemplate" type="text/x-jsrender">
    {{:#data}}
</script>
```

Then you can say to jsrender to use this sub template with following:

```html
{{for languages tmpl="itemTemplate"/}}
```

It's a good practice to use jsrender sub templates together with jsreport [child templates](https://jsreport.net/learn/child-templates) and move sub templates into dedicated report templates. This will allow you to separate big template into multiple templates and keep things clear. Note that you should set jsreport child template to `None` engine and `html` recipe for this case.
