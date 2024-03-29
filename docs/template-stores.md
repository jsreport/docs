
jsreport supports durable persistence of templates, assets, scripts and all other entities. This helps to cover full reporting scenario from designing reports in the studio, saving templates to the template store and referencing them to render the final reports based on the input data in the production.

> jsreport uses `blob storage` abstraction for persisting bigger and high volume objects like output reports or profiles. See the [blob storages documentation](/learn/blob-storages) for details.

The default jsreport template store is based on the file system, however, this is not the only option. jsreport provides unified API around the persistent storage which is implemented by the custom extensions providing access layer to the other media like sql database and many more. Switching from the file system persistence to let say mongodb requires in the end only installing a custom extension and changing the connection string.

## Default file system store
jsreport stores templates by default to the file system. This default implementation generates human readable directory structure with templates and other entities definitions. Such structure is very easy to edit with your favorite text editor, deploy to the production and also version with git or other source control. The file system implementation finally supports also cloud based "storage" like AWS S3 or Azure Storage.

You can find more information about this default store in the dedicated article - [file system based store](/learn/fs-store)

## Template store extensions
The extensions implementing template store can be installed as any other jsreport extension. Typically it only requires to run `npm install jsreport-xxx` and restart the jsreport server. The automatic jsreport extensions discovery should find and load it afterwards.

Applying store extension to the jsreport requires changing the `store.provider` in the [configuration](/learn/configuration) and configuring the extension based on the instructions in the extension's documentation.

```js
"store": {
  "provider": "mongodb",
},
"extensions": {
  "mongodb-store": {
      "address": "127.0.0.1",
      "databaseName" : "std"
  }
}
```

The currently supported templates store implementation includes:

| Documentation | Technology |
| ------------- | ---------- |
| [jsreport-fs-store](/learn/fs-store) | file system + Azure Storage + AWS S3 |
| [jsreport-mssql-store](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-mssql-store)| Microsoft SQL Server |
| [jsreport-postgres-store](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-postgres-store) | PostgreSQL|
| [jsreport-mongodb-store](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-mongodb-store) | MongoDB
| [jsreport-oracle-store](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-oracle-store) | Oracle

Note the extensions implementing templates store are being used only for persisting the jsreport entities. Its purpose isn't to load the report input data from a source database. This feature is provided through [custom scripts](/learn/scripts) extension.

The SQL based stores are creating database schema the first time you start jsreport. In case you change the jsreport version in the future, you may see errors about missing columns. This is because the SQL stores aren't maintaining change scripts. You can either create the missing columns manually on your own. Or [export](/learn/import-export) the entities with the current jsreport version, drop the database, upgrade jsreport and import the entities back from the zip. Make sure to back up the first.

## Migrating between different stores

jsreport [import-export](/learn/import-export) extension is able to export data from every supported store implementation into single zip package. This package can be then imported back into any other jsreport instance which can be using even different store implementation. This allows you to use locally a file system based store and easily import the same data to production server running mongo for example.

## REST API
The template store is typically internally used by extensions or studio to query and update entities like report templates. However template store API is exposed also through the unified [odata](http://www.odata.org/) based REST API. This can be used by any client to remotely query or update the underlying jsreport data. The API is always the same no matter if the underlying store implementation is based on SQL or file system. See more details in [API documentation](/learn/api#querying-and-crud).

## javascript API
The jsreport custom extension authors can also interact with the store directly through javascript API. This can be also used in a node.js application to write a custom data import/export.

The API is compatible with mongo

```js
const templates = await reporter.documentStore.collection("templates")    
    .find({ "shortid": "foo"})    
	.sort({ "name": 1 })    
	.limit(10)    
	.toArray()
```    

See the [custom extension documentation](https://jsreport.net/learn/custom-extension) for more details.
