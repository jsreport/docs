> Persist report rendering outputs for later access

## Basics
Everytime you call jsreport API to render report, you will get back stream with report content. This is usefull when you want to display report directly to the user or you want to store the result in your data storage for later use. But sometimes you just don't want the report in the time of generation and you don't want to store it on your own. In this case you can use `Reports` extension and let the jsreport store the report for you.

The reports extension uses [blob storage](/learn/blob-storages) abstraction for reports blobs persistence. This by default stores reports to the file system. Check the documentation if you want to store reports to a different destination.

## API

Reports extension is not used by default for all rendering requests and you need to specify it for every request separately.

To use `reports` extension you need extend rendering request in a following way:

> `POST:` https://jsreport-host/api/report
> `BODY:`
>```js
   {
      "template": { "name" : "My template" },
      "data" : { ... },
      "options": {
	      "reports": { "save": true }
      }
   }
>```

This will create a `Report` entity you can use in jsreport studio as well as in OData API. Additionally it will add custom header `Permanent-Link` to the response which you can later use to actually download the report content.

The stored report entity name is by default inherited from the template name. However, you can customize it by passing`{ "options": { "reportName": "yourCustomName" }` in the request body.

The name of the stored file/blob is by default inherited from the entity unique _id. This can be customized using `{ "options": { "reports": { "save": true, "blobName": "myfilename" } }`

## Async
Sending rendering request with `options.reports.save = true` will instruct the extension to save the report and add `Permanent-Link` header to the response, but the rendering is still synchronous and you receive response back after the process is finished. If you want to start the rendering process  asynchronously and receive the responds immediately you should set `options.reports.async = true`.

> `POST:` https://jsreport-host/api/report
> `BODY:`
>```js
   {
      "template": { "name" : "My template" },
      "data" : { ... },
      "options": {
	      "reports": { "async": true }
      }
   }
>```

In this case you receive response with `Location` header containing url to the rendering status page. It will be something like `http://jsreport-host/reports/id/status`. You can then ping the status page to check if the rendering is done. In that case the response status will be `201` and the location header will contain address to the stored report.

## Public reports
The stored reports are protected by [authentication](/learn/authentication) as any other entity by default. However, you can expose the report to the public by adding `options.reports.public=true` to the rendering request.

> `POST:` https://jsreport-host/api/report
> `BODY:`
>```js
   {
      "template": { "name" : "My template" },
      "data" : { ... },
      "options": {
	      "reports": {  "save": true,  "public": true  }
      }
   }
>```

In this case the returned `Permanent-Link` will be public url bypassing the authentication.


## Cleanup
The reports stored from async calls are forever persisted by default. You can change this and enable automatic old reports clean up. This can be done through config.
```json
{
  "extensions": {
    "reports": {
      // how often the cleanup runs
      "cleanInterval": "5m",
      // how much old reports should be deleted
      "cleanThreshold": "1d",
      // how many deletes are processed in parallel (this avoids putting too much pressure on the database), defaults to 10
      "cleanParallelLimit": 10
    }
  }
}
```
Be aware the auto cleanup logic removes also the reports produced through scheduling extension.

## OData

You can use standard OData API to manage and query report entities. For example you can query all reports using:
> `GET` http://jsreport-host/odata/reports
