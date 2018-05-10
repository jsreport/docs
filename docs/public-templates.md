> Expose report template with secure token granting permissions to the unauthenticated users. Requires [authentication](/learn/authentication) and [authorization](/learn/authorization) extensions to be enabled.

[Authentication](/learn/authentication) extension grants access to jsreport only for the authenticated users and [authorization](/learn/authorization) extension assures users gets access only to the permitted operations. `public-templates` extension comes into play if you want to share the template with someone who doesn't have any credentials into jsreport.

## Sharing template
To share a template you can use the `share` toolbar button in jsreport designer  or API. This provides you a public link including a security token. Accessing the link outputs a report produced by rendering particular template.

## API
To include secure token in the rendering output just add `options.authorization.grantRead` or `grantWrite` into the body:

> `POST:` api/report
> ```js
> {
>   "template": { ... }
>   "options": {
>     "authorization": {
>       "grantRead": true
>     }
>   }
> }
> ```
