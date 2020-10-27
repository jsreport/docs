> Use sample testing data during template development

## Basics

The input to the report generation process is the report template and some json formatted input data. You will provide this data to the jsreport API during the runtime, but it's not very effective to call jsreport API every time you want to see how the report will look. Therefore, jsreport comes with a `Data` extension.

`Data` extension allows you to statically define input data using JSON syntax directly in jsreport studio and easily test your templates.

## API

You can use standard OData API to manage and query `data` entities. For example you can query all items using:
> `GET` http://jsreport-host/odata/data

A sample data item is usually linked to the report template by its shortid. In this case the report's template structure looks like the following in json:

```js
  template : {
	  content: "foo",
	  data: {
		  shortid: "sd543fds"		  
	  }
  }
```  

You can also put inline data into a template, which then looks like the following in json:

```js
  template : {
	  content: "foo"
  },
  data: {
    price: 10,
    amount: 5,
    total: 50
  }
```
