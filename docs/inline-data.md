> Use sample testing data during template development

## Basics

The input to the report generation process is the report template and some json input data. You will provide these data to the jsreport API in the runtime, but it's not very effective to call jsreport API every time you want to see how will the report look like. There for jsreport comes with `Data` extension.

`Data` extension allows you to statically define input data using JSON syntax directly in jsreport studio and easily test your templates.

## API

You can use standard OData API to manage and query `data` entities. For example you can query all items using:
> `GET` http://jsreport-host/odata/data

A sample data item is usually linked to the report template by its shortid. In this case template structure with the report looks following in json:

```js
  template : {
	  content: "foo",
	  data: {
		  shortid: "sd543fds"		  
	  }
  }
```  

Data extension also supports inline data directly in the `Template` entity. This is used mainly in [the browser client](/learn/browser-client).

Data inline into template then looks following in json:

```js
  template : {
	  content: "foo",
	  data: {
			price: 10,
			amount: 5,
			total: 50
	  }
  }
```
