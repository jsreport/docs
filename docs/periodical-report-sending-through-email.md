
You learned in the [get started](/learn/get-started) article how you can design an invoice template with sample data inside the jsreport studio and later push the real data into the server using API and get out the pdf. This may be the most common approach in jsreport but not the only one. Another approach next to pushing data into API is to let jsreport obtain required data on its own and also deliver generated report itself. This makes jsreport server nicely loosely coupled and assuming most of the applications already have an API there is no integration work needed. The missing piece in this strategy is how to set up a report template to automatically obtain data from your application and deliver the output report.

jsreport comes out of the box with pre-installed [scripts extension](/learn/scripts). This extension lets you write a custom javascript functions and modify the report rendering inputs as well as operate on the report rendering outputs. The custom javascript function has no limits except some security considerations and you can use it for example to get input data from another web service or any kind of database and send an email with the output report. This perfectly fits into the previously mentioned strategy and you can use scripts extension to fulfill it.

## Example

As an example for this let's make a report which will request input data from an external service, render a pdf and send the result through an email.  As the external service, we use public available JSON REST service [http://jsonplaceholder.typicode.com/posts](http://jsonplaceholder.typicode.com/posts) which provides us some sample data.

### Request input data
The first thing to do is create a script which will be used to request data from the external API and later also sends the output report. You can create the [script](/learn/scripts) from the jsreport studio in the left entity tree panel.<br>
Right-click the panel -> new entity -> script.

![script](https://jsreport.net/img/script-create.png)

Tasks like preparing report input data should be implemented in the script's global function called `beforeRender`. Doing http calls to fetch the input data in raw nodejs can be tedious, so let's use a custom library [axios](https://github.com/axios/axios). The script loading data and assigning the data to the report input can look like this.

```js
const axios = require('axios')

async  function beforeRender(req, res)  {
  const resData =  await axios.get('http://jsonplaceholder.typicode.com/posts')
  console.log(resData.data)
  req.data.posts = resData.data
}
```
To make this script running properly you need to make sure `axios` module is installed inside the jsreport app. This is done with the command:

```sh
npm install axios --save-exact
```

And you need to also make sure the jsreport is [configured](/learn/configuration) with access to the local files and modules in `jsreport.config.json`. Note you need to restart jsreport after the config changes.
```js
{
  "allowLocalFilesAccess": true
}
```

### Define a template
Now it's time to create a report template. <br>
Right-click the entity tree panel -> new entity -> template

And associate the previously created script with it.

![select script](https://jsreport.net/img/script-select.png)

The template has now the input data filled from the custom script. We can iterate over it and create a simple pdf with `Run` button or `F8`.

```html
{{#each  posts}}
<div>
  <h2>{{title}}</h2>
  <div>{{body}}</div>
</div>
{{/each}}
```

> Tip: You can use the `Debug` button to output the report logs which includes also script `console.log` calls.

See a life example in the playground:
<iframe src='https://playground.jsreport.net/w/anon/hlybQAl8?embed=1' width="100%" height="400" frameborder="0"></iframe>

> Tip: You can easily export workspace from the playground and import it to your local jsreport instance. Just open the entity tree panel in playground and click the `Export` button.

### Send output in mail
The next thing to do is to automate the process of sending the output pdf through email. This can be achieved in the original script by adding `afterRender` global function which will be invoked when the output report is already prepared. To send an email you can use for example  [nodemailer](https://github.com/nodemailer/nodemailer) node.js module. 

```sh
npm i nodemailer --save-exact
```

You can use [ethereal.email](https://ethereal.email/) email service to easily test your emails the first. It even gives you [nodemailer](https://github.com/nodemailer/nodemailer) initialization snipped. The whole `afterRender` hook sending an email can in the end look like this.

```js
const nodemailer = require('nodemailer')
async function afterRender(req, res) {
    const transporter = nodemailer.createTransport({
      host: 'smtp.ethereal.email',
      port: 587,
      auth: {
          user: 'xxx',
          pass: 'yyy'
      }
    })  

    await transporter.sendMail({
      from: "Jan Blaha <test@gmail.com>",
      to: "foo@gmail.com",
      subject: "Sending email from node.js",
      text: "See the attached report",
      html: "<b>See the attached report</b>",
      attachments: [
      {  
        filename: 'Report.pdf',
        content: Buffer.from(res.content)
      }],
    })       
}
```

Now every time you render the template you should also receive an email with the attached pdf.

## Scheduling
You should now have a report template that automatically downloads data from an external REST API and sends the output report through email. This is nice but you still need to trigger the rendering through API. In many cases, this won't be necessary if you need to send the report periodically. In this case, you can use [sheduling](/learn/scheduling) extension to plan the report rendering directly in the jsreport. Then you don't need to integrate with jsreport at all and let it run as a standalone reporting server.

To plan a periodical report rendering you should create a schedule, associate the template, and specify its time plan using CRON expression.<br>
Right-click the entity tree panel -> new entity -> schedule

![schedule](https://jsreport.net/img/schedule.png?v2)

## Conclusion
Now you have a self-sufficient independent report which runs periodically on its own, downloads data, and also distributes the output. All of this was done without touching your application. This was just an example but you can connect jsreport with a script to any kind of database or distribute the output to any kind of service. You can for example connect to a SQL database, read input data, and upload the result to the AWS S3 bucket.
