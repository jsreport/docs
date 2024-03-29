﻿jsreport 1.0 looks dramatically different from the UI perspective, but the underlying server part is mostly back compatible with the previous releases. It shouldn't be a big problem to update, mostly it will just work. Here are some notes for problematic and incompatible features.

## Embedded store
jsreport now doesn't include deprecated [jsreport-embedded-store](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-embedded-store) (`connectionString.name='neDB`) in the default installation. If you still use it, you need to explicitly install it:
`npm install jsreport-embedded-store --save`

However we recommend to migrate to the currently default [jsreport-fs-store](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-fs-store).

This can be done manually. Open two instances of jsreport on different ports and copy paste your templates from the instance running on `neDB` to the instance running on `fs`.

If you have many templates, you can take a look on migration utility [jsreport-migrate](https://www.npmjs.com/package/jsreport-migrate). This will help you to convert your templates, scripts and data items to the new format.
1. **backup!!!** your data folder
2. make sure you have really `neDB` in your connection string
3. `npm install jsreport-migrate -g`
4. `jsreport-migrate`
5. hit enter
6. you may need to delete `data\schedules` afterwards

## Scripts and helpers custom modules
jsreport 0.x enabled some modules to be used in custom scripts by default. These modules included request, underscore, sendgrid and several others. This is no longer true in jsreport@1 and you need to explicitly install and enable modules you like to use.

1. install custom module: `npm install request --save`
2. enable custom module in script `prod|dev.config.json`
```js
{
   "scripts": {
     "allowedModules": [ "request" ]
   }
}   
```

The same applies for the helper functions where you need to explicitly enable modules the same way, just use `"tasks"` in the config instead of `"scripts"`

```js
{
   "tasks": {
     "allowedModules": [ "underscore" ]
   }
}   
```


Eventually you can use `"allowedModules": "*"`  to enable all modules

## wrapped-html recipe
`wrapped-html` has been replaced with new [html-with-browser-client](https://jsreport.net/learn/html-with-browser-client) recipe in the default installation. However you can additionally install it and it should still work: `npm install jsreport-embedding`

Unfortunately the embeddable editor included in `jsreport-embedding` won't work with jsreport@1. We were not able to port it to the newly rewritten studio. However we  may prepare through time an embeddable version of the new studio as well.

## Development mode
jsreport now better distinguish between the production mode and development mode.

`npm start --development`  starts the reporting server with configuration loaded from `dev.config.json` and more importantly with defaults which fits for the **developing report templates**. This includes more verbose logging or monitoring changes in templates caused by editing in the external editor.

`npm start --production` uses `prod.config.json` and defaults adapted for **rendering reports in production**.

## Default port
This likely affects only new installations, however worth it to notice. We changed the default configuration `prod|dev.config.json` created through `node node_modules/jsreport --init`.  The major change is that the server now starts on http port 5488 instead of https and 443

## Removed extensions
Some rarely used extensions were removed from the default installation and you may need to additionally install them. This includes `client-html` and `client-app`.
