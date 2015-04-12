How to...

**General questions**
- [Update existing jsreport server to the latest version](#update-server)
- [Migrate templates from the test to the production server](#migrate-templates)
- [Run jsreport on different port](#port-config)

**Phantom pdf recipe**
- [National characters are not displayed properly](#national-characters)


#### <a name="update-server"></a>Update existing jsreport server to the latest version

Updating is very simple using [npm](https://www.npmjs.com/). Just navigate to your application directory and type:

`npm update`

#### <a name="migrate-templates"></a>Migrate templates from the test to the production server

jsreport stores by default templates in the `application\data` folder. To migrate templates you can just grab content of this folder and copy it to the production server.

#### <a name="port-config"></a>Run jsreport on different port

You need to open `prod.config.json` file and edit `httpsPort` property to desired value. For details please explore [configuration documentation](/learn/configuration).

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

