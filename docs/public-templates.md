> Expose report template with secure token granting permissions to the unauthenticated users. Requires [authentication](/learn/authentication) and [authorization](/learn/authorization) extensions to be enabled.

[Authentication](/learn/authentication) extension grants access to jsreport only for the authenticated users and [authorization](/learn/authorization) extension assures users gets access only to the permitted operations. `public-templates` extension comes into play if you want to share the template with someone who doesn't have any credentials into jsreport.

##Sharing template
To share a template you can use the `share` toolbar button in jsreport designer  or API. This provides you a public link including a security token. Accessing the link outputs a report produced by rendering particular template.

The generated public link grants the user read or edit access. This can be distinguished using an option during generating link. The edit access is useful in integration scenarios when the template is being edited in the jsreport embedded designer.

The security token provided by `public-templates` extension not only grants access to the particular template but also grants access for rendering any dynamically created anonymous template. This is required for the scenarios in which shared template can by also exported into the other format like pdf.

The security token is stored directly in the template itself. This allows to create self editable and self exportable templates together with the [wrapped-html](/learn/wrapped-html) recipe.

##API

To include secure token in the rendering output just add `options.authorization.grantRead` or `grantWrite` into the body:

> `POST:` api/report
> ```js
> {
>   "template": { ... }
>   "options": {
>     "authorization": {
>       "grantEdit": true
>     }
>   }
> }
> ```

See the [wrapped-html](/wrapped-html) recipe for the usage of public templates.





