> Decompose templates into reusable components

## Basics

A complex template can be decomposed into components. The component consists of templating engine content and helpers. Its evaluation works similar way to a template. It accepts input data, evaluates templating engine, and returns the output. However components, unlike [child templates](/learn/child-templates), don't have recipes or runs scripts, because components are designed to perform great even when thousands of components are rendered in a loop.

A component, with [handlebars templating engine](/learn/handlebars) can look the following
```handlebars
<h1>{{name}}</h1>
<span>{{address}}</span>
```

## How to use

A component can be embedded to the template or to a parent component using [templating engine](/learn/templating-engines) helper `component(path)`. The syntax depends on the particular templating engine used.

[handlebars](/learn/handlebars)
```handlebars
{{#each customers}} 
  {{component "./customer"}}
{{/each}}
```

[jsrender](/learn/jsrender)
```handlebars
{{for customers}} 
  {{:~component("./customer")}}
{{/for}}
```

The templating engine current context is automatically applied in the component. This means the current array item from `customers` in the previous example will be `this` in the component.

Passing some extra attributes to the component context is possible through a custom helper. This can look the following way in handlebars.

```js
function prepareData(someAttribute, options) {   
    return options.fn({
        ...this,
        someAttribute
    })
}
```

```handlebars
{{#each customers}}    
    {{#prepareData @root.someAttribute}}
        {{component "customer"}}
    {{/prepareData}}
{{/each}}
```