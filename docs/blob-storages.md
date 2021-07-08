
jsreport supports persisting big blobs like [output reports](/learn/reports) or [profiles](/learn/studio#profiles) through an abstraction called `Blob storage`. The main reason for an extra abstraction is that often it's needed to persist big and high volume data outside the main [templates store](/learn/template-stores). However, the most common is to persist the blobs in the same location. Like in the following config:

```js
{
	"store": { 
		"provider": "mongodb"
	},
	"blobStorage": {
		"provider": "mongodb"
	}
}
```

Every [templates store](/learn/template-stores) driver also includes a blob storage provider and typically the blob storage provider is the same as the store provider.
The exception is the blob storage driver for [aws s3](https://github.com/jsreport/jsreport/packages/jsreport-aws-s3-storage) and [azure storage](https://github.com/jsreport/jsreport/packages/jsreport-azure-storage). Both need extra installation but can be reasonable when for example SQL based store has limited space and there is expected very high consumption for stored output reports.

## Configuration

The default blob persisting to the file system uses just this configuration.
```js
"blobStorage": {
	"provider": "fs"
},
```

The default blobs location on the file system is `data/storage` directory. This can be changed using `blobStorage.dataDirectory` configuration.

```js
"blobStorage": {
	"provider": "fs",
	"dataDirectory": "myblobslocation"
}
```

The custom blob storage implementation typically needs more options passed through standard extensions configuration. Like in case of [aws s3 storage](https://github.com/jsreport/jsreport/packages/jsreport-aws-s3-storage),
```js
"blobStorage": {  
	"provider": "aws-s3-storage"
},
"extensions": {
	"aws-s3-storage": {
		"accessKeyId": "...",
		"secretAccessKey": "...",
		"bucket": "...",  
	}	
}	
```
