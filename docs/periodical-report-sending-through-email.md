You learned in the [get started](/learn/get-started) article how you can design an invoice template with a sample data inside jsreport studio and later push the real data into the server using API and get out the pdf. This maybe the most common approach for generating static documents in jsreport but definitely not the only one. Another approach next to pushing data into API is to let jsreport obtain required data on it's own and also deliver generated report itself. This makes jsreport server nicely loosely coupled and assuming the most of the applications already have an API there is no integration work needed. The missing peace in this strategy is how to set up a report template to automatically obtain data from your application and deliver the output report.

jsreport comes out of the box with pre-installed [scripts extension](/learn/scripts). This extension lets you to write a custom javascript functions and modify the report rendering inputs as well as operate on the report rendering outputs. The custom javascript function has no limits except some security considerations and you can use it for example to get input data from another web service and send email with the output report. This perfectly fits into the  previously mentioned strategy and you can really use scripts extension to fulfill it.

##Example

As the example for this let's make a report which will request input data from an external service, render a pdf and send the result through the email.  As the external service we use public available JSON REST service [http://jsonplaceholder.typicode.com/posts](http://jsonplaceholder.typicode.com/posts) which provides us some sample data.

###Request input data
The first thing to do is create a script which will be used to request data from the external API and later also send the output report. You can create script from the jsreport studio in the common way using `ACTIONS/Create Script` menu.

Tasks like preparing report input data should be implemented in the script's global function called `beforeRender`. We can use pre-installed [request](https://github.com/request/request) node.js module  to collect data from the external API for this case. Following snippet simply requests specified url and add parsed JSON response into the rendering request inputs.

```js
function beforeRender(done) {
  require('request')(
    {
      url: 'http://jsonplaceholder.typicode.com/posts',
      json:true
    })(err, body, response) {
      request.template.data = { posts: body };
      done();
    }
}
```

###Define a template
Now it's time to create a report template and associate previously created script with it. You can do it again from the jsreport studio using `ACTIONS/Create Template` menu and then associate the script in the designer's left panel.

It's convenient to explore the input data before actually designing the template. I do it personally with a custom helper function which "stringifies" the input data. Then I put the helper function output somewhere at the top of the template. Run the template and then copy paste the output into JSON [online parser](http://json.parser.online.fr/).

In [jsrender](/learn/jsrender) it looks like this:

```js
function log(data) {
	return JSON.stringify(data)
}
```

```html
{{:~log(#data)}}
```

Doing this you can explore the input data structure and bind it to the report template design. Designing the report template is not in the scope of this example because it was already explained previously in the [get started](/learn/get-started) article.

###Send output in mail
The report template should be now finished and the output report nicely visualized. The next thing to do is to automate the process of sending the output pdf through email. This can be achieved in the original script by adding `afterRender` global function which will be invoked when the output report is already prepared. To send an email you can use for example  [SendGrid](https://github.com/sendgrid/sendgrid-nodejs) node.js module.
```js
function afterRender(done) {
  var SendGrid = require('sendgrid');
  var sendgrid = new SendGrid('username', 'password');

  sendgrid.send({ to: '',  from: '', subject: '',
          html: 'This is your report',
          files: [ {
	          filename: 'Report.pdf',
	          content: new Buffer(response.content) } ]
	  }, function(success, message) {
          done(success);
	  });
}
```

Now every time you render the template you should also receive an email with attached pdf. You can do it by rendering the template from inside jsreport designer or in [playground  example](https://playground.jsreport.net/#playground/bk1twCWht/8).

##Scheduling
You should have now a report template which automatically downloads data from an external REST API and send the output report through email. This is nice but you still need to trigger the rendering through API. In many cases this won't be necessary if you need to send the report periodically. In this cases you can use [sheduling](/learn/scheduling) extension to plan report rendering directly in jsreport. Then you don't need to integrate with jsreport at all and let it run as a standalone reporting server.

To plan a periodical report rendering you should create a Schedule first using `ACTIONS/Create Schedule` menu. Then you should specify which report should be rendered and a period definition using CRON expression.

##Conclusion
Now you have a self sufficient independent report which runs periodically on its own, downloads data and also distributes the output. All of this was done without touching your application or granting special access to the database. This usage makes jsreport fully capable reporting server.