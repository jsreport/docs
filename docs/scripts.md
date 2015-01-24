> Run custom javascript to modify report inputs/outputs or to send a report

##Basics

Define global functions `beforeRender`  or (and) `afterRender` in script and use global variables `request` and `response` to reach your needs. You need to call `done()` at the end because script can be async. 

```js
//load some data from the remote API
function beforeRender(done) {
  require('request')({ url: 'http://someservice.url', json:true })(err, body, response) {
    request.template.data = body;
    done();
  }
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

##request, response

`request.template` - modify report template, mostly `content` and `helpers` attributes
`request.data` - json object used to modify report input data
`request.headers` - json object used to read input headers

`response.content` - byte array with output report
`response.headers` - output headers

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

A custom script is usually linked to the report template by its shortid. In this case template structure with the report looks following in json:

```js
  template : {
	  content: "foo",
	  script: {
		  shortid: "sd543fds"		  
	  }
  }
```  

Scripts extension also supports inline scripts directly in the `Template` entity. This is used in [embedding extension](/learn/embedding) and you can use it also when you want to render a custom report from the API. 

Inline script looks following in json:

```js
  template : {
	  content: "foo",
	  script: {
		  content: "request.template.content='hello'; done();"		  
	  }
  }
```


##Using script to load data
Some people prefer to push data into jsreport from the client and some people prefer to use `scripts` extension to actively fetch them. Where pushing data to the jsreport is more straight forward, using `scripts` can provide better architecture with fully separated reporting server where report itself is responsible for defining input as well as fetching them. The choice is up to you. 


##Quick links
> **[Sending reports periodically in email article](http://jsreport.net/blog/sending-reports-periodically-in-email)**

> **[Downloading report data using script in playground](https://playground.jsreport.net/#/playground/lyWJuycgAc)**
