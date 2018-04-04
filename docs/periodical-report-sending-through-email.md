You learned in the [get started](/learn/get-started) article how you can design an invoice template with a sample data inside jsreport studio and later push the real data into the server using API and get out the pdf. This maybe the most common approach for generating static documents in jsreport but definitely not the only one. Another approach next to pushing data into API is to let jsreport obtain required data on it's own and also deliver generated report itself. This makes jsreport server nicely loosely coupled and assuming the most of the applications already have an API there is no integration work needed. The missing peace in this strategy is how to set up a report template to automatically obtain data from your application and deliver the output report.

jsreport comes out of the box with pre-installed [scripts extension](/learn/scripts). This extension lets you to write a custom javascript functions and modify the report rendering inputs as well as operate on the report rendering outputs. The custom javascript function has no limits except some security considerations and you can use it for example to get input data from another web service and send email with the output report. This perfectly fits into the  previously mentioned strategy and you can really use scripts extension to fulfill it.

## Example

As the example for this let's make a report which will request input data from an external service, render a pdf and send the result through the email.  As the external service we use public available JSON REST service [http://jsonplaceholder.typicode.com/posts](http://jsonplaceholder.typicode.com/posts) which provides us some sample data.

### Request input data
The first thing to do is create a script which will be used to request data from the external API and later also send the output report. You can create a script from the jsreport studio in the left entity tree panel.

![script](https://jsreport.net/screenshots/script.png)

Tasks like preparing report input data should be implemented in the script's global function called `beforeRender`. We can use pre-installed [request](https://github.com/request/request) node.js module  to collect data from the external API for this case. Following snippet simply requests specified url and add parsed JSON response into the rendering request inputs.

```js
function beforeRender(req, res, done) {
    require('request')({
      url:"http://jsonplaceholder.typicode.com/posts",
      json:true
    }, function(err, response, body){
        req.data = { posts: body };
        done();
    });
}
```

Additionally to the script creation, you need to make sure [request](https://github.com/request/request) is locally installed. This is done with command:
>npm install request --save

And secondly you need to enable this module in the jsreport configuration.
```js
{
  "extensions": {
    "scripts": {
      "allowedModules": ["request"]    
    }
  }
}
```

### Define a template
Now it's time to create a report template and associate previously created script with it.

![select script](https://jsreport.net/screenshots/select-script.png)

The template has now the input data filled from the custom script. We can iterate over it and create a simple pdf.

<iframe src='https://playground.jsreport.net/studio/workspace/r1G-1ULP/3?embed=1' width="100%" height="400" frameborder="0"></iframe>


### Send output in mail
The next thing to do is to automate the process of sending the output pdf through email. This can be achieved in the original script by adding `afterRender` global function which will be invoked when the output report is already prepared. To send an email you can use for example  [nodemailer](https://github.com/nodemailer/nodemailer) node.js module. Note you need to again install it and enable it in the jsreport config.


```js
function afterRender(req, res, done) {
    var mailer = require("nodemailer");

    var smtpTransport = mailer.createTransport('smtps://user%40gmail.com:pass@smtp.gmail.com');

    var mail = {
        from: "Jan Blaha <test@gmail.com>",
        to: "foo@gmail.com",
        subject: "Sending email from node.js",
        text: "See the attached report",
        html: "<b>See the attached report</b>",
        attachments: [
        {  
            filename: 'Report.pdf',
	        content: new Buffer(res.content)
        }],
    }

    smtpTransport.sendMail(mail, function(error, response){
        smtpTransport.close();
        if(error){
            return done(error);
        }

        return done();
    });
}
```

Now every time you render the template you should also receive an email with attached pdf.

## Scheduling
You should have now a report template which automatically downloads data from an external REST API and send the output report through email. This is nice but you still need to trigger the rendering through API. In many cases this won't be necessary if you need to send the report periodically. In this cases you can use [sheduling](/learn/scheduling) extension to plan report rendering directly in jsreport. Then you don't need to integrate with jsreport at all and let it run as a standalone reporting server.

To plan a periodical report rendering you should create a schedule, associate the template and specify its time plan using CRON expression.

![schedule](https://jsreport.net/screenshots/schedule.png)

## Conclusion
Now you have a self sufficient independent report which runs periodically on its own, downloads data and also distributes the output. All of this was done without touching your application or granting special access to the database. This usage makes jsreport fully capable reporting server.
