> Install-Package [jsreport.Embedded](https://www.nuget.org/packages/jsreport.Embedded)

##Basics

jsreport can be easily installed using [nuget package](https://www.nuget.org/packages/jsreport.Embedded) into c# project and run with the same lifecycle as the orignal .NET process. Everything needed is inside a package and no node.js or any other dependency installation is required.

When you have nuget package installed you can just create `EmbeddedReportingServer`, start it and invoke pdf rendering using [c# .NET client](http://jsreport.net/learn/net-client) as there would be full jsreport running somewhere.

```c#
// somewhere in application startup....
var embeddedServer = new EmbeddedReportingServer(port: 2000);
await embeddedServer.StartAsync();

// local ReportingService ready to be used...
var result = await embeddedServer.ReportingService.RenderAsync(new RenderRequest()
{
    template = new Template()
    {
        content = "<h1>Hello from {{greetings}}</h1>",
        recipe = "phantom-pdf",
        engine = "handlebars"        
    },
    data = new {
	    greetings = ".NET embedded jsreport"
    }
});

using (var fileStream = File.Create("C:\\temp\\report.pdf"))
{
    result.Content.CopyTo(fileStream);
}
```

##Project structure
The nuget installation creates `jsreport` directory with the following structure.

![project structure](http://jsreport.net/screenshots/embedded-project.png?v=2)

**`app`** - jsreport configuration, startup script and also the local dev installation    
**`reports`** -  report templates and other jsreport object definitions    
**`jsreport.zip`** - zipped jsreport from `app` folder with `node.exe` included. This is then taken form the bin folder, decompressed and started through `embeddedServer.StartAsync`.    
**`studio.cmd`** - windows batch starting jsreport local development server    
**`install.cmd`** - used by nuget to install jsreport into `app` folder and zip it into  `jsreport.zip`    
**`update.cmd`** - update jsreport local installation and pack it into `jsreport.zip`    

Most often you should ignore from the source control `app/node_modules` and `jsreport.zip`, because these are always created during nuget install.

However if you are not interested in designing report templates and using local jsreport studio, you can delete completely everything except `jsreport.zip`. In this case nuget will skip any additional installation.

##Report studio
jsreport comes with handy report studio which you can use to develop your report templates. 

![project structure](http://jsreport.net/screenshots/vs-studio.gif)

1. Open the studio using `studio.cmd`
2.  Then you can use studio to create new report templates and other report objects. 
3. The new report templates are persisted in `jsreport\reports` directory. You need to manually include the new folders in the solution and set the build action to `Copy to output`.
4. You can also use the Visual Studio editor to directly edit the templates. Just open the jsreport studio, navigate to particular template and try to edit it in the VS in the background. **You can see the jsreport studio automatically refreshes and re-renders the report after the change in VS.**
5. Finally you can render the prepared templates using the following code

```cs
embeddedServer.ReportingService.RenderAsync(new {
    template = new {
        name = "Sample template" 
    },
    data = new {
	    greetings = ".NET embedded jsreport"
    }
});
```
 

##Securing embedded server
You can simply secure the jsreport embedded server from the outside world by the firewall rule. However if you want to secure also the local traffic, you can easily enable the authentication extension: 

1.
Edit `jsreport\app\prod.config.json`
```js
"authentication" : {
    "cookieSession": {
        "secret": "dasd321as56d1sd5s61vdv32"        
    },
    "admin": {
        "username" : "admin",
        "password": "password"
    }
}
```
2.
And now create `EmbeddedReportingServer` with the specified credentials
```cs
new EmbeddedReportingServer(port: 2000) {
	Username = "admin",
	Password = "password"
}	
```


##Additional extensions
There are already [more than 40 jsreport extensions](https://github.com/jsreport/jsreport-core#list-of-extensions) which can be additionally installed. Example installing [wkhtmltopdf recipe](http://jsreport.net/learn/wkhtmltopdf).

1.
Navigate to `jsreport/app` and run
```sh
"../../.bin/npm" install jsreport-wkhtmltopdf --production --save
```
2.
Start jsreport studio using `start.cmd`. You should find `wkhtmltopdf` in the list of recipes

3.
Finally run `install.cmd` to pack the extended `app` into `jsreport.zip`

4.
Now you can easily run `wkhtmltopdf`  recipe from the c#
```cs
embeddedServer.ReportingService.RenderAsync(new {
{
    template = {
        content = "Hello world from wkhtmltopdf",
        engine = "handlebars",
        recipe = "wkhtmltopdf"
    }
});
```

##Manage jsreport version

 Installing `jsreport.Embedded` nuget to the empty project always installs the latest [jsreport from the npm](https://www.npmjs.com/package/jsreport). However restoring nuget package installs the same version as previously installed and mentioned in the file `jsreport/app/package.json`. This assures jsreport version to be consistent. 

Updating jsreport can be done using `update.cmd` batch or simply updating the version manually in `package.json` and calling `install.cmd`.
