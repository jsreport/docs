> Users can quickly browse and display reports in the simple html  based client application

## Basics
jsreport studio is great for developers who want to define the templates but it is not very convenient for common end users who want to just browse and display the reports. For this kind of users jsreport contains lightweight client application.

To open the client application just navigate to `http://<jsreport url>/client` . The application contains the same authentication/authorization layer as the jsreport studio. This means users created in jsreport studio can be used to login to client application as well.

User can only browse the templates or reports in the client application. When a user navigates to a template, client application triggers rendering of the particular template and displays the result.  This is convenient to be used with [html live reports](/learn/live-reports) or with templates which are loading input data using [scripts extension](/learn/scripts). Browsing reports can be on the other hand used together with [scheduling extension](/learn/scheduling) and lets the user to display previously rendered and stored reports.

The application is really designed for the common end users and display also well on tablets or even phones. This fits to the use case where developer defines reports in the jsreport studio, gives permissions to the user in a manager role and a manager can just quickly refresh reports in his tablet or phone.

## Default template for client application
Sometimes you want to go further and prepare for the end user some kind of a portal with custom navigation elements forwarding user to the particular templates so the user doesn't need to stick with basic templates list provided by client application. To do it you just need to create a common jsreport template and mark it as a *Default for client application* in the jsreport studio. This forces client application to automatically open this template when the user opens the application. The template can then contain navigation links to the desired templates.

The client application default template should probably use the [html](/learn/html) to display the content.  You can then easily add static links to the other templates like this:

```html
<a href="/client/#/template/EyeQve0ZN" target="_parent">Monthly invoices</a>
```
*The `href` should start with `/client` or just `/` depending what url users use to access the client application.*

Or you can even use OData api to list all the templates

```js
$.getJSON("/odata/templates", function(val) {
	$("#foo").html(JSON.stringify(val));
});
```

Note that you can have dedicated *default* template for every single user. This can be achieved by setting the correct permission to the template, because client application filters out *default* templates to which user doesn't have access.

## User preference for client application
You can set to every user a preference for the client application or full studio. Navigating to jsreport url will be the forwarded based on the user preferences. This can be set in the jsreport studio user detail toolbar.

Another way to send users directly to the client application is to use a proxy or web server to forward requests from your dedicated url to the jsreport client application url.
` MAP FROM mydomain.com TO jsreport-url/client`

## API
Client application extension adds `isClientAppPreferred` flag to every user and `isClientDefault` to every template. You can modify this properties through standard OData API.
