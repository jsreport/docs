

# Basics
`Chrome-image` recipe is using [headless chrome](https://developers.google.com/web/updates/2017/04/headless-chrome) to print html content into image files.

## Options

The settings reflects the [headless chrome API](https://github.com/GoogleChrome/puppeteer/blob/v1.11.0/docs/api.md#pagescreenshotoptions) where you can also find detail information.

- type
- quality
- fullPage
- clipX
- clipY
- clipWidth
- clipHeight
- omitBackground
- waitForJS
- waitForNetworkIddle


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


## Configuration

Since this recipe is part of `chrome-pdf` extension the configuration node in the standard [config](/learn/configuration) file looks like this:

```js
"extensions": {
  "chrome-pdf": {  
    "timeout": 30000,
    "launchOptions": {...}
  }
}
```

to find more information about what is available in `launchOptions` configuration object you can check the [docs here](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#puppeteerlaunchoptions).

you can also use top level `chrome` property in configuration, the difference is that this configuration will be shared with any other extension that uses chrome and the configuration snippet above is specifically for options in `chrome-pdf` extension.

```js
"chrome": {
  "timeout": 30000
}
```

## Fonts

The fonts can be easily embedded into chrome page using the [assets](https://jsreport.net/learn/assets) extension. You can find the tutorial on how to do it [here](https://jsreport.net/blog/fonts-in-pdf).

## Printing triggers
You may need to postpone capturing the image until some javascript async tasks are processed. If this is your case set the `chromeImage.waitForJS=true` in the API or `Wait for printing trigger` in the studio menu. Then the printing won't start until you set `window.JSREPORT_READY_TO_START=true` inside your template's javascript.
```html
...
<script>
    // do some calculations or something async
    setTimeout(function() {
        window.JSREPORT_READY_TO_START = true; //this will start the pdf printing
    }, 500);
    ...
</script>
```

## Reuse chrome instances
The recipe by default starts extra new chrome process every time you render a template. This behavior can be changed and recipe configured to reuse several chrome instances to improve rendering performance.

```js
{
  "extensions": {
    "chrome-pdf": {
      "strategy": "chrome-pool",
      "numberOfWorkers": 3
    }
  }
}
```

## Troubleshooting

chrome/puppeteer doesn't run by default in limited environment like docker and it usually asks to pass `--no-sandbox` argument. This can be achieved using the following config. See also [puppeteer troubleshooting](https://github.com/GoogleChrome/puppeteer/blob/master/docs/troubleshooting.md).

```js   
{
  "extensions": {  
    "chrome-pdf": {  
      "launchOptions": {  
        "args": ["--no-sandbox"]  
      }
    }
  }
}
```
