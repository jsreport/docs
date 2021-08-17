
> Embed static assets like images, CSS, fonts, scripts, and others

## Creating assets

You can create an asset using jsreport studio. The most common approach is to just upload a file like CSS. The second is to create an empty asset and edit its content using the jsreport editor. The third option is to create an asset as a link to an existing file. Such a link can be an absolute path or relative path to the folder where the jsreport was started from.

## Embedding assets

Assets can be embedded using [templating engine](/learn/templating-engines) helper `asset(nameObjectOrPath, encoding)`.
The syntax depends on the particular templating engine used.

[handlebars](/learn/handlebars)
```handlebars
<style>
  {{asset "mainTheme.css"}}
</style>

<img src="{{asset "/shared/logo.png" "dataURI"}}" />
```

[jsrender](/learn/jsrender)
```handlebars
<style>
  {{:~asset("mainTheme.css")}}
</style>

<img src="{{:~asset("/shared/logo.png", "dataURI")}}" />
```

The assets are static and there is no templating engine evaluation processed on the embedded content. 
In case you want to decompose big templates into smaller parts with templating engines, look at the [jsreport components](/learn/components).

## Registering helpers from assets
The assets can be conveniently used to store and reuse templating engine helpers.

The first, create an asset `myHelpers.js` with globaly declared helper functions
```js
function upperCase(val) {
  return val.toUpperCase()
}
```

Register the helpers using the following code placed into the template helpers section.
```js
const jsreport = require('jsreport-proxy')
await jsreport.assets.registerHelpers('myHelpers.js')
```

The helper functions are then reachable from the template content
```handlebars
{{upperCase someVal}}
```

> The asset can be also marked as a "shared helpers asset" and automatically included in every template and [component](/learn/components) helpers.
This can be done from the asset's properties panel through the jsreport studio.

## Read asset
The asset content can be reached from the [jsreport script](/learn/scripts) or from the templating engines using `jsreport.assets.read`.

```js
const jsreport = require('jsreport-proxy')
const myConfiguration = await jsreport.assets.read('/somepath/myConfiguration.txt')
```

## Require asset
The asset can be also loaded into [jsreport script](/learn/scripts) or into templating engine helpers as a nodejs module.

```js
const jsreport = require('jsreport-proxy')
const myModule = await jsreport.assets.require('myModule.js')

myModule.myFn()
```

## Encoding options

The assets don't need to be necessary text files. It can also be a binary file like an image or font. In this case, you can specify the encoding using the second parameter.

```handlebars
<img src="{{asset "/shared/logo.png" "dataURI"}}" />
```

Supported encoding values:

- `utf8` - default encoding used when not specified, embeds the asset as a raw string in utf8 character set

- `string` - embeds the asset as a raw string in utf8 character set but escaping some characters (`"`, `'`, `\n`, etc) for usage inside a javascript context. Use this encoding when you want put asset content as part of a javascript variable
ex: `var data = "{{asset "vardata.txt" "string"}}"`

- `link` - embeds the asset as a reference to a link (`http://<host>:<port>/assets/content/<asset name>`). for in deep usage and caveats see [Embedding assets as links](#embedding-assets-as-links)

- `base64` - embeds the asset as a [base64](https://en.wikipedia.org/wiki/Base64) representation of its content

- `dataURI` - embeds the asset as a [base64](https://en.wikipedia.org/wiki/Base64) representation of its content but using a [data URI](https://en.wikipedia.org/wiki/Data_URI_scheme). Useful when embedding images and fonts.

Each encoding has different use cases, so make sure to use the correct one depending on your case.

## External files access

Uploading or linking assets through jsreport studio is not required, although it gives you authorization and user interface by design. You can also enable option `assets.searchOnDiskIfNotFoundInStore=true` in the configuration and simple embed files in the same way without touching jsreport studio.

## Embedding assets as links

Assets can be also referenced as links. This is usually better performing in HTML recipe because browsers can cache the assets or request them in parallel.

```handlebars
<script src="{{asset "jquery.js" "link"}}"></script>
```

However, this approach has several gotchas and you should use it only when it makes sense.

The first problem is that the asset links need to be publicly accessible for chrome (or external recipes based on browsers, like phantomjs). This is opt-in through config:

```js
{
   "extensions": {
     "assets": {
       "publicAccessEnabled": true
     }
   }
}
```

The second problem is that the assets links are based on the jsreport server URL and this is not always easy to automatically determine. There are three cases:

1. The config contains a value for `assets.rootUrlForLinks` which is then always used as the base
2. The incoming HTTP rendering request is used to construct the root URL
3. There is no incoming request (the rendering is triggered differently) and the `rootUrlForLinks` is not set. In this case the `localhost:[PORT]` is used as the base

## Configuration

Add `assets` node to the standard config file:

```js
extensions: {
  assets: {
    // wildcard pattern for accessible linked or external files
    allowedFiles: "static/**.css",
    // enables access to files not stored as linked assets in jsreport store    
    searchOnDiskIfNotFoundInStore: false,
    // root url used when embedding assets as links {#asset foo.js @encoding=link}
    rootUrlForLinks: "http://mydomain.com",
    // make all assets accessible to anonymous requests
    publicAccessEnabled: true
  }
}
```

## API
You can use standard OData API to manage and query assets entities. For example, you can query all assets using
> `GET` http://jsreport-host/odata/assets

## Examples
- [Using assets to embed fonts into reports](https://jsreport.net/blog/fonts-in-pdf)
- [Using assets to create template layouts](https://jsreport.net/blog/template-layouts)

## Preview in studio
See general documentation for office preview in studio [here](/learn/office-preview).

## The v2 {#asset } syntax
The original v2 syntax for assets `{#assset}` is still supported, but using helper calls instead is preferred.
See the [v2 child templates documenation](/learn/2.11.0/assets) for details.