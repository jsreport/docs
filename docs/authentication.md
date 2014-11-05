jsreport provides simple access `Authentication` through [configuration file](https://github.com/jsreport/jsreport/blob/master/config.md).

To enable authentication add following json into `[prod|dev].config.json`

```js
"authentication" : {
	"cookieSession": {
        "secret": "dasd321as56d1sd5s61vdv32",
        "cookie": {
            "domain": "localhost"
        }
	},
	"admin": {
		"username" : "admin",
		"password": "password"
	}
},
```

This will enable both browser cookie based authentication as well as [basic](http://en.wikipedia.org/wiki/Basic_access_authentication) authentication for [API](/learn/api). You can change admin username or password and you will also need to change `cookieSession.cookie.domain` to your dns or ip address.
