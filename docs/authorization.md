> Manage and delegate user permissions on jsreport objects. Requires [authentication](/learn/authentication) to be enabled

## Basics
jsreport `authorization` extension implements permission rules evaluation and delegation with single object granularity. Every user previously created by [authentication](/learn/authentication) extension is only authorized to manage objects created by himself by default. If the user wants to share an object with another user he needs to explicitly set this up in the permissions form. jsreport can currently distinguish only between `read` and `edit` permissions where `edit` permission represents all operations including permission delegation.

## API

`Authorization` extensions adds to every jsreport object `readPermissions` and `editPermissions` properties. These properties contains list of user ids you can easily alter from the odata API.

## Limitations
[Scheduling](/learn/scheduling) and [Version Control](https://jsreport.net/learn/version-control) extension is currently enabled just for admin user.
