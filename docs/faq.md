How to...

**General questions**
- [Update existing jsreport server to the latest version](#update-server)    
- [Migrate templates from the test to the production server](#migrate-templates)    
- [Run jsreport on different port](#port-config)
- [Increase performance](#performance)    
- [Windows deployment](#windows-deployment)    

**Phantom pdf recipe**
- [National characters are not displayed properly](#national-characters)    
- [Highcharts are not printed into pdf properly](#highcharts)    


#### <a name="update-server"></a>Update existing jsreport server to the latest version

Updating is very simple using [npm](https://www.npmjs.com/). Just navigate to your application directory and type:

`npm update`

#### <a name="migrate-templates"></a>Migrate templates from the test to the production server

jsreport stores by default templates in the `application\data` folder. To migrate templates you can just grab content of this folder and copy it to the production server.

#### <a name="port-config"></a>Run jsreport on different port

You need to open `prod.config.json` file and edit `httpsPort` property to desired value. For details please explore [configuration documentation](/learn/configuration).

#### <a name="performance"></a>Increase performance

jsreport uses by default dedicated processes for rendering pdf or scripts.  This solution works better in some cloud and corporate environments with proxies. However for other cases it is better to reuse phantomjs and nodejs workers over multiple requests.

Open `prod.config.json` and update following:

```js
"phantom": {     
	"strategy": "phantom-server"
},
"tasks": {       
	"strategy": "http-server"
}
```

See [configuration documentation](/learn/configuration) for details.

#### <a name="windows-deployment"></a>Windows deployment

Windows has limitation for maximum file path set to 256 characters. This is unfortunately not enough for node.js based application and it doesn't let you to easily copy paste jsreport to your production servers.  You can still use npm to deploy jsreport application but you may also like the following workaround which allows you to copy paste application without using npm:

1. use [npm shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap) to prune the duplicate packages in `node_modules` folder
2. then [flatten-packages](https://www.npmjs.com/package/flatten-packages) to limit the directory tree depth
3. add `node.exe` to jsreport application path
4. zip or just copy paste jsreport to you production servers

#### <a name="national-characters"></a>National characters are not displayed properly

You need to add proper charset to the html head meta:
```html
<html>
  <head>
    <meta content="text/html; charset=utf-8" http-equiv="Content-Type">
  </head>
  <body>
     ....
  </body>
</html>
```

For details check *National characters* section in the [documentation](/learn/phantom-pdf).

#### <a name="highcharts"></a>Highcharts are not printed into pdf properly

[Highcharts](http://www.highcharts.com/) are using complex animations by default preventing proper pdf printing. To go through it you need to disable highcharts animation like shown on [playground example](https://playground.jsreport.net/#playground/e77zeliQO/2).

