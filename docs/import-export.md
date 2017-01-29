> Export templates store into zip file and later recover the state or import to a different instance

![export](https://jsreport.net/img/export.gif)

##Migrating templates
This extension can be used to move templates to different servers as well to different servers using different template stores. This means you can for example copy templates from default file system store to [mssql store](https://github.com/jsreport/jsreport-mssql-store) or [postgres store](https://github.com/jsreport/jsreport-postgres-store).

##API

###Export into zip stream
```js
POST: /api/export
BODY: {
  //optional
  selection: ["id1", "id2" ...]
}
```



###Import zip stream

```js
POST: /api/import
BODY: /* multipart import.zip */
```
`

