jsreport v3 is a major release which on top of great new features also includes several breaking changes.

The following chapters pinpoint the changes that could affect your installation and describe how to migrate.

It's strongly recommended to don't do an in-place production upgrade, but rather process upgrade locally and do proper testing before going live.

Please don't hesitate to reach us on [forum](https://forum.jsreport.net), in case you experience problems or you have any doubts.

## nodejs version
You will need at least node.js 16.11.0 to run jsreport v3.

## npm @jsreport scope
The official [jsreport npm package](https://www.npmjs.com/package/jsreport) has still the same name in the npm. But all the extensions including the core now prefixes `@jsreport/` scope in the name. This means the following:

`jsreport-core` -> `@jsreport/jsreport-core`
`jsreport-pdf-utils` -> `@jsreport/jsreport-pdf-utils`

The `@jsreport` scope guarantees the package is distributed and maintained by the official jsreport team.
You won't notice this change in most cases, but when you have custom extensions in the `package.json`, you need to change the dependencies to include the `@jsreport` scope.

## licensing development mode
Since v3, you need to properly mark your licensed development instances with config `{ "license": { "development": true } }`. Otherwise, you may experience warning dialogs in the studio, notifying you about the parallel license usage.

## docker full
We removed `jsreport-wkhtmltopdf`, `jsreport-electron-pdf`, `jsreport-phantom-pdf` and `jsreport-fop-pdf`  extensions from the jsreport full docker image.  

The first three are still maintained and you can explicitly install them to your own docker images. However, you won't be able to use the jsreport docker image as a base, because it uses an updated Ubuntu base image that breaks phantomjs. You need to use the older Ubuntu version which we use in jsreport v2. Keep in mind the npm name has changed to `@jsreport/jsreport-electron-pdf` and `@jsreport/jsreport-phantom-pdf`

## blob storages
jsreport v3 uses much more the [blob storages](/learn/blob-storages).  jsreport now persist also version control diffs or the rendering profiles to blobs. You won't notice this when you use the `fs` store. However, SQL and Mongo users should validate this. In jsreport v2, it's typically not important if you use SQL [template store](/learn/template-stores) and store blobs to file system locally. But in v3, you should configure blob storage to persist blobs to the same store.

This configuration causes entities to be stored in SQL and blobs to the local disk. This is typically wrong.
```js
{
  "store": { "provider": "mssql" },
  "blobStorage": { "provider": "fs" }
}
```

You should instead use the same provider also for blobs. This is now possible because every template store implementation supports also blobs persistence.
```js
{
  "store": { "provider": "mssql" },
  "blobStorage": { "provider": "mssql" }
}
```

## browser client
The jsreport browser client package `jsreport-browser-client-dist` was renamed to [@jsreport/browser-client](https://www.npmjs.com/package/@jsreport/browser-client) and it includes **BREAKING** changes in the API.

We removed the sync `jsreport.render` API using the hidden forms submit. It was causing too many problems with high volumes and data types. Instead, the `jsreport.render` API uses the standard [javascript fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) and runs asynchronously.

See the [browser client docummentation](/learn/browser-client) for details to inspect the API changes.

**There is no migration automation for this change and you need to update your code using the browser client by hand.**

## fs store modification synces
The `extensions.fs-store.syncModifications` was removed because the jsreport now synces changes between multiple servers in the cluster using the journal file. 

Also because of the journal file, the `jsreport-fs-store-aws-sns-sync` and `jsreport-fs-store-azure-sb-sync` extensions are no longer needed. You don't need to install these and the sync will just work implicitly by design.
Please remove them from your docker image or package.json.

In case you want the original external modifications monitoring through file system polling, use the config 
`extensions.fs-store.externalModificationsSync=true`.

## config changes
The deprecated `templatingEngines.timeout`, `extensions.chrome-pdf.timeout` and `extensions.scripts.timeout` are now removed and only the root `reportTimeout` property is used.

The `templatingEngines.numberOfWorkers` is removed. Instead, the `workers.numberOfWorkers` specifies how many worker threads will be allocated. This also specifies the maximal parallelization of the reports rendering.
```js
{
  "workers": { "numberOfWorkers": 2 } 
}
```

The `templatingEngines.allowedModules` is removed. Instead, the `sandbox.allowedModules` can be used.
```js
{
  "sandbox": {
    "allowedModules": ["moment"]
  }
}
```

## resources extension
The `resources` extension is replaced with [localization](/learn/localization) extension in v3.  The localization extension doesn't need strict references and uses folders and assets instead.

The v3 includes automatic migration for the templates using resources. It copies the linked resources into assets and attaches jsreport [script](/learn/scripts) which implementation replicates the same behavior as the resources extension provided in the v2. In other words, you can just upgrade jsreport and your current template using resources will work the same.

Although the migration will give you the same behavior, we strongly recommend adapting your templates to use the [localization extension](/learn/localization) instead.

## xlsxTemplates
The `xlsxTemplate` entity set, originally used in [xlsx](/learn/xlsx) and [html-to-xlsx](/learn/html-to-xlsx), to reference excel template was replaced with assets and deprecated long time ago. The v3 fully removes this entity set. However, it includes automatic migration to copy all of the existing xlsxTemplates into assets so you shouldn't notice problems if you still have some of them in your workspace.

The API requests using `template.xlsxTemplate.content` to specify the template inside the requests won't work in v3.
```js
{
	template: {
	  recipe: 'xlsx',
	  engine: 'handlebars',
	  xlsxTemplate: { content: ".... },
	  content: '{{{xlsxPrint}}}'
   }
}
```

You need to use the `template.xlsx.templateAsset.content` instead.

```js
{
"template":  {  
	"recipe":  "xlsx",  
	"engine":  "handlebars",  
	"content":  "{{{xlsxPrint}}}",  
	"xlsx":  {  
		"templateAsset":  {  
			"content":  "base64 encoded word file",  
			"encoding":"base64" 
		}  
	}  
}
```

## nodejs render shortcut
The nodejs rendering shortcut was removed:
```js
const jsreport = require('jsreport')
// this throws, it was removed
jsreport.render(..)
```

Instead, initialize the jsreport instance and fully specify what you want to render.

```js
const jsreport = require('jsreport')()

await jsreport.init()
const response = await jsreport.render({
  template: { content: 'hello', engine: 'handlebars', recipe: 'chrome-pdf' }
})
```


