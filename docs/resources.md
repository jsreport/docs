> Localize your templates or attach any static JSON to the rendering process

##Basics

`Resources` extension lets you attach multiple [JSON data objects](/learn/inline-data) to the report template and later conveniently accessed them using [templating engines](/learn/templating-engines) or in [custom scripts](/learn/scripts). This can be useful to add a general configuration to the template or mainly localize the template.

##Localization

The main idea behind this extension is to move all localizable strings from report templates to the JSON resources and then bind them using javascript templating engines rather then hardcode them. This extension then pushes the right localizable resource to the rendering process based on the requested language.

To localize  a template you need to:

- Attach resources containing localizable strings to the template. Each resource containing localizable string needs to be prefixed with language name. So for example attached reosurces should be data items with name `en-invoice` and `de-invoice`.

- Fill the template default language in the jsreport studio in the resources menu

- Use templating engines to fill the localized strings from `$localizedResource` property
```html
{{:$localizedResource.title}}
```

- When having multiple resources for each language use the resource names to reach the particular one
```html
{{:$localizedResource.invoice.title}}
{{:$localizedResource.globals.title}}
```

- Specify `options.language` in the API call to specify desired language

##Accessing custom resources

###Templating engines
Every template resource is parsed and provided to the templating engine rendering input. For convenience the resources are provided in two forms. 

The first form can be reached in the main object's `$resource` property. There is stored an object which contains all the attached resources' data distinguished by the resources names. 

So for example template with attached resources:

- data with name `config1`
```js
{
  "foo": "this is config1"
}	
```

- data with name `config2`
```js
{
  "foo2": "this is config2"
}	
```

```html
outputs "this is config1" when using jsrender
{{:$resource.config1.foo}}

output "this is config2" when using jsrender
{{:$resource.config2.foo2}}
```


The second form is represented as an array in `$resources` property. This array contains every attached resource in it's complete form including `name`, `shortid` and `data` property. This can be used in some advanced scenarios. To be sure what is actually stored in the `$resources` property you can dump the object in a common way you do in jsreport.
```js
//helper function
function dumpResources(data) {
  return JSON.stringify(data.$resources);
}
```
```html
print helper function outpout using jsrender
{{:~dumpResources(#data}}
```

###Scripts

[Custom jsreport script](/learn/scripts) can use the following properties:

```js
//equivalent to request.data.$resources
request.options.resources
//equivalent to request.data.$resource
request.options.resource
```

##API

To specify language in the API call you need to add `language` property in the `options` object.

> `POST:` https://jsreport-host/api/report    
> `Headers`: Content-Type: application/json    
> `BODY:`    
>```js 
   { 
      "template": { "shortid" : "g1PyBkARK" },
      "data" : { ... },
      "options": { "language": "en" }
   } 
>```


Resources are stored directly in the template document. Additionally to the `resources` this extension also adds property `defaultLanguage` to the template document.
> `GET` http://jsreport-host/odata/templates('aaaa')
>```js
{ 
	"name": "template name",
	"content": "<h1>Hello world</h1>",
	"defaultLanguage": "en",
	...
	"resources":{
		"items":[
			{ "entitySet":"data", "shortid":"NJ5H9pkb"	}
		]
	}
}
>```

##Example
<iframe src='https://playground.jsreport.net/studio/workspace/VkLWfMyMb/7?embed=1' width="100%" height="400" frameborder="0"></iframe>


