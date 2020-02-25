

> jsreport provides plain HTTP rest based API for rendering and CRUD

## Basics

jsreport API can be split into the two main use cases:

1. Rendering reports - use it when you need to invoke the report rendering process
2. Querying and CRUD - use it in more complex jsreport integration in which you want to update or synchronize entities using API

The following two sections are describing these main use cases in detail.

> The jsreport studio uses the same API exposed to you. In case you are missing something in the documentation you can open F12 browser tools and see what is happening in the network tab.

## Rendering report

Invoking report rendering process is the most commonly used API method. The next snippet shows the service endpoint url as well as the body schema. Options and data fields are optional.

> `POST:` https://jsreport-host/api/report<br/>
> `Headers`: Content-Type: application/json<br/>
> `BODY:`
>```js
   {
      "template": { "name" : "my template"  },
      "data" : { ... },
      "options": { "reports": { "save": true } }
   }
>```

In the most typical case, you just need to specify template **name** (or shortid) and input data. The template **name** property needs to be unique template name. In the case you use multiple folders it's recommended to pass full path instead of the name. For example   
```js
{
  "template": { "name": "/myfolder/mytemplate" }"
  "data": {}
}
```

You may want to override just some attributes of the template. This is easy because all the request attributes are merged into the stored template before evaluation. If you want to for example just change the template recipe to `html` you can do it with the following request body.

```js
{
	"template": { 
		"name": "myTemplate",
		"recipe": "html"
	  },
	"data" : { ... },
}
```

The template doesn't necessarily need to be stored inside jsreport [templates store](/learn/template-stores) and it can be fully defined in the request body. In this case, you need to specify at least required attributes `recipe`,  `engine` and `content`. The following snippet shows how to define such a request body.

```js
{
	"template": { 
		"content" : "Hello world {{name}}",
		"recipe": "chrome-pdf",
		"engine": "handlebars",
		"chrome": {
			"landscape": true
		}
	},
	"data" : { ... },
}
```

The valid template properties can be found in the following ways:
- using API dialog which you can open through studio settings
- using odata metadata definition which you can get from http://jsreport-host/odata/$metadata
- using F12 browser tools when running the rendering request from the studio

### Content-Disposition and report name

You can change the `Content-Disposition` response header thus the file name browser displays during download using the request property  `options[reportName]`

```js
{
      "template": { ... },
      "options": { "reportName": "myreport" }
}
```

The file extension adds jsreport depending on the content type of the report being generated. For example `chrome-pdf`produces "myreport.pdf" in this case.

In case you want full control over this response header you can specify `options['Content-Disposition']`  in the request body and override the defaults.

### Timeout

The rendering timeout specified in the configuration `reportTimeout` can be overridden in the API call. This possibility needs to be opted in using configuration `enableRequestReportTimeout=true`. Then use `options.timeout` in the request body.

```js
{
      "template": { ... },
      "options": { "timeout": 30000 }
}
```

## Querying and CRUD

Querying and CRUD API in jsreport is based on [OData](http://www.odata.org) protocol. You can use it to query or CRUD programmatically any entity that jsreport server contains. The Odata defines some basic standards jsreport follows, here are some examples of the most common calls.

### Query

Get all entity types
> `GET:` https://jsreport-host/odata/$metadata

Get list of templates' names
> `GET:` http://jsreport-host/odata/templates?$select=name

Get a script with specific name
> `GET:` http://jsreport-host/odata/scripts?$filter=name eq 'myscript'

Get an assets in a specific folder
> `GET:` http://jsreport-host/odata/assets?$filter=folder/shortid eq 'abc'

### CRUD 

Create a template
> `POST`: http://jsreport-host/odata/templates
<br/>
> `Headers`: Content-Type: application/json
<br/>
> `BODY:`
>```js
  {
    "name":"demo",
    "recipe":"chrome-pdf",
    "engine":"handlebars",
    "content":"<h1>sample content</h1>"
  }
>```

Read a template by id
> `GET`: http://jsreport-host/odata/templates('xxxxxxxxxx')

Update template property
> `PATCH`: http://jsreport-host/odata/templates(WOIzhZdfjj7rRRO2)
<br/>
> `Headers`: Content-Type: application/json
<br/>
> `BODY:`
> send the properties that you want to change
>```js
  {
    "content": "updated content"
  }
>```

Delete a template
> `DELETE`: http://jsreport-host/odata/templates(UCV8p6iVIzR6pEz8)

### Folders
Folders are entities just like templates and you can use the same odata calls for them.

You can for example list all folders using 
> `GET:` http://jsreport-host/odata/folders

Get templates in specific folder
> `GET:` http://jsreport-host/odata/templates?$filter=folder/shortid eq 'foo'

Get templates in the root folder
> `GET:` http://jsreport-host/odata/templates?$filter=folder eq null

Create a template in specific folder
> `POST:` http://jsreport-host/odata/templates
>```js
  {
    "name": "template name",
    "engine": "none",
    "recipe": "html",
    "folder": {
      "shortid": "foldershortid" 
    }
  }
>```

## Authentication

When jsreport server has [authentication extension](/learn/authentication) enabled or when using [jsreportonline](/online) you need to add authentication header to all API requests. The header is based on [basic http authentication](http://en.wikipedia.org/wiki/Basic_access_authentication) so you should be able to authenticate easily from any platform.

`Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`

Where the hash is based on username and password:  
`base64(username:password)`

>Check [authentication extension docs](/learn/authentication) for more details about different authentication options

## Ping

There is public endpoint http://jsreport-host/api/ping which you can use to check if the jsreport is running. This endpoint isn't behind the authentication so you can use it from your loadbalancer or docker heathcheck.
