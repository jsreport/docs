
`text`  recipe simply returns plain files. This recipe is great for generating text files like `csv` or `xml` because there is no transformation required. Recipe can be configured to change `Content Type`, `File Extension` and `Content Disposition`.

- `Content Type` - any mime type like `text/xml` or `text/csv` can be set
- `File Extension` - output file extension like `xml` or `csv`
- `Content Disposition` - standard full http `Content-Disposition` header or just shorten `inline` or `attachment` string. It is useful to set for example `attachment` disposition for `csv` files to force browser to open csv in excel during preview.

## Entities escaping

The templating engines usually escapes html entities like `&` into `&amp;` or `< `into `&lt;` and you need to use correct syntax to avoid this. The [handlebars](/learn/handlebars) for example has syntax `{{{foo}}}` and [jsrender](/learn/jsrender) `{{:foo}}` which doesn't do escaping.

## Big outputs

You can reach the nodejs string size limit when rendering >=500MB outputs. This can be solved using the `jsreport.templatingEngines.createStream` and streming output parts through files while minimizing the memory footprint.

This is the mostly used by implementing your own `each` helper.
```js
const jsreport = require('jsreport-proxy')
async function myEach(items, options) {
    const stream = await jsreport.templatingEngines.createStream()
    for (let item of items) {        
        await stream.write(options.fn(item))
    }
    return await stream.toResult()
}
```

```html
{{#myEach myData}}
  {{colA}};{{colB}};{{colC}}
{{/myEach}}
```

**[See the playground demo](https://playground.jsreport.net/w/admin/Iu6v6TJJ)**

