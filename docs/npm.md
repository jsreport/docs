> Install and use npm modules from within reports during realtime

**[Example in playground](https://playground.jsreport.net/w/admin/q7MtIkV7)**

## Basics

This extension allows you to install and use custom [npm](https://www.npmjs.com/) modules without a need to access the environment where is jsreport hosted.
This can be very handy during common reports development because it saves time for server access and restarts. Also it opens the new possibilities in locked environments like [jsreport online](/online) or [playground](/learn/playground).

## Use npm module in helper or jsreport script

The extension exposes async `jsreport.npm.require` function which installs and loads the requested module. The module needs to be specified in form `[moduleName]@[moduleVersion]`.
```js
const jsreport = require('jsreport-proxy')
const moment = await jsreport.npm.require('moment@2.29.1')

function myHelper() {
    return "Today is " + moment()
}
```

## Use npm module in the template content

The extension exposes custom helper `npmModule` that reads the bundled distribution of the module. The module needs to be specified in the form `[moduleName]@[moduleVersion]`.
[handlebars](/learn/handlebars)
```
<script>{{npmModule "moment@2.29.1"}}</script>
<script>console.log(moment())</script>
```

[jsrender](/learn/jsrender)
```
<script>{{:~npmModule("moment@2.29.1")}}</script>
<script>console.log(moment())</script>
```

## Configuration

The usage of the npm modules is opt-in and can be enabled in the following ways:

1. enable config `allowLocalFilesAccess:true` for the whole server
2. set conifg `extensions.npm.allowedModule:"*"`
3. set conifg `extensions.npm.allowedModule:["moment@2.29.1"]` and explicitly list modules users can require