> Organize jsreport objects with tags

![export](https://jsreport.net/img/tags.gif)

## Basics
Enabling `tags` extension will add organizational capabilities to jsreport, enabling organize, filter and show jsreport objects with tags.

## Creating and using tags

You can create a tag using jsreport studio. In which you can specifiy information about the tag (name, color, description, etc), later when creating/editing other objects (templates, data, scripts, assets, images, etc) you can assign one or multiple tags to these objects.

## Configuration

Add `tags` node to the [standard config file](/learn/configuration).

```js
"extensions": {
	"tags" : {
	  // boolean to determine if jsreport studio by default should show objects organized by tags
		"organizeByDefault": true
	}
}
```

## API

You can use standard OData API to manage and query tag entities. For example you can query all tags using:

>`GET` http://jsreport-host/odata/tags
