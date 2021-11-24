
# Basics
`chrome-image` recipe is able to convert html to image. It works just like the [chrome-pdf](/learn/chrome-pdf) recipe only some options are different.

## Options

The settings reflect the [headless chrome API](https://github.com/GoogleChrome/puppeteer/blob/v1.11.0/docs/api.md#pagescreenshotoptions) where you can also find detail information.

- type
- quality
- fullPage
- clipX
- clipY
- clipWidth
- clipHeight
- omitBackground
- mediaType
- viewportWidth
- viewportHeight
- waitForJS
- waitForNetworkIdle


These basic settings are typically stored with the template, but you can also send them through [API calls](/learn/api)  inside the `template.chromeImage` property.

The options can be also dynamically set from within the page javascript using:

```js
<script>
	...
	window.JSREPORT_CHROME_IMAGE_OPTIONS = {
		type:  'jpeg'
	}
</script>
```

you can customize how the chrome page render the content by using `viewportWidth` and `viewportHeight`, this will have an impact in how the html layout is rendered, which will cause the image to look different depending if you use bigger or smaller viewport.

## chrome-pdf

The `chrome-image` recipe is part of the same extension as [chrome-pdf](/learn/chrome-pdf) and it shares its configuration. This means you can increase the timeout and other options the same way.
```js
"extensions": {
  "chrome-pdf": {
    "timeout": 30000,
    "launchOptions": {...}
  }
}
```

Not only the configuration but also many aspects are the same. You can for example  [configure printing triggers](/learn/chrome-pdf#printing-triggers), [embed fonts](/learn/chrome-pdf#fonts), [reuse chrome instances](/learn/chrome-pdf#reuse-chrome-instances) or even [troubleshoot](http://localhost:2000/learn/chrome-pdf#troubleshooting) it the same way. Refer to [chrome-pdf](/learn/chrome-pdf) for details.
