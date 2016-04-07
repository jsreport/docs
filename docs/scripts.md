> Run custom javascript to modify report inputs/outputs or to send a report

##Basics

Define global functions `beforeRender`  or (and) `afterRender` in script and use global variables `request` and `response` and `reporter` to reach your needs. You need to call `done()` at the end because script can be async. 

```js
//load some data from the remote API
function beforeRender(done) {
    require('request')({ 
      url:"http://service.url", 
      json:true 
    }, function(err, response, body){
        request.data = body;
        done();
    });
}

//send the pdf report by mail
function afterRender(done) {
  var SendGrid = require('sendgrid');
  var sendgrid = new SendGrid('username', 'password');

  sendgrid.send({ to: '',  from: '', subject: '',
          html: 'This is your report',
          files: [ {filename: 'Report.pdf', content: new Buffer(response.content) }]
  }, function(success, message) {          
          done(success);
  });
}
```

##request, response, reporter

`request.template` - modify report template, mostly `content` and `helpers` attributes
`request.data` - json object used to modify report input data
`request.headers` - json object used to read input headers

`response.content` - byte array with output report
`response.headers` - output headers

`reporter.render(req, cb)` - invoke rendering process of another template

##Multiple scripts
You can associate multiple scripts to the report template. The scripts are then serially executed one after one in the order specified in the jsreport studio.

##phantom-pdf note
Please note that some recipes like [phantom-pdf](/learn/phantom-pdf) are invoking the whole rendering process for the main page and also for headers and footers. This causes the custom script to be invoked multiple times - for main page, header and footer. To determine calls from header or footer use `request.options.isChildRequest` property.

```js
function afterRender(done) {
    //filter out script execution for phantom header
    if (request.options.isChildRequest)
      return done();

    //your script
    done();
}
```

##Rendering another template from script

Script can be used also to invoke rendering of another template. 
```
function afterRender(done) {
    reporter.render({ template: { shortid: "EyeVA2_k-" }}, function(err, res) {
        if (err)
            return done(err);
        
        response.headers = res.headers;
        response.content = res.content;
        done();
    });    
}
```

##Configuration

Add `scripts` node to the standard [config](https://github.com/jsreport/jsreport/blob/master/config.md) file. The defaults are following.
```js
scripts: {
  timeout: 30000
  allowedModules: ["handlebars", "request-json", "feedparser", "request", "underscore", "constants", "sendgrid"]
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

