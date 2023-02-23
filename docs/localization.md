

> Store localized texts for specific languages in JSON asset and reach them using helper calls

**[Example in playground](https://playground.jsreport.net/w/admin/1cC6FCIe)**    
**[Example with interpolation](https://playground.jsreport.net/w/anon/3O_g94m1)**

## Get started
Using jsreport studio, create a folder and inside JSON [assets](/learn/assets) representing your localized texts. The assets should be named as `[language abbreviation].json`. For example `en.json` and `de.json`. The content should contain key-value pairs for the localized texts. For example

`en.json`
```js
{
    "hello": "Hello world"
}
```

`de.json`
```js
{
    "hello": "Hallo Welt"
}
```

Now the localization resources are ready, and you can use templating engine helper `localization("key", "folder")` to reach them from inside your templates.
The first parameter represents the key in the stored JSON and the second parameter is a path to the folder with JSON assets. The path can be absolute or relative to the particular template being rendered.

[handlebars](/learn/handlebars)
```html
<h1>Locallized message: {{localize "hello" "localization"}}
```

[jsrender](/learn/jsrender)
```html
<h1>Locallized message: {{~localize("hello","localization")}}
```

Now you can render the template, but the first you need to specify the language to be used. This can be done easily through the studio template/localization menu.

## API
In production, the language is typically specified as part of the rendering request inside `options.localization.language` property.

> `POST:` https://jsreport-host/api/report
> `Headers`: Content-Type: application/json
> `BODY:`
>```js
   {
      "template": { "name" : "my-template" },
      "data" : { ... },
      "options": { "localization": { "language": "en" } }
   }
>```


## Using in scripts
The localization function can be used also in the jsreport [scripts](/learn/scripts).

```js
const jsreport = require('jsreport-proxy')
async function beforeRender(req, res) {
    const localizedString = await jsreport.localization.localize("key", "folder")
}
```
The `jsreport-proxy` is a virtual module automatically provided to your sandbox, you can't install it from the package manager.

You can also use a custom script, to specify the language, used in the templating engine helpers, based on the input data.

```js
const jsreport = require('jsreport-proxy')
function beforeRender(req, res) {
    req.options.localization = {
        language: req.data.myLanguageSpecification
    }
}
```

## Complex use case
It can be often helpful to create your own `localization` helper and adapt the behavior for your needs. The following example uses [shared assets](/learn/assets#shared-helpers) to add `translate`  helper with more complex flow to every template.
```js
const jsreport = require('jsreport-proxy');
const defaultLanguage = 'en'

// add to helpers custom localization function
async function translate(key, translationsPath) {   
    if (typeof translationsPath !== 'string') {
        // default to specific translations folder
        const template = jsreport.req.template
        const templatePath = await jsreport.folders.resolveEntityPath(template, 'templates')
        const folderPath = templatePath.substring(0, templatePath.lastIndexOf('/'))
        translationsPath = folderPath + '/translations'
    }

    let localizedVal
    try {
        localizedVal = await jsreport.localization.localize(key, translationsPath)
    } catch (e) {
         // asset with requested language not found, try default language
         try {
            localizedVal = await jsreport.localization.localize({
                key, 
                folder: translationsPath,
                language: defaultLanguage
            })
         } catch (e) {
             // asset for default language not found
             return `[${key}]`
         }
    }

    if (localizedVal == null) {
        // key not found in the asset, try search for key in the asset for default language
        try {
            localizedVal = await jsreport.localization.localize({
                key, 
                folder: translationsPath,
                language: defaultLanguage
            })
        } catch (e) {
            // asset for default language not found
            return `[${key}]`
        }
        if (localizedVal == null) {
            return `[${key}]`
        }
        return localizedVal
    }

    return localizedVal
}
```

You can also extend the localization function with interpolation for example. See an [example in playground](https://playground.jsreport.net/w/anon/3O_g94m1).

