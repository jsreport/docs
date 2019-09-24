
The office preview in the studio uses Microsoft Office Online service which requires public access to the displayed file. This is why we need to upload the office file to our publicly running server. This is happening only during the preview thus your production data are safe. The file on our servers has also unique secure id and stays just for max 5 minutes before it is automatically deleted. 

However you can disable this behavior in the configuration and let jsreport studio always download office file instead of previewing it.

```js
"office": {
  "enabled": false  
}
```

Instead of using our public server you can also use yours. Such server needs to accept POST with the file stream, return a string id of the file which needs to be accessible afterward with GET.

```js
"office": {  
  "publicUri": "https://custom-server.com"
}
```

The last configuration option lets you disable the warning for previews.

```js
"office": {  
  "showWarning": false
}
```

The `office` root configuration is global for all recipes and extensions using office preview. However, you can also use configuration with extension based granularity.

```js
"extensions": {
  "[extensioname]": {
    "enabled": true,
    "publicUri": "https://custom-server.com",
    "showWarning": false
  }
}
```
