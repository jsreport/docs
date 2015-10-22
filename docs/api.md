##Basics
You can communicate with jsreport using it's REST based API. jsreport will provide some wrappers for the most popular languages but using it directly is very easy when jsreport client wrapper does not exist for your language. This article is about using jsreport API in it's raw natural form.

jsreport API can be split into two different use cases:

1. Rendering reports - use it when you need to invoke rendering process
2. Querying and CRUD - use it when you want to maintain templates, do exports ...

Next two sections are describing these use cases in detail.

>You should sent body in json format. Content negotiation is supported for some parts of API, but anyway it's not recommended since jsreport is running in javascript.

## Rendering report
Invoking rendering process is the most common API method you will call. The next snippet shows the service enpoint url as well as the body schema. Options and data fields are optional.

> `POST:` https://jsreport-host/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js 
   { 
      "template": { "shortid" : "g1PyBkARK"  },
      "data" : { ... },
      "options": { timeout: 60000 }
   } 
>```

In the most typical case, you will just insert your template **shortid** or template **name** and input data. The best way to find out shortid and to get other informations is to use API button in the template designer. This button will popup dialog with very useful information for the particular template you should have in order to render it using API.

![API dialog](http://jsreport.net/screenshots/API.png)


> See the POST response headers to get usefull informations about report like content type.

In the advanced scenario where you have some kind of a dynamic template, you can remove `shortid` or `name` identification from the request and fill the template attributes manually as you want. Look at the following snippet rendering simple hello world.

> `POST:` https://test.jsreportonline.net/api/report
<br/>
> `Headers`: Content-Type: application/json
<br/>
> `BODY:`
>```js 
   { 
      "template": { "content" : "Hello world", "recipe" : "phantom-pdf" },
   } 
>```

##Content-Disposition and report name

Use the `options['Content-Disposition']` to override the jsreport response with particular document name.
> `BODY:`
>```js 
   { 
      "template": { ... },
      "options": {  "Content-Disposition": "attachment; filename=myreport.pdf" }
   } 
>```

## Querying and CRUD

Querying and CRUD API in jsreport is based on [odata](http://www.odata.org) protocol. You can use it to query or CRUD any object jsreport server contains. For example, to get list of all jsreport entities, you can call standard odata notation endpoint.

> `GET:` https://jsreport-host/odata/$metadata

Or to get list of all template names:

> `GET:` http://jsreport-host/odata/templates?$select=name

Odata protocol has client libraries for various languages and you should be able to find a wrapper for yours [here](http://www.odata.org/libraries).

## Authentication

When jsreport server has [authentication extension](/learn/authentication) enabled or when using [jsreportonline](/online) you need to add authentication header to all API requests. The header is based on [basic http authentication](http://en.wikipedia.org/wiki/Basic_access_authentication) so you should be able to authenticate easily from any platform. 



`Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`

Where the hash is based on username and password:  
`base64(username:password)`




