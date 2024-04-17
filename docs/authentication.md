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

## Authentication using an authorization server

> This feature is in preview mode, it means that we have implemented all the necessary steps to complete the authentication flow with an authorization server but we are looking for real feedback about the feature to determine if something is missing or incomplete for other use cases. If you have such feedback don't hesitate to raise a [question](https://forum.jsreport.net) or open an [issue](https://github.com/jsreport/jsreport/issues).

If you would like to delegate authentication in the **jsreport studio** and in the **jsreport http API** to an authorization server that supports [OpenID](https://openid.net/), you will need to use the `authorizationServer` options. It is likely you will only need this if you would like to expose jsreport as a product with Single Sign-On support.

There are two main authentication cases supported when authorization server is enabled:

- Single Sign On access to jsreport studio
- Token based authentication to the jsreport http API

As requirements you will need to have this ready:

- both the **jsreport studio** registered as a client, and the **jsreport http api** registered as a api resource in your authorization server, so both can be authenticated and can talk to the authorization server. You will provide the credentials for both in `authorizationServer.studioClient` and `authorizationServer.apiResource` configuration options. Additionally you may need to configure your auth server to allow redirect from a jsreport uri, assuming jsreport is hosted on `localhost` the uri to allow is `http://localhost:5488/auth-server/callback`, which is used during the [Single Sign On](#single-sign-on-using-an-authorization-server) when the login against your authorization server is successful and redirects back to jsreport.
- jsreport needs to know how to associate the users from your authorization server to users/groups in jsreport, this association is needed to correctly identify which jsreport entities and permissions the user of your authorization server is able to work with in the context of jsreport. to do this you will have to do any of the following:
  - associate the jsreport username of an existing jsreport user with a claim (`authorizationServer.usernameField`) linked to the user handled by your authorization server.
  - associate the jsreport group name of an existing jsreport group with a claim (`authorizationServer.groupField`) linked to the user handled by your authorization server. **this association has the extra advantage of not requiring to duplicate the user of your authorization server in jsreport**, you can handle all the permissions in jsreport in groups and let your authorization server handle the user, you just need to associate them so after login the jsreport is able to identify which entities it has access to. When doing SSO to the **jsreport studio** you can customize the username shown there by setting another claim (`authorizationServer.usernameField`) to the data linked to the user handled by your authorization server.

### Single Sign On using an authorization server

You likely want this if you have a multi-tenant application, which handles authentication against a central authorization server and you want to delegate the access to the **jsreport studio** (reusing the same user credentials that your users already have) against the same authorization server.

In this case the authentication flow will be like this:

- if you go to the studio you will notice that the login now has an extra button "LOGIN WITH XXXXX", which is designed to start the authentication/authorization flow against your authorization server, internally the configured `authorizationServer.endpoints.authorization`, `authorizationServer.endpoints.jwks`, `authorizationServer.endpoints.token`, `authorizationServer.endpoints.userinfo` endpoints of your authorization server are going to be reached.
- in the authorization server you will need to login and give consent to the information the jsreport studio is going to need to get access to (`authorizationServer.authorizationRequest.scope`).
- if the communication with your authorization server resolves successfully you will now see that you are going to be authenticated in jsreport studio as the jsreport user associated with the user of your authorization server, and you can do any action the associated jsreport user is allowed to

When you configure the studio to have this authentication flow you will probably want to integrate a button or link in your app that redirects the user to the studio.

```html
<!-- Assumming jsreport is available on localhost:5488 -->
<a href="http://localhost:5488" />
```

if you do this then the user will be presented with the login page by default, requiring that user clicks the "LOGIN WITH XXXXX" to start the authentication with the authorization server. you can avoid the user needing to do that by instead using an extra parameter in the link from your app.

```html
<!-- Assumming jsreport is available on localhost:5488 -->
<a href="http://localhost:5488/?authServerConnect" />
```

this will redirect the user to the authorization server directly, and provide a much better experience if the user is already logged-in with the authorization server.

### Token based authentication using an authorization server

You likely want this if you want to build an app that uses the **jsreport http api**, and want that the calls to the api are authenticated against tokens issued by an authorization server.

The authentication flow in this case looks like the following:

- first of all, somehow you will need to get a token, issuing a token is the responsibility of the authorization server and you will need to ask for tokens from one of your applications, the necessary steps and details to get the token will depend on the implementation of your authorization server, you will need the token to later be able to call the jsreport API
- jsreport will expect to get a token from `Authorization` header in any protected API call, the token must be sent using `Bearer` auth schema, this means that all your requests to the jsreport API must be using a header: `Authorization: Bearer <your token here>`
- With the token in place, jsreport will send the token along with any other configured data (`authorizationServer.introspectionRequest.extraBody`) and credentials (`authorizationServer.apiResource`) to the configured introspection endpoint (`authorizationServer.endpoints.introspection`)
- The authorization server must return a json response with `active` field that describes if the token is valid or not (`{ active: false | true }`), and optionally the response can contain a field (`authorizationServer.usernameField` or `authorizationServer.groupField`) which represents the linked jsreport user/group associated with this token
- jsreport will check the information of the token returned from the authorization server, and if no linked jsreport user information was found in the previous step the configured userinfo endpoint `authorizationServer.endpoints.userinfo` is going to be called, the response from that call should contain a field (`authorizationServer.usernameField` or `authorizationServer.groupField`) with the linked jsreport username/group. the resolved username/group must be a valid and existing jsreport user/group, and if scope validation is configured `authorizationServer.introspectionRequest.tokenValidScopes` we will also check that the token has a valid scope
- finally if the token is valid and active and the linked jsreport user/group is resolved correctly then the request will be authenticated

All of these steps are based on the [OpenID](https://openid.net/) standard, which is common to see in authorization servers that provide Single Sign On.

For a real implementation of jsreport + authorization server check our [official sample](https://github.com/bjrmatos/jsreport-with-authorization-server-sample).

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
		// use the "authorizationServer" options when you plan to delegate authentication
		// to an authorization server,
		// this means that jsreport studio access and all protected jsreport http API calls will be validated with
		// the configured authorization server, giving you the chance to expose
		// jsreport as a product with Single Sign On support.
		// see "Token based authentication using an authorization server" section of this document for more details and a link to a sample with real implementation.
		"authorizationServer": {
			// name for you authorization server (required), this is used for display purposes in
			// the studio login (button "LOGIN WITH <name>").
			"name": "AuthServer",
			// issuer identifier (required), basically the identifier of your authorization server. it is usually a URL
			"issuer": "http://localhost:9800",
			// endpoints to the authorization server, you can easily find the information of these routes in the http://localhost:9800/.well-known/openid-configuration route of your authorization server
			"endpoints": {
				// (required) endpoint to get the public keys used to verify any JSON Web Token issued by the authorization server.
				"jwks": "http://localhost:9800/.well-known/openid-configuration/jwks",
				// (required) endpoint to start the authentication/consent with the authorization server
				"authorization": "http://localhost:9800/connect/authorize",
				// (required) endpoint to get a token
				"token": "http://localhost:9800/connect/token",
				// (required) endpoint to the Token introspection https://tools.ietf.org/html/rfc7662 of your authorization server.
				"introspection": "http://localhost:9800/connect/introspect",
				// (required) endpoint to the Userinfo https://openid.net/specs/openid-connect-core-1_0.html#UserInfo of your authorization server
				"userinfo": "http://localhost:9800/connect/userinfo"
			},
			// (required) registered client in your authorization server, that identifies the jsreport studio
			"studioClient": {
				"clientId": "jsreport-studio",
				"clientSecret": "---secret---",
				// configure how the credentials (clientId and clientSecret) are going to
				// be sent to the authorization server.
				// valid values are "basic", "post", defaults to "basic"
				"authType": "basic"
			},
			// (required) registered api resource in your authorization server, that identifies the jsreport http api
			"apiResource": {
				"clientId": "jsreport-api",
				"clientSecret": "---secret---",
				// configure how the credentials (clientId and clientSecret) are going to
				// be sent to the authorization server.
				// valid values are "basic", "post", defaults to "basic"
				"authType": "basic"
			},
			// name of the field that jsreport will look at in the response from authorization server to
			// use as the jsreport username associated with the token. (optional, defaults to "username")
			"usernameField": "username",
			// time in milliseconds that jsreport will wait before closing the request
			// sent to the authorization server. (optional, defaults to 3500)
			"timeout": 6000,
			// additional configuration for the jsreport studio authentication (authorize request) against the authorization server
			"authorizationRequest": {
				// <string>| Array<string> the scopes that the jsreport studio authentication (authorize request)
				// is going to request to the authorization server. default: ["openid", "profile"]
				"scope": "openid profile" // or ["openid", "profile"]
			},
			// additional configuration for the token based authentication request against the authorization server
			"introspectionRequest": {
				// <string>| Array<string> list of valid scopes that the token needs to have in order to be considered valid, the token must have at least one scope that match with some item in the list in order to be considered valid
				"tokenValidScopes": "jsreport", // or ["jsreport"]
				// extra body to sent to the introspection request
				"extraBody": {}
			}
		}
	}
}
```
