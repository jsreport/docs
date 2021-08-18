

> Compose existing templates into complex reports

## Basics

A complex report can grow in its size and contain many separate sections. Each section logically needs its specific helpers, input data, or even a [custom script](/learn/scripts) preparing input data. I this case, you can split complex report template into multiple child templates which can be developed separately and possibly also reused. Such use case is covered by this extension.

However, if you only have static content like styles, scripts, or part of HTML, which you want to reuse in multiple templates,  it's better to use [assets](https://jsreport.net/learn/assets) extension. Assets are simpler to use and bring better performance. Additionaly, in case you want to reuse just html with dynamic templating engines, you should use [components](/learn/components) extension. The child templates should be used when there is a need to attach a [custom script](/learn/scripts) or use existing standalone template in a more complex report.

## How to use

Child template rendering can be invoked by calling [templating engine](/learn/templating-engines) helper `childTemplate(nameObjectOrPath)`.
The helper call invokes the full rendering of the specified template and fills the output to the place where it was called.
The first argument of the helper should be the child template name or its path and the syntax is specific to the particular templating engine which the main template uses.

[handlebars](/learn/handlebars)
```
{{childTemplate "departmentsTemplate"}}
{{childTemplate "/contosoCompany/templates/departments"}}
{{childTemplate "../shared/departments"}}
```

[jsrender](/learn/jsrender)
```
{{:~childTemplate("departmentsTemplate")}}
{{:~childTemplate("/contosoCompany/templates/departments")}}
{{:~childTemplate("../shared/departments")}}
```

Note child template needs to produce a text which gets embedded into the parent template during templating engine evaluation.
You can't produce binary output like pdf in the child template and expect it gets embedded into the parent.
Child templates are typically configured to use [html recipe](/learn/html).

## Input data
Child template receives data from the current templating engine context by default.
In the following handlebars example, the template `student` input data would be equal to the respective item in the `students` array.
```
{{#each students}}
  {{childTemplate "student"}}
{{/each}}
```

To change the current context, you can wrap the `childTemplate` call into an extra helper.

```js
function prepareData(someAttribute, options) {   
    return options.fn({
        ...this,
        someAttribute
    })
}
```

```
{{#each students}}
  {{#prepareData @root.someAttribute}}
    {{childTemplate "student"}}
  {{/prepareData}}
{{/each}}
```

## Template attributes
The child template can be also specified using an object. This can be handy when you want to specify a different recipe.

```
{{childTemplate (myTempate)}}

function myTemplate() {
  return {
    name: "myTemplate",
    recipe: "html"
  }
}
```

## The v2 {#child } syntax
The original v2 syntax for child templates `{#child}` is still supported, but using helper calls instead is preferred.
See the [v2 child templates documenation](/learn/2.11.0/child-templates) for details.
