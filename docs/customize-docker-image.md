Do you need to create your own jsreport docker image, because you want to install additional node modules for scripts or helpers? Or you want to create an image that contains your prepared templates? This is very easy to do...

## The first steps
Assuming you have docker running, create a custom folder with `Dockerfile` file inside.

```
FROM jsreport/jsreport:2.7.2
```

Then run
```
docker build . -t myjsreport
docker run -p 5488:5488 myjsreport
```
You should see jsreport running on port 5488 afterwards.

## Use your own config file
Create your config file `jsreport.config.json`
```js
{
	"allowLocalFilesAccess": true,	
}
```
And edit `Dockerfile` to include it in the image
```
FROM jsreport/jsreport:2.7.2
COPY --chown=jsreport:jsreport jsreport.config.json /app
```
Rebuild and run the image again. You should be able to `require` custom modules now.

## Install custom modules
Add custom module installation step to your `Dockerfile`.

```
FROM jsreport/jsreport:2.7.2
RUN npm i handlebars-intl --save
COPY --chown=jsreport:jsreport jsreport.config.json /app
```

Rebuild the image and run it. You should be able to use [handlebars-intl](https://github.com/formatjs/handlebars-intl) package now. You just need to register it in the template's helpers section.
```js
const handlebars = require('handlebars')
const HandlebarsIntl = require('handlebars-intl')
HandlebarsIntl.registerWith(handlebars)
```
## Copy templates to the image
Run your local jsreport and prepare your templates. Then copy the data folder to the same folder you have `Dockerfile`. Now add the following to the `Dockerfile`.

```
FROM jsreport/jsreport:2.7.2
RUN npm i handlebars-intl --save
COPY --chown=jsreport:jsreport jsreport.config.json /app
COPY --chown=jsreport:jsreport /data /app/data
```
And configure jsreport to use [fs store](/learn/fs-store) using `jsreport.config.json`.

```js
{
	"allowLocalFilesAccess": true,
	"store": {
		"provider": "fs"
	}
}
```

Rebuild the image and run it. You should see your templates in the studio. 
This way you can easily version your templates inside git and be sure your specific docker image has always the exact templates.

## Publish the image
Now you can put the sources to the [github](https://github.com) and let the [dockerhub](https://hub.docker.com/) do the automatic docker build and publish. You just need to push the desired templates in the data folder to the git and let it trigger the build. Or use docker registry of your choice. This is fully up to you.