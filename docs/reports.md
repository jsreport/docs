> Persist report rendering outputs for later access

##Basics
Everytime you call jsreport API to render report, you will get back stream with report content. This is usefull when you want to display report directly to the user or you want to store the result in your data storage for later use. But sometimes you just don't want the report in the time of generation and you don't want to store it on your own. In this case you can use `Reports` extension and let the jsreport store the report for you.

##API

Reports extension is not used by default for all rendering requests and you need to specify it for every request separately. 

To use `reports` extension you need extend rendering request in a following way:

> `POST:` https://jsreport-host/report
> `BODY:`
>```js 
   { 
      "template": { "shortid" : "g1PyBkARK" },
      "data" : { ... },
      "options": { 
	      "reports": { "save": true }
      }
   } 
>```

This will create a `Report` entity you can use in jsreport studio as well as in OData API. Additionally it will add custom header `Permanent-Link` to the response which you can later use to actually download the report content.


 You can use standard OData API to manage and query report entities. For example you can query all reports using:
> `GET` http://jsreport-host/odata/reports


