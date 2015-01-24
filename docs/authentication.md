> Add login screen to jsreport and user management forms

##Basics
Enabling `authentication` extension will add a login screen into jsreport studio and authenticate all incoming requests. The browser authentication is based on the cookie and API calls are verified using [basic](http://en.wikipedia.org/wiki/Basic_access_authentication) authentication.  

`Authentication` configuration adds a `user administrator` into the system  responsible for managing other users. This user can create users, remove users or change their passwords. All other individual users do not have permissions to alter any other user.

##Configuration

To enable authentication add following json into [configuration file](https://github.com/jsreport/jsreport/blob/master/config.md).

```js
"authentication" : {
	"cookieSession": {
        "secret": "dasd321as56d1sd5s61vdv32"        
	},
	"admin": {
		"username" : "admin",
		"password": "password"
	}
},
```

You can change admin username or password as you want.

##API
You need to add header to every request when is this extension enabled.

`Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`

Where the hash is based on username and password:
`base64(username:password)`

You can use standard OData API to manage and query user entities. For example you can query all users using:

>`GET` http://jsreport-host/odata/users

