> Add login screen to jsreport and user management forms

## Basics
Enabling the `authentication` extension will add a login screen into jsreport studio and authenticate all incoming requests. The browser authentication is based on the cookie and API calls are verified using [basic](https://en.wikipedia.org/wiki/Basic_access_authentication) authentication or using [bearer](https://tools.ietf.org/html/rfc6750) authentication verified against a configured authorization server.  

`Authentication` configuration adds a `user administrator` into the system responsible for managing other users. This user can create users, remove users or change their passwords. All other individual users do not have permission to alter any other user.

## Basic authentication

### Configuration

To enable authentication add the following json into [configuration](/learn/configuration).

```js
"extensions": {
	"authentication" : {
	    "cookieSession": {
	        "secret": "dasd321as56d1sd5s61vdv32",
					"cookie": { <custom cookie options here> }       
	    },
	    "admin": {
	        "username" : "admin",
	        "password": "password"
	    }
	}
}
```

You can change admin username or password as you wish.

The list of custom cookie options to set is available [here](https://github.com/pillarjs/cookies#cookiesset-name--value---options--), for example, you can set `{ "cookie": { "secure": true } }` to indicate that the auth cookie will only be sent over HTTPS, which is a good security practice but it requires that your server works under https first.

```js
"extensions": {
	"authentication" : {
	    "cookieSession": {
	        "secret": "dasd321as56d1sd5s61vdv32",
					"cookie": { "secure": true }       
	    },
	    "admin": {
	        "username" : "admin",
	        "password": "password"
	    }
	}
}
```

### Custom users
The jsreport custom users can be created from the entity tree plus button.

![new user](/learn/static-resources/authentication-new-user.png)

### API
You need to add a Header to every request when this extension is enabled.

`Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`

Where the hash is based on username and password:
`base64(username:password)`

You can use standard OData API to manage and query user entities. For example, you can query all users using:

>`GET` http://jsreport-host/odata/users

## Token based authentication using an authorization server

> This feature is in preview mode, it means that we have implemented all the necessary steps to complete the authentication flow with an authorization server but we are looking for real feedback about the feature to determine if something is missing or incomplete for other use cases. If you have such feedback don't hesitate to raise a [question](https://forum.jsreport.net) or open an [issue](https://github.com/jsreport/jsreport/issues).

If you would like to delegate all authentication in the **jsreport API** to an authorization server that supports token based authentication (Bearer auth schema), you will need to use the `authorizationServer` options. It is likely you will only need this if you would like to expose jsreport as a product with Single Sign-On support.

When jsreport is configured to use an authorization server, the authentication flow looks like the following:

- first of all, somehow you will need to get a token, issuing a token is the responsability of the authorization server and you will need to ask for tokens from one of your applications, the necessary steps and details to get the token will depend on the implementation of your authorization server, you will need the token to later be able to call the jsreport API
- jsreport will expect to get a token from `Authorization` header in any protected API call, the token must be sent using `Bearer` auth schema, this means that all your requests to the jsreport API must be using a header: `Authorization: Bearer <your token here>`
- With the token in place, jsreport will send the token (`token=<value of your token>`, `token_type_hint=access_token` fields) along with any other configured data (`authorizationServer.tokenValidation.hint`) or credentials (`authorizationServer.tokenValidation.auth`) to the configured endpoint (`authorizationServer.tokenValidation.endpoint`)
- The authorization server must return a json response with fields that describe if the token is valid or not (`authorizationServer.tokenValidation.activeField`), and with which user is associated (`authorizationServer.tokenValidation.usernameField`)
- jsreport will check the information of the token returned from the authorization server (the active field `authorizationServer.tokenValidation.activeField` must be true if the token is valid, the username field `authorizationServer.tokenValidation.usernameField` must be a valid jsreport user, and if scope validation is configured `authorizationServer.tokenValidation.scope` we will also check that the token has a valid `authorizationServer.tokenValidation.scope.valid` scope) and then authenticate the user

Much of these steps are based on the [token introspection](https://tools.ietf.org/html/rfc7662#section-4) method, which is very common to see in authorization servers based on `OAuth2` or `OpenID Connect`, and if your authorization server is based on any other kind of implementation the described steps are very easy to implement, so you can achieve compatibility with most of the authorization servers.

For a real implementation of jsreport + authorization server check our [official sample](https://github.com/bjrmatos/jsreport-with-authorization-server-sample)

### Configuration

To enable authentication, add the following json into [configuration file](/learn/configuration).

```js
"extensions": {
	"authentication" : {
		"cookieSession": {
	        "secret": "dasd321as56d1sd5s61vdv32"        
		},
		"admin": {
			"username" : "admin",
			"password": "password"
		},
		// use the "authorizationServer" options when you plan to protect API calls
		// with token based authentication against an authorization server,
		// this means that all protected jsreport API calls will be validated with
		// the configured authorization server, giving you the chance to expose
		// jsreport as a product with Single Sign On support.
		// see "Token based authentication using an authorization server" section of this document
		// for more details and a link to a sample with real implementation.
		"authorizationServer": {
			"tokenValidation": {
				// URL to the authorization server endpoint (required)
				"endpoint": "http://localhost:9800/token/introspection",
				// time in milliseconds that jsreport will wait before closing the request
				// sent to the authorization server. (optional, defaults to 6000)
				"timeout": 6000,
				// by default jsreport will send data to the authorization server
				// using "application/x-www-form-urlencoded" content type,
				// setting this option to true will make jsreport to send the data using
				// "application/json" content type. (optional, defaults to false)
				"sendAsJSON": false,
				// use this option if you would like to pass custom data to
				// the authorization server. you can configure one or multiple values
				// for example using "hint": "custom value" will send a "hint" field
				// with value "custom value" to the authorization server,
				// to use another field name you can use "hint": { "name": "reportService", value: true } to send a "reportService" field
				// and use "hint": [{ "name": "reportService", value: true }, { "name": "anotherField", value: true }] to send multiple fields
				// (optional, defaults to null)
				"hint": null,
				// name of the field that jsreport will look at in the response from authorization server to
				// use as the jsreport username associated with the token. (optional, defaults to "username")
				"usernameField": "username",
				// name of the field that jsreport will look at in the response from authorization server to
				// determine if the token is valid or not. (optional, defaults to "active")
				"activeField": "active",
				// options to use if you want that jsreport checks for valid scopes in the token
				// optional, defaults to null
				"scope": {
					// name of the field that jsreport will look at in the response from authorization server to
					// determine the scope/scopes of the token. (optional, defaults to "scope")
					"field": "scope",
					// list of valid scopes that the token needs to have in order to be considered valid, the token must have at least one scope that match with some item in the list in order to be considered valid
					"valid": ["myscope"]
				},
				// options to use if the authorization server is not public and requires authentication,
				// if your authorization server is public just pass "auth": false
				// (required)
				"auth": {
					// defines which auth schema to use while sending credentials to the authorization server
					// supported values are "basic" and "bearer"
					"type": "basic",
					// credentials to use when using the "basic" type
					"basic": {
						"clientId": "test",
						"clientSecret": "xxxx"
					},
					// credentials to use when using the "bearer" type
					"bearer": "dasdasddw23fsdfsdfr56hvFVdf3"
				}
			}
		}
	}
}
```
