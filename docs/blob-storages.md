jsreport supports persisting big blobs like output pdf reports through an abstraction called `Blob storage`. The abstraction is implemented in several extensions which provide particular persistence drivers.

- file system (default)
- [aws s3 storage](https://github.com/jsreport/jsreport-aws-s3-storage)
- [azure blob storage](https://github.com/jsreport/jsreport-azure-storage)

The blob storage abstraction is then used in the other jsreport extensions to persist blobs. However, currently, there is an only single extension which is using it. The reports extension uses blob storage to persist captured rendering outputs which are then available for later access. See [reports extension documentation](/learn/reports) for details.

## Configuration

The default blob persisting to the file system uses just this configuration.
```js
"blobStorage": {
	"provider": "fs"
},
```

The custom blob storage implementation typically needs more options passed through standard extensions configuration. Like in case of [aws s3 storage](https://github.com/jsreport/jsreport-aws-s3-storage),
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
