
> Decompose a report template into multiple reusable child templates

## Basics

A complex report can grow in its size and contain many separate sections. Each section logically needs its own specific helpers, design input data or even custom script to fetch them remotely. It makes sense in this case to split complex report template into multiple child templates which can be developed separately and possibly also reused. Such use case is covered by this extension.

However, if you only have static content (such as a group of styles, [html layout](https://jsreport.net/blog/template-layouts), etc) the best option (simple and with more performance) is to use the [assets](https://jsreport.net/learn/assets) extension to handle that kind of content.

## How it works
When rendering reports, jsreport searches for the special tag `{#child [nameOrPath]}` and replaces its content with the output of the referenced template. It calls the whole rendering process for child template, and includes that output where the `{#child [nameOrPath]}` special tag used to be.

The `nameOrPath` can be a unique template name or absolute or relative path. Examples:

```
{#child departmentsTemplate}
{#child /contosoCompany/templates/departments}
{#child ../shared/departments}
```


### Warning about PDF
Since child templates perform the entire rendering operation, trying to render a child template that generates PDF inside a parent template that generates PDF will fill the parent PDF with garbage (the rendered PDF of the child template).

In this case, set the `recipe` of your child template to `html` when rendering a child template into a parent PDF which is using the `chrome-pdf` recipe.

## Set child template parameters
The root request input data cascades through the child templates, but you can also pass additional data to the child template through its declaration
```html
{#child myChildTemplate @data.paramA=foo}
```

Or you can override existing child template attributes, such as the recipe used to render the child template.
```html
{#child myChildTemplate @template.recipe=html @options.language=sp}
```

Note that templating engine's `this` context doesn't flow to the child template rendering. You always get the whole parent data set context in the child template.

Complex objects can be passed as param using special syntax `$=` and helper `childTemplateSerializeData`.

```
{#child myChild @data.foo$={{{childTemplateSerializeData foo}}} }
```

##  Configuration

Add `child-templates` node to the standard config file:

```js
extensions: {
  "child-templates": {
    // controls how many child templates rendering can happen in parallel, defaults to 2
    "parallelLimit": 5
  }
}
```
