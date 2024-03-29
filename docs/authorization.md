
> Manage and delegate user permissions on jsreport objects. Requires [authentication](/learn/authentication) to be enabled

## Basics
jsreport `authorization` extension implements permission rules evaluation and delegation. Every user previously created by [authentication](/learn/authentication) extension is only authorized to manage objects created by himself by default. If the user wants to share an object with another user he needs to explicitly set this up in the permissions form. jsreport can currently distinguish only between `read` and `edit` permissions where `edit` permission represents all operations including permission delegation.

![permissions](/learn/static-resources/authorization-permissions.png)

All entities that are nested inside a folder are inheriting permissions form the parent folder. This works recursively down through multiple levels of folders. In the other words you can fill to the folder some permissions and all the entities inside will get this permissions as well. Additionally if a user has permissions to the particular entity, he or she gets readonly permissions to the all parent folders up the tree.

> To edit the folder permissions, you need to do right-click on it in the entity tree and select edit.

User can be also granted to edit to read all entities.

![all entities](/learn/static-resources/authorization-all-entities.png)

## User groups
Users can be associated into logical groups using entity `UsersGroup`. The group can be created just like any other entity in the studio and the users associated in the properties.

![user group create](/learn/static-resources/user-group-create.png)

Every entity then can get granted access from the users in the group by associating group permissions. This works also with folder based structures where the nested folders inherit group permissions from the parent.

![user-group-permissions](/learn/static-resources/user-group-permissions.png)


## API

`Authorization` extensions adds to every jsreport object `readPermissions` and `editPermissions` properties. These properties contains list of user ids you can easily alter from the odata API.

## Limitations
[Scheduling](/learn/scheduling) and [Version Control](https://jsreport.net/learn/version-control) extension is currently enabled just for admin user.
