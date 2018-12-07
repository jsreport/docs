
> Merge or concatenate multiple pdf templates into one output pdf

## Examples

- [Mixed pages orientation](https://playground.jsreport.net/studio/workspace/BkujXYfVG/10)
- [Header with page number](https://playground.jsreport.net/studio/workspace/BkEHf9MNG/5)
- [Watermark](https://playground.jsreport.net/studio/workspace/By2Z79z4f/7)
- [Dynamic header](https://playground.jsreport.net/studio/workspace/BkEHf9MNG/11)

## Basics
jsreport extension which is able to merge or concatenate multiple pdf templates into single output. The merge functionality can be used to add dynamic header based on the content of particular page. The join can be used to prepend a cover to the pdf or to change page orientation  dynamically through the single report. These advanced functions are provided on the top of standard pdf recipes and fills the gap to reach fully dynamic and unlimited pdf outputs.

The pdf manipulations are configured using studio, respectively individual template menu.

![pdf-utils](https://jsreport.net/screenshots/pdf-utils.png)

## Operations
Each template can contain ordered list of pdf operations which will be invoked after the main template rendering is finished. The operation required attributes are template and type. The template specifies what to render and the type what to do with the result. There are three operation types:
- **prepend**: render another template and put all the output pdf pages to the beginning
- **append**: the same as prepend, but just put the output pdf to the end
- **merge**: render another template with expectation single pdf page is produced and merge it to the every pdf page in the current output

## Operations order
The operations are executed sequentially in the specified order. This is important attribute because the input of every operation is the output of the previous one. This behavior can be explained on the following example.

**Example**    
*It is required that the output pdf has at the beginning a single cover page and every other page includes dynamic header. This can be solved with one prepend and one merge operations. However if you move the merge operation after the prepend, it results into the undesired output with header also merged into the first cover page.  This is because the prepend operation in such order runs the first and expands the final output with another page. This expanded pdf is then used as input of the second operation which invokes merge into every page which means also to the cover. The correct order in this case should be the merge operation the first and the prepend as the second.*

## Input data
The input data from the original template are forwarded to the rendering of the pdf operation. The operation input data however includes additional information about the current pdf. 

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

The merge operation can be expressed as putting two transparent papers (pages) on each other. This opens various use cases and possibilities. The merge can be used for example to add a watermark to every page. This requires an extra template with positioned and transparent image in the middle. The merge operation with such template puts the image on every page of the parent pdf.

The header scenario works the similar way. It is only required the header has already enough space in the parent template for the correct paging. This is typically done using margin settings provided by the recipe. This way the header can be positioned to the top, bottom or even rotated to the side.

The merge operation includes two additional parameters. 
- **render for every page**: if true, the operation invokes rendering of the specified template for every pdf page, otherwise it is invoked just once and the single output is merged
- **merge to front**: if true, the pdf page produced by the operation is merged to the front layer, otherwise it is merged to the background

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

## Recipes
The extension is tested on the [phantom-pdf](/learn/phantom-pdf) and [chrome-pdf](/learn/chrome-pdf). It should be able even to combine outputs from both recipes inside one template.