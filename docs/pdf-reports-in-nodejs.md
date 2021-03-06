jsreport is an innovative reporting platform developed in node.js. It can be used in various use cases from simple one like html to pdf conversion. To more advanced where jsreport is used as a reporting server for designing and printing reports remotely. This article covers some of the scenarios where jsreport can be usefull.

## Rendering pdf
jsreport makes it super simple to print html into pdf using [chrome](/learn/chrome-pdf). You just need to install [jsreport npm package](https://www.npmjs.org/package/jsreport) and call one function.

> npm install jsreport

```js
const http = require('http');
const jsreport = require('jsreport');

http.createServer((req, res) => {
  jsreport.render({
    template: {
      content: '<h1>Hello world</h1>',
      engine: 'handlebars',
      recipe: 'chrone-pdf'
    }
  }).then((out)  => {
    out.stream.pipe(res);
  }).catch((e) => {
    res.end(e.message);
  });

}).listen(1337, '127.0.0.1');
```

Note html can contain any complex javascript including jquery or charting libraries so the output pdf can look anything you want.

## Paper size, headers and footers

You can also submit various other options to rendering like paper size, paper margin, header and others. You can find more information about jsreport and chrome integration [here](/learn/chrome-pdf). Following example will print pdf with a width of 300px and a header.

```js
require('jsreport').render({
	template: {
		content: '<h1>Hello world</h1>',
		recipe: 'chrome-pdf',
		chrome: {
			headerTemplate: '<p>some header</p>',
			width: '300px'
		}
	},
})
```

for more complex use cases in header/footer use [pdf-utils](/learn/pdf-utils) instead.

### Dynamic pdf rendering
jsreport evaluates javascript templating engines in submitted html. This means you don't have to render html dynamically on your own and let the hard work to be done by jsreport. JavaScript templaing engine can be specified in the overloaded `render` function. Currently it supports [jsrender](https://jsreport.net/learn/jsrender) and [handlebars](https://jsreport.net/learn/handlebars) by default. You can also submit javascript templating engine input object together with helper functions. See example:

```js
require('jsreport').render({
	template: {
		content: 'Hello world from {{#sayLoudly this.name}}',
		helpers: 'function sayLoudly(str) { return str.toUpperCase(); }',
		engine: 'handlebars"
	},
	data: { name: 'jsreport' }
}).then((out) => {
	//pipes pdf with Hello world from JSREPORT
	out.stream.pipe(res);
});
```

## Output formats
jsreport is not strictly tied to chrome. It is just one of the supported technologies to print output in jsreport. You can for example just [print html](https://jsreport.net/learn/html) or print pdf using [apache fop](https://jsreport.net/learn/fop-pdf). Technology used for printing can be specified using the `recipe` property. You can find more about supported recipes [here](https://jsreport.net/learn/recipes). Following example will evaluate javascript templating engines and return html.

```js
require('jsreport').render({
	template: {
		content: '<h1>Hello world from {{this.name}}</h1>',
		recipe: 'html"
	},
	data: { name: 'jsreport' }
}).then((out) => {
	//pipes plain text with Hello world from jsreport
	out.stream.pipe(resp);
});
```

## Safe for dangerous code

jsreport rendering is safe by default and you don't have to be afraid it will block node.js event loop or do any potentially dangerous things.  For example dangerously modified helper function like following one will not block node.js event loop because it will run in child process and fails resulting promise if the timeout occurs.
```js
function sayLoudly(str) { while(true) { } }
```
This is similar for chrome where jsreport will just recycle affected instance when timeout occurs.

## More options

jsreport has tons of options and is very extensible.  You may want to enable particular extension. This can be achieved by modifying or extending `renderDefaults` object. Following example shows how to increase chrome timeout and add a console logger. See [configuration documentation](/learn/configuration) for other options.

```js
var jsreport = require('jsreport');

jsreport.renderDefaults.logger.console = { transport: 'console', level: 'debug' };
jsreport.renderDefaults.chrome.timeout = 180000;

jsreport.render('<h1>Hello world</h1>').then((out) => {
    out.stream.pipe(resp);
};
```

## Studio for designing reports

jsreport has also a user interface you or users of your software can use to define and preview reports.  You can visit simplified [jsreport playground](https://jsreport.net/playground) and check it out.

<a href="https://jsreport.net/screenshots/studio.png" target="_blank">
<img src="https://jsreport.net/screenshots/studio.png" alt="studio" style="width: 600px;"/>
</a>

You can easily start whole jsreport application including user interface and public api. There you can create and test report templates and later trigger report rendering using remote client. You can find more information in section [adapting jsreport](https://jsreport.net/learn/adapting-jsreport).

```js
//starts default express.js application containing jsreport studio and api
require('jsreport')({ httpPort: 2000}).init();
```

## Render pdf in a remote server

In some cases your application just want to connect to an already running jsreport server. You may also want to use [reporting as a service](https://jsreport.net/online) provided by jsreport. In this cases you can use similar rendering functions remotely.

To render pdf remotely you only need to install [jsreport-client](https://www.npmjs.org/package/jsreport-client) and call render in the same way as mentioned earlier in this blog post. Next example shows how to remotely print pdf "hello world". See the full documentation for `jsreport-client` npm package [here](https://jsreport.net/learn/nodejs-client).

> npm install jsreport-client

```js
const client = require('jsreport-client')(url, username, password);

client.render({
  template: { content: 'Hello World', recipe: 'chrome-pdf'}
}).then((out) => {
	out.pipe(res)
});
```
