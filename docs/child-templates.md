> Decompose templates into small reusable pieces

## Basics
Sometimes you'll encounter a situation where you find out that your report templates are starting to be too complicated. The html gets bigger, and you need to copy paste several parts between templates quite often. In this situation, you may wish to consider using the _child templates_ extension.

Child templates allow you to include report templates into each other even in multiple levels. Doing so is simple. You just create a standard template for a part you want to reuse. Let's call it `Some table`. In every parent template in which you want to reuse the table, insert the special tag `{#child Some table}`.

It may also be used to centralize your CSS styles into one 'child template,' and include that CSS into every other template by including it.

## How it works
When rendering reports, jsreport searches for the special tag `{#child [name]}` and replaces its content with the output of the referenced report. It calls the whole rendering process for child template, and includes that output where the `{#child [name]}` special tag used to be.

Choosing the `none` engine for a child template means that no rendering of the child template will be performed before it is inserted into the parent. In most cases, you should use an [asset](https://jsreport.net/learn/assets) for this static content instead.

### Warning about PDF
Since child templates perform the entire rendering operation, trying to render a child template that generates PDF inside a parent template that generates PDF will fill the parent PDF with garbage (the rendered PDF of the child template).

In this case, set the `recipe` of your child template to `html` when rendering a child template into a parent PDF which is using the `phantom-pdf` recipe.

## Set child template parameters
The root request input data cascades through the child templates, but you can also pass additional data to the child template through its declaration
```html
{#child myChildTemplate @data.paramA=foo}
```

Or you can override existing child template attributes, such as the recipe used to render the child template.
```html
{#child myChildTemplate @template.recipe=html @options.language=sp}
```

## Accessing parent items
Inside a child template, the scope may change. If you want to still access the content of the parent items, you can use `../` in the path to access those items.

## Using static content
Child templates are particularly useful when you wish to break a big template into smaller child templates for better organization. This is an ideal use for child templates because they support dynamic content like a normal template.

However, if you only have static content (such as a group of styles, [html layout](https://jsreport.net/blog/template-layouts), etc) the best option (simple and with more performance) is to use the [assets](https://jsreport.net/learn/assets) extension to handle that kind of content.
