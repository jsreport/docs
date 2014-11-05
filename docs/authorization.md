jsreport implements authorization using an external custom service you provide. When enabled, jsreport will send http request to external authorization service every time some entity is about to be read or modified. External service then decides if the current user should be authorized or not.

To enable authorization add following into [config] file.
```js
"authorization" : {
        "externalService": {
            "url" : "http://169.89.123.45:9000"
        }
},
```

jsreport will send requests to external service in the following format:

`externalService.url/operation/type/shortid`

- `operation` can take value `create`, `read`, `delete` or `update`
- `type` is entity type like `templates`
- `shortid` identification of object which can be taken from url in studio for example

Note that jsreport will attach authentication cookie and authorization header to the authorization request. The external service can use these information to authenticate user in the main application.

jsreport will skip authorization for admin users authenticated previously with [authentication](/learn/authentication) extensions.