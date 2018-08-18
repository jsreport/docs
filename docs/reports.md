
> Persist report rendering outputs for later access

## Basics
Everytime you call jsreport API to render report, you will get back stream with report content. This is usefull when you want to display report directly to the user or you want to store the result in your data storage for later use. But sometimes you just don't want the report in the time of generation and you don't want to store it on your own. In this case you can use `Reports` extension and let the jsreport store the report for you.

## API

Reports extension is not used by default for all rendering requests and you need to specify it for every request separately.

To use `reports` extension you need extend rendering request in a following way:

> `POST:` https://jsreport-host/api/report
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

## Async
Sending rendering request with `options.reports.save = true` will instruct the extension to save the report and add `Permanent-Link` header to the response, but the rendering is still synchronous and you receive response back after the process is finished. If you want to start the rendering process  asynchronously and receive the responds immediately you should set `options.reports.async = true`.

> `POST:` https://jsreport-host/api/report
> `BODY:`
>```js
   {
      "template": { "shortid" : "g1PyBkARK" },
      "data" : { ... },
      "options": {
	      "reports": { "async": true }
      }
   }
>```

In this case you receive response with `Location` header containing url to the rendering status page. It will be something like `http://jsreport-host/reports/id/status`. You can then ping the status page to check if the rendering is done. In that case the response status will be `201` and the location header will contain address to the stored report.

## Cleanup
The reports stored from async calls are forever persisted by default. You can change this and enable automatic old reports clean up. This can be done through config.
```json
{ 
  "extensions": { 
    "reports": {
      // how often the cleanup runs
      "cleanInterval": "5m",
      // how much old reports should be deleted
      "cleanTreshold": "1d"
    }
  }
}
```
Be aware the auto cleanup logic removes also the reports produced through scheduling extension.

## OData

You can use standard OData API to manage and query report entities. For example you can query all reports using:
> `GET` http://jsreport-host/odata/reports
