> Embed static assets like styles, fonts or html

## Creating assets

You can create an asset using jsreport studio. The most common approach is to just upload a file like css. The second is to create an empty asset and edit its content using jsreport editor. The third option is to create an asset as link to an existing file. Such a link can be an absolute path or relative path to folder where the jsreport was started from.

## Embedding assets

The syntax for embedding asset is the following:
```
{#asset name.js}
```

Such a string will be then replaced in the output as the content of previously uploaded or linked `name.js` asset. There is no additional transformation running so it is way faster than extracting [child templates](https://jsreport.net/learn/child-templates).

The asset can be embedded into template's content, helpers or [custom script](https://jsreport.net/learn/scripts). This enables scenarios like adding common helper functions or adding configuration files to scripts.

The asset extraction is recursive which means you can create a hierarchies of assets. This lets you to group styles' links into a one asset.

The asset extraction runs twice during the rendering. At the beginning after jsreport knows the template and also after the templating engines are executed. This means you can dynamically construct asset names. The following will for example work with jsreport [handlebars](https://jsreport.net/learn/handlebars) engine.

```
{#asset {{giveMeAssetName}}}
```

## Encoding options

The assets doesn't need to be necessary text files. It can also be a binary file like image or font. In this case you can embed the asset as image in the following way using a `@encoding`

```
<img src='{#asset logo.png @encoding=dataURI}'/>
```

Supported encoding values:

- `utf8` - default encoding used when not specified, embeds the asset as a raw string in utf8 character set

- `string` - embeds the asset as a raw string in utf8 character set but escaping some characters (`"`, `'`, `\n`, etc) for usage inside a javascript context. Use this encoding when you want put asset content as part of a javascript variable
ex: `var data = "{#asset vardata.txt @encoding=string}"`

- `link` - embeds the asset as a reference to a link (`http://<host>:<port>/assets/content/<asset name>`). for in deep usage and caveats see [Embedding assets as links](#embedding-assets-as-links)

- `base64` - embeds the asset as a [base64](https://en.wikipedia.org/wiki/Base64) representation of its content

- `dataURI` - embeds the asset as a [base64](https://en.wikipedia.org/wiki/Base64) representation of its content but using a [data URI](https://en.wikipedia.org/wiki/Data_URI_scheme). Useful when embedding images and fonts.

Each encoding has different use cases, so make sure to use the correct one depending on your case.

## External files access

Uploading or linking assets through jsreport studio is not required, although it gives you authorization and user interface by design. You can also enable option `assets.searchOnDiskIfNotFoundInStore=true` in the configuration and simple embed files in the same way without touching jsreport studio.

## Embedding assets as links

Assets can be also referenced as links. This is usually better performing in html recipe because browsers can cache the assets or request them in parallel.

```html
<script src="{#asset jquery.js @encoding=link}"></script>
```

However this approach has several gotchas and you should use it only when it makes really sense.

The first problem is that the asset links needs to be publicly accessible for chrome (or external recipes based on browsers, like phantomjs). This can be opted in through config:

```js
{
   "extensions": {
     "assets": {
       "publicAccessEnabled": true
     }
   }
}
```

The second problem is that the assets links are based on the jsreport server url and this is not always easy to automatically determine. There are three cases:

1. The config contains a value for `assets.rootUrlForLinks` which is then always used as base
2. The incoming http rendering request is used to construct the root url
3. There is no incoming request (the rendering is triggered in different way) and the `rootUrlForLinks` is not set. In this case the `localhost:[PORT]` is used as base



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
You can use standard OData API to manage and query assets entities. For example you can query all assets using
> `GET` http://jsreport-host/odata/assets

## Examples
- [Using assets to embed fonts into reports](https://jsreport.net/blog/fonts-in-pdf)
- [Using assets to create template layouts](https://jsreport.net/blog/template-layouts)
