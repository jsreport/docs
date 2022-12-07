

jsreport previews office reports directly in the browser when running from the studio by default.  This is handy because it creates faster development cycles. This behavior can be of course generaly disabled or you can use the "Download" option from the "Run" button context menu if you want to return the office file stream. 

## Security
The office preview uses Microsoft Office Online service which requires public access to the displayed file. This is why we need to upload the office file to our publicly running server. This is happening only during the preview thus your production data are safe. The file on our servers has also unique secure id and stays just for max 5 minutes before it is automatically deleted. 

## Configruation
You can fully disable the preview behavior in the configuration and let jsreport studio always download office file instead of previewing it.

```js
"office": {
  "preview": {  
    "enabled": false  
  }
}
```

Instead of using our public server you can also use yours. Such server needs to accept POST with the file stream, return a string id of the file which needs to be accessible afterward with GET.

```js
"office": {  
  "preview": {
    "publicUri": "https://custom-server.com"
  }
}
```

The last configuration option lets you disable the warning for previews.

```js
"office": {  
  "preview": {
    "showWarning": false
  }
}
```

The `office` root configuration is global for all recipes and extensions using office preview. However, you can also use configuration with extension based granularity.

```js
"extensions": {
  "[extensioname]": {
    "preview": {
        "enabled": true,
        "publicUri": "https://custom-server.com",
        "showWarning": false
    }
  }
}
```

## Force preview
You can also force the office preview from the API call using `options.office.preview=true` in case you want to response the office online html page instead of the xlsx stream.
```js
{ 
  "template": {...},
  "data": {...},
  "options": { 
    "office": {
       "preview": true
    }
  }
```

## Scripts
The `afterRender` [scripts](/learn/scripts) receives the preview html instead of the xlsx file in the `res.content` when you preview with the studio. This is undesired when you want to test postprocessing the output. In this case, you can use the "Download" button instead of "Run" or disable the preview in the script.
```js
function beforeRender (req, res) {
	req.options.office = {
		...req.options.office,
		preview: false
	}
}
```