> Run custom javascript to modify report inputs/outputs or to send a report

##Basics

Define global functions `beforeRender`  or (and) `afterRender` in script and use parameters  `req` and `res` to reach your needs. You need to call `done()` at the end because script can be async. 

```js
function beforeRender(req, res, done) {
  req.data = {
    foo: "foo"
  }
  done()
}
```

##Use external modules

You can `require` external modules in the node.js way, however you need to first enable them in the config. 
```js
{
  "scripts": {
    "allowedModules": ["request"]
  }
}
```

Alternatively you can enable all modules using `scripts.allowedModules="*"`.


The following example uses popular [request](https://github.com/request/request) module to make a rest call and [sendgrid](https://github.com/sendgrid/sendgrid-nodejs) service to email the output service.

```js
//load some data from the remote API
function beforeRender(req, res, done) {
    require('request')({ 
      url:"http://service.url", 
      json:true 
    }, function(err, res, body){
        req.data = body;
        done();
    });
}

//send the pdf report by mail
function afterRender(req, res, done) {
  var SendGrid = require('sendgrid');
  var sendgrid = new SendGrid('username', 'password');

  sendgrid.send({ to: '',  from: '', subject: '',
          html: 'This is your report',
          files: [ {filename: 'Report.pdf', content: new Buffer(res.content) }]
  }, function(success, message) {          
          done(success);
  });
}
```

##request, response, reporter

`req.template` - modify report template, mostly `content` and `helpers` attributes
`req.data` - json object used to modify report input data
`req.headers` - json object used to read input headers

`res.content` - byte array with output report
`res.headers` - output headers

`req.reporter.render(request, cb)` - invoke rendering process of another template

##Multiple scripts
You can associate multiple scripts to the report template. The scripts are then serially executed one after one in the order specified in the jsreport studio.

##Global scripts
You can set up a script to run for every single template by adding flag `isGlobal`. This can be done for example in jsreport studio script's properties. Global script can be useful for common tasks like adding common helpers or settings to templates.

##phantom-pdf note
Please note that some recipes like [phantom-pdf](/learn/phantom-pdf) are invoking the whole rendering process for the main page and also for headers and footers. This causes the custom script to be invoked multiple times - for main page, header and footer. To determine calls from header or footer use `req.options.isChildRequest` property.

```js
function afterRender(req, res, done) {
    //filter out script execution for phantom header
    if (req.options.isChildRequest)
      return done();

    //your script
    done();
}
```

##Rendering another template from script

Script can be used also to invoke rendering of another template. 
```
function afterRender(req, res, done) {
    req.reporter.render({ template: { shortid: "EyeVA2_k-" }}, function(err, response) {
        if (err)
            return done(err);
        
        res.headers = response.headers;
        res.content = response.content;
        done();
    });    
}
```

##Configuration

Add `scripts` node to the standard config file:

```js
scripts: {
  timeout: 30000,
  allowedModules: "*"  
}
```

##API
You can use standard OData API to manage and query script entities. For example you can query all scripts using
> `GET` http://jsreport-host/odata/scripts

A custom script is physically linked to the stored report template using its shortid or name. In this cases there is nothing to do in the API call, because the script is automatically applied.

```js
POST: { template: { name: 'My Template with script' }, data: { }
```

If you are rendering anonymous template you can identify the script by its name or shortid

```js
POST: {
  template : {
	  content: "foo",
	  scripts: [{
		  shortid: "sd543fds"		  
	  }, {
		  name: "My Script"  
	  }]  	
  }
}  
```

The last option is to specify the script content directly
```js
POST: { 
  template : {
	  content: "foo",
	  scripts: [{
		  content: "request.template.content='hello'; done();"           
	  }]  	
  }
}  
```


##Using script to load data
Some people prefer to push data into jsreport from the client and some people prefer to use `scripts` extension to actively fetch them. Where pushing data to the jsreport is more straight forward, using `scripts` can provide better architecture with fully separated reporting server where report itself is responsible for defining input as well as fetching them. The choice is up to you. 


##Quick links
> **[Sending reports periodically in email article](http://jsreport.net/blog/sending-reports-periodically-in-email)**

> **[Downloading report data using script in playground](https://playground.jsreport.net/#/playground/lyWJuycgAc)**

