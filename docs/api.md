## Basics
You can communicate with jsreport using it's HTTP based API. jsreport will provide some wrappers for the most popular languages but using it directly is very easy when jsreport client wrapper does not exist for your language. This article is about using jsreport API in it's raw natural form.

jsreport API can be split into two different use cases:

1. Rendering reports - use it when you need to invoke rendering process
2. Querying and CRUD - use it when you want to maintain templates, do exports, etc

Next two sections are describing these use cases in detail.

>Remember that you should send body in json format. Content negotiation is supported for some parts of API, but anyway it's not recommended since jsreport is running in javascript.

## Rendering report
Invoking rendering process is the most common API method you will call. The next snippet shows the service enpoint url as well as the body schema. Options and data fields are optional.

> `POST:` https://jsreport-host/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js
   {
      "template": { "shortid" : "g1PyBkARK"  },
      "data" : { ... },
      "options": { "timeout": 60000 }
   }
>```

In the most typical case, you will just put your template's **shortid** or template's **name** and input data. The best way to find out the template's shortid and to get other information is to use API button in the template designer, also known as **jsreport studio**. This button will popup dialog with very useful information for a particular template you should have in order to render it using HTTP API.

![API dialog](https://jsreport.net/screenshots/API.png?v=2)


> See the POST response headers to get useful information about report like content type. for example the response of a pdf report will have `application/pdf` as its content type

In the advanced scenario where you have some kind of a dynamic template that is not stored by jsreport, then you can remove `shortid` or `name` properties from the request and fill the template attributes manually as you want. Look at the following snippet rendering simple hello world from raw template representation in request.

> `POST:` https://test.jsreportonline.net/api/report
<br/>
> `Headers`: Content-Type: application/json
<br/>
> `BODY:`
>```js
   {
      "template": { "content" : "Hello world", "recipe" : "chrome-pdf" },
   }
>```

## Content-Disposition and report name

The most easy way to change report name is using `options[reportName]`

> `BODY:`
>```js
   {
      "template": { ... },
      "options": { "reportName": "myreport" }
   }
>```

in this case the file extension of the output will added by jsreport depending on the content type of the report that is being generated, for example if you are rendering a template with `chrome-pdf` recipe then the output full name with these options will be "myreport.pdf".

additionally you can have more control in the response by using the `options['Content-Disposition']` to override the jsreport response with particular document name and file extension.

> `BODY:`
>```js
   {
      "template": { ... },
      "options": {  "Content-Disposition": "attachment; filename=myreport.pdf" }
   }
>```

## Querying and CRUD

Querying and CRUD API in jsreport is based on [OData](http://www.odata.org) protocol. You can use it to query or CRUD any entity jsreport server contains. For example, to get list of all jsreport entities, you can call standard odata notation endpoint.

> `GET:` https://jsreport-host/odata/$metadata

Or to get list of all template names:

> `GET:` http://jsreport-host/odata/templates?$select=name

OData protocol has client libraries for various languages and you should be able to find a wrapper for yours [here](http://www.odata.org/libraries).

## Authentication

When jsreport server has [authentication extension](/learn/authentication) enabled or when using [jsreportonline](/online) you need to add authentication header to all API requests. The header is based on [basic http authentication](http://en.wikipedia.org/wiki/Basic_access_authentication) so you should be able to authenticate easily from any platform.

`Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`

Where the hash is based on username and password:  
`base64(username:password)`

>Check [authentication extension docs](/learn/authentication) for more details about different authentication options
