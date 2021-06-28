jsreport uses javascript templating engines to define report layout. You can bind input data, use loops, conditions or javascript helpers to dynamically build your content. Templating engines provide a way to define any custom report you like in a very fast, flexible, matured, and well-known way.

```html
{{#each items}}
<tr class="item">  
    <td>
        $ {{calculateVAT price}}
    </td>
</tr>
{{/each}}
```

## Integrated engine libraries
jsreport default installation includes two templating engines. It's [handlebars](/learn/handlebars) and [jsrender](/learn/jsrender).
Both are very popular with a ton of sources and examples on the internet. The most common start is with handlebars, but use the one you like the most. Additionally you can install custom templating engines [EJS](/learn/ejs) and [PUG](https://github.com/bjrmatos/jsreport-pug) or even provide your own [custom engine](/learn/custom-engine).

<div class="grid">        
    <div class="row">
        <div class="span4">
            <a href="/learn/handlebars">
                <div class="tile thin bg-darkCyan text-center">
                    <div class="tile-content">
                        <span class="fg-white">handlebars</span>
                    </div>
                </div>
            </a>
        </div>
        <div class="span4">
            <a href="/learn/jsrender">
                <div class="tile thin bg-darkCyan text-center">
                    <div class="tile-content">
                        <span class="fg-white">jsrender</span>
                    </div>
                </div>
            </a>
        </div>
        <div class="span4">
            <a href="/learn/ejs">
                <div class="tile thin bg-darkViolet text-center">
                    <div class="tile-content">
                        <i class="icon-github fg-white"></i>
                        <span class="fg-white">EJS</span>
                    </div>
                </div>
            </a>
        </div>
    </div>
    <div class="row">
        <div class="span4">
            <a href="https://github.com/bjrmatos/jsreport-pug">
                <div class="tile thin bg-darkViolet text-center">
                    <div class="tile-content">
                        <i class="icon-github fg-white"></i>
                        <span class="fg-white">pug</span>
                    </div>
                </div>
            </a>
        </div>
        <div class="span4">
            <a href="/learn/custom-engine">
                <div class="tile thin bg-orange text-center">
                    <div class="tile-content">
                        <span class="fg-white">Custom Engine</span>
                    </div>
                </div>
            </a>
        </div>
    </div>
</div>

## Basics
The goal of the templating engine is to dynamically build report source, typically html, based on the input data.
The main components participating in the engine evaluation are
- input data
- HTML code with templating engine specific markup
- custom helper functions

You can see on the following screenshot where these components can be found in the jsreport [studio](/learn/studio).
<br><br>
![templating-engines](/img/templating-engines.png)
<br>

The syntax differs for every templating engine, but the following chapters will describe the general concept that is the same for every engine.

## Helpers
The common component for every templating engine is a custom helper function. 
These functions are placed at the bottom panel of the template and their main goal is to customize the evaluation using javascript.
Such helpers can help with calculation or even dynamically produce a block of html. 
The call of a helper function is invoked using specific templating engine syntax and the return value of the function is inserted at the place where it was called.
The functions are evaluated in jsreport [nodejs](https://nodejs.org/) sandbox and may include any javascript.

## Custom modules
The helper functions can also use third-party modules. 

First, you need to make sure the third-party module is installed and available.

```bash
npm i moment --save
```

The next step is enabling access to the local files and modules in the [/learn/configuration](configuration).

```js
{
    "allowLocalFilesAccess": true
}
```

Then you can use the custom module inside your helpers in the following way. 

```js
const moment = require('moment')

function formatNow() {
    return moment().format('DD.MM.YYYY')
}
```

## Async
The helper functions can be asynchronous and return promises. 
This can be handy to perform external calls or invoke modules that don't have synchronous API.

You can, for example, resize an image before putting it to the pdf and produce smaller pdfs.

```js
const Jimp = require('jimp')

async function resize (url) {
    const newImage = await Jimp.read(url)
    newImage.resize(50, 50)
    const newImageDataUri = await newImage.getBase64Async(Jimp.AUTO)
    return newImageDataUri
}
```

The helpers section can also asynchronous at the top level. 
This can be beneficial when a custom library needs an asynchronous initialization code.

```js
const someLibrary = require('someLibrary')
await someLibrary.init()

function myHelper() {
    return someLibrary.foo()
}
```

Note the async code in helpers shouldn't be used to load or manipulate the input data.
Such tasks should be done using [jsreport custom scripts](/learn/scripts).

## Internationalization
Since the [node.js 14](https://nodejs.org/) you can use the [ECMA script Intl standard](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl) to format numbers, dates, or use plural rules.

You can, for example, define a helper for formatting money and then use it based on your templating engine.
```js
function formatPrice(number, culture, currency) {
    return new Intl.NumberFormat(culture, { style: 'currency', currency }).format(number)
}
```
```html
{{formatPrice 12.5 'cs-CZ' 'EUR'}}
```

See the [Intl documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Int) for more details.