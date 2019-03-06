


> Merge or concatenate multiple pdf templates into one output pdf

## Examples

- [Mixed pages orientation](https://playground.jsreport.net/studio/workspace/BkujXYfVG/10)
- [Header with page number](https://playground.jsreport.net/studio/workspace/BkEHf9MNG/5)
- [Watermark](https://playground.jsreport.net/studio/workspace/By2Z79z4f/7)
- [Dynamic header](https://playground.jsreport.net/studio/workspace/BkEHf9MNG/11)
- [Merge whole documents](https://playground.jsreport.net/w/admin/Wy6stA8t)
- [TOC - table of contents](https://playground.jsreport.net/w/admin/akYBA4rS)

## Basics
jsreport extension which is able to merge or concatenate multiple pdf templates into single output. The merge functionality can be used to add dynamic header based on the content of particular page or even table of contents. The join can be used to prepend a cover to the pdf or to change page orientation dynamically through the single report. These advanced functions are provided on the top of standard pdf recipes and fills the gap to reach fully dynamic and unlimited pdf outputs.

The pdf manipulations are configured using studio, respectively individual template menu.

![pdf-utils](https://jsreport.net/screenshots/pdf-utils.png)

## Operations
Each template can contain ordered list of pdf operations which will be invoked after the main template rendering is finished. The operation required attributes are template and type. The template specifies what to render and the type what to do with the result. There are three operation types:
- **prepend**: render another template and put all the output pdf pages to the beginning
- **append**: the same as prepend, but just put the output pdf to the end
- **merge**: render another pdf template and merge the output single pdf page or whole document into the current pdf

## Operations order
The operations are executed sequentially in the specified order. This is important attribute because the input of every operation is the output of the previous one. This behavior can be explained on the following example.

**Example**    
*It is required that the output pdf has at the beginning a single cover page and every other page includes dynamic header. This can be solved with one prepend and one merge operations. However if you move the merge operation after the prepend, it results into the undesired output with header also merged into the first cover page.  This is because the prepend operation in such order runs the first and expands the final output with another page. This expanded pdf is then used as input of the second operation which invokes merge into every page which means also to the cover. The correct order in this case should be the merge operation the first and the prepend as the second.*

## Input data
The input data from the original template are forwarded to the rendering of the pdf operation. The operation input includes additional information about the current pdf. 

```js
{
   ...user data
   $pdf: {
    // array representing pdf before the current operation started
	pages: [{ 
	   // explained bellow
	   items: [{}],
	   group: {}
	}]
   }
```

## Append and prepend
These two operations are straight forward. It is typically used to concatenate multiple heterogeneous reports into one. This fills the limitation of the pdf recipes which typically doesn't allow to have for example one page in portrait orientation and the second in landscape. The solution is to create one append operation producing portrait pdf and one producing landscape.

## Merge
The merge operation can be expressed as putting two transparent documents/papers on each other. This opens various use cases and possibilities. The merge can be used for example to add a watermark to every page. This requires an extra template with positioned and transparent image in the middle. The merge operation with such template puts the image on every page of the parent pdf.

The header scenario works the similar way. It is only required the header has already enough space in the parent template. This is typically done using margin settings provided by the recipe. This way the header can be positioned to the top, bottom or even rotated to the side.

The merge operation includes three additional parameters. 
- **merge whole documents**: if true, the operation will invoke rendering of the specified template and merge the whole pdf output into the current pdf, otherwise it also renders specified template but it takes just the first pdf page and merge it to every page of the current pdf
- **render for every page**: if true, the operation invokes rendering of the specified template for every pdf page, otherwise it is invoked just once and the single output is merged
- **merge to front**: if true, the pdf produced by the operation is merged to the front layer, otherwise it is merged to the background

With `merge whole documents` enabled, the header template development  requires more code but the resulting template has better performance. Such header template typically needs to iterate over all pages and add header with a page break. The output pdf then includes just headers which can be in one chunk merged into the current pdf. See this demonstrated on the [following example](https://playground.jsreport.net/w/admin/Wy6stA8t).

### Input data
In case the merge operation includes `render for every page` attribute the input data includes additionally `$pdf.pageNumber` and `$pdf.pageIndex`. 
```js
{
   ...user data
   $pdf: {
	pages: [...],	
	pageNumber: 5,
	pageIndex: 4	
  }
```
## Adding items to page

It is possible to add hidden information to a page of the main content which can be used in the pdf operations afterwards. The page content can for example include list of numbers which are summarized in the page header.

The extension provides templating engine helper `pdfAddPageItem` which adds value or hash to the page.

```html
{{#each prices}}
  <span>{{value}}</span>
  {{{pdfAddPageItem value}}}
{{/each}}
```

The value can be then reached inside the pdf operation on `$pdf.pages[x].items[y]`. The merge operation with "render for each page" enabled can use it with

```js
{{{mySum}}}
--
function mySum() {
  return this.$pdf.pages[this.$pdf.pageIndex].items.reduce((a,c) => a + c)
}
```

The passed item can be also an object with properties.
```html
{{{pdfAddPageItem name="Jan" age=33}}}
```
Note passing objects like this works only for [handlebars](/learn/handlebars) and [jsrender](/learn/jsrender). 

## Grouping pages
Another use case is when the report is represented by multiple groups of pages and it is required to make the header dynamic based on the particular group. This can be reached using helper `pdfCreatePagesGroup`.

The main pdf report producing the list of students can look like this
```html
{{#each students}}
    <h1 style='page-break-before: always'>{{name}}</h1>    
    {{{pdfCreatePagesGroup name}}}
    <div>lots of other content expanding to multiple pages</div>
    ....	
{{/each}}
```
The difference to page items is that the group doesn't need to be specific to single page. It should be created just once at the top of the group and all pages share it until there isn't another the one created.

The group information can be then retrieved from `$pdf.pages[x].group` property. The merge operation with "render for each page" enabled can use with 
```html
{{#with (lookup $pdf.pages $pdf.pageIndex)}}
  This page is in the group with value {{{group}}}
{{/with}}
```

The passed group can also be an object with properties
```html
{{{pdfCreatePagesGroup name="Jan" age=33}}}
```

## TOC - table of contents
The pdf utils can be used also to dynamically create pdf table of contents including the outlines. This is done mainly using pdf-utils fundamentals like merge operation and page items. The flow is the following.

The main template renders TOC at the top just like any other content using templating engines. To make the links clickable the html should use anchors (a tags) with # in href pointing to particular headings. Like

```html
<a href='#hello-world'>Hello world</a>
...
<h1 id='hello-world'>Hello world</a>
```

The problem is the main template doesn't know the page numbers for the links. This can be solved with pdf-utils fundamentals.

Every heading should be followed by page item which is later used to identify it in the merge operation.

```html
<h1 id='hello-world'>Hello world</a>
{{{pdfAddPageItem id='hello-world'}}}
```

Then there needs to be a new template which renders the same content as the TOC at the top of the main and it gets merged using pdf utils operation to the main template. This extra template can then use the pdf utils properties to calculate the page numbers of the headings.

```js
function getPage(root, id) {
    for (let i = 0; i < root.$pdf.pages.length; i++) {
        const item = root.$pdf.pages[i].items.find(item => item.id === id)

        if (item) {
            return i + 1
        }
    }
}
```

The last step is to add the pdf outlines for easier navigation. This is done using anchor's special attribute `data-pdf-outline`.

```html
<a href='#hello-world' data-pdf-outline'>
  Hello world         
</a>
```

This by default use the anchors inner content for the outline title which can be overwriten using `data-pdf-outline-title` attribute. To create hierarchic outlines structure the `data-pdf-outline-parent` with id of the parent outline can be used.

This documentation mainly highlights the idea how the TOC can be implemented using pdf-utils. It can look a bit tedious to implement and maintain, but this can be improved a lot using jsreport [child-templates](/learn/child-templates) which helps with reusing the TOC part.

You can find complex example which all of this together here:
https://playground.jsreport.net/w/admin/akYBA4rS

## Recipes
The extension is tested on the [phantom-pdf](/learn/phantom-pdf) and [chrome-pdf](/learn/chrome-pdf). It should be able even to combine outputs from both recipes inside one template.