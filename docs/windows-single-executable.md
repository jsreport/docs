jsreport is mainly distributed through [node package manager](https://www.npmjs.com/package/jsreport), but for windows users we ship also convenient distribution in the single executable file. You simple [download](/downloads) the file `jsreport.exe` and run it. The executable automatically unpacks required files and runs the node process hosting jsreport server. Except .net runtime there is no need for any other dependency.

##Usage

Start server with default config file on the https 443
> jsreport.exe    

Override configuration through command line parameters
> jsreport.exe --httpPort 2000 --httpsPort 0

Install windows service
> jsreport.exe --httpsPort 5000 --install

Uninstall windows service
> jsreport.exe --httpsPort 5000 --uninstall

Get help
> jsreport.exe  --help

##Just rendering
`jsreport.exe` can be also used for command line report rendering without starting a server or REST API. 

>jsreport.exe --render request.json --output file.pdf

Where `request.json` is json formatted file with report specification. The file has the same structure as API call so you can use the same instructions as in the [API documentation](/learn/api).

Example `request.json`:
```js
{
  "template": { 
    "content": "Hello World {{:foo}}",
    "recipe": "phantom-pdf",
    "engine": "jsrender"
  },
  "data": {
    "foo": "from jsreport command line"
  }
}
```

Additionally you can use `--template` option to specify report template separately. The request is then merged from both `--render` and `--template` values.

>jsreport.exe --render request.json --template tmpl.json --output.pdf

Example `tmpl.json`:
```js
{
  "content": "Template World {{:foo}}",
  "recipe": "phantom-pdf",
  "engine": "jsrender"
}
```

