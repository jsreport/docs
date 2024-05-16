> Download jsreport single file executable and use it from the command line

![executable](https://jsreport.net/img/executable.gif)

## Download

Every jsreport npm release contains also its compiled version for windows, linux and mac. These executable can be downloaded right from the github releases:

**[Download from github releases](https://github.com/jsreport/jsreport/releases/latest)**

*We recommend to check jsreport [get started](https://jsreport.net/learn/get-started) to understand the basic concepts before further reading.*

## Rendering

The executable can be used as state-less, server-less and dependency-less pdf or excel rendering utility. It can be easily integrated into other applications and repeatedly invoked as process with arguments defining the rendering parameters.

### Simple html to pdf conversion

The most basic example of jsreport executable usage can be simple html to pdf conversion.
```sh
jsreport render
  --template.engine=none
  --template.recipe=chrome-pdf
  --template.content=test.html
  --out=out.pdf
```

You can see that this command includes some not very obvious arguments at the first glance, but these arguments basicaly represents jsreport [API request](/learn/api) and makes perfectly sense if you know a bit about jsreport.

- `--template.engine=none` - specifies we don't want to use any preprocessor for transforming html. These transformers are called [engines](/learn/get-started#templating-engines) in jsreport.
- `--template.recipe=chrome-pdf` - specifies how the input should be converted to the output format. This is called [recipe](https://jsreport.net/learn/get-started#recipes) in jsreport and in this case we use [chrome-pdf](/learn/chrome-pdf) recipe which can convert html into pdf.
- `--template.content=test.html` - is our html we want to convert
- `--out=out.pdf` - specifies the file where the output is stored

We could extend this basic example with some additional options [chrome-pdf](/learn/chrome-pdf) recipe supports. Like changing the chrome paper format:
```sh
jsreport render  
  --template.engine=none
  --template.recipe=phantom-pdf
  --template.content=test.html
  --template.chrome.format=Tabloid
  --out=out.pdf
```

### Html to excel conversion

jsreport supports many [recipes](/learn/recipes) and you can for example use one which is cappable of transforming html into excel

```sh
jsreport render
  --template.engine=none
  --template.recipe=html-to-xlsx
  --template.content=test.html
  --out=out.xlsx
```

See the [html-to-xlsx](/learn/html-to-xlsx) recipe documentation for supported features and details.

### Dynamic output

jsreport supports dynamic html construction using javascript [templating engines](/learn/templating-engines) like [handlebars](/learn/handlebars) or [jsrender](/learn/jsrender). These can be used to assemble the output report based on the input data and html template.

Lets say we have template file `template.html`
```html
<h1>{{message}}</h1>
```

And some dynamicly obtained data stored in `data.json`
```json
{
  "message": "Hello world"
}
```

We can now send both to jsreport and get back pdf with `Hello world` heading.

```sh
jsreport render
  --template.engine=handlebars
  --template.recipe=chrome-pdf
  --template.content=template.html
  --data=data.json
  --out=out.pdf
```

These were just very simple examples. Please try to give jsreport some time and you will find many powerful features you may need.

### Improving performance

You can dramatically improve the performance by adding `--keepAlive` argument to the `render` command. This will instruct jsreport to start background process and reuse it in the following renderings. You can find more information in the [cli documentation](/learn/cli).

## Running full server

The jsreport executable is not just an utility program. It also includes visual report designer or http server with the REST API. You basically get with it almost the same functionality as with the full server installed through npm.

The easiest is to start up with `configure` command which asks you couple of questions and prepares some configurations files for you.

```sh
jsreport configure
```

Then you can start jsreport server and open studio in the browser

```sh
jsreport start
```

Now you can use studio to prepare report templates and use API to render them. However you can also shut down the server and use just the cli to invoke rendering of stored templates identified by names.

```sh
jsreport render
    --template.name=MyStoredTemplate
    --data=mydata.json
    --out=myreport.pdf
```

## Using extensions outside of the executable

The jsreport executable have its own set of predefined extensions (in the same way that jsreport does), if you want to use a custom extension that is not included by default, for example `jsreport-wkhtmltopdf`, you will need to install the extension using `npm install @jsreport/jsreport-wkhtmltopdf` and then create a jsreport config file (if you don't have one yet), add `discover: true` option to your config:

```js
{
  "discover": true,
   ... the rest of your config here ...
}
```

this option will tell the jsreport executable to try to search for jsreport extensions in the `node_modules` directory of your project, after that change any extension that you have installed in `node_modules` will be available in the executable.

## Compile custom binary

The jsreport binary includes only the extensions from the default jsreport distribution. However, you are free to compile your own binary and include custom extensions you like.
This can be done by following these steps.

1. Make sure you have nodejs and yarn installed
2. Clone jsreport monorepo https://github.com/jsreport/jsreport.
3. Edit `jsreport.config.json` and include the desired extensions to the property `extensionsList`.
4. Run `yarn compile`

