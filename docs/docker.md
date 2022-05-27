## Tags

[jsreport/jsreport](https://hub.docker.com/r/jsreport/jsreport/) image is automatically pushed with adequate tags into [docker hub](https://www.docker.com/)  public repository in three variations:

- `jsreport/jsreport:3.6.1` ([Dockerfile](https://github.com/jsreport/jsreport/blob/master/packages/jsreport/docker/default/Dockerfile))  contains default installation from npm
- `jsreport/jsreport:3.6.1-full` ([Dockerfile](https://github.com/jsreport/jsreport/blob/master/packages/jsreport/docker/full/Dockerfile)) contains default installation plus all the custom extensions, see the list of installed extensions in the dockerfile
- `jsreport/jsreport:3.6.1-windowsservercore` ([Dockerfile](https://github.com/jsreport/jsreport/blob/master/packages/jsreport/docker/windowsservercore/Dockerfile)) contains default installation with windows server core based image. Use `c:\jsreport` for mounting volume.

You can find the [list of all available tags and previous versions in the docker hub](https://hub.docker.com/r/jsreport/jsreport/tags/)

## Usage
1. Install [Docker](https://www.docker.com/)
2. `docker run -p 5488:5488 jsreport/jsreport:3.6.1`

Start jsreport server on the port 5488 directly in the current shell with data and configuration stored directly in the container.

## Start on reboot

You may want to additionally run the container as a daemon and restart it on system reboot
```sh
docker run -d -p 5488:5488 --restart always jsreport/jsreport:3.6.1
```

## Configuring jsreport

The easiest way is to pass the [configuration](https://jsreport.net/learn/configuration) as environment variables. The [authentication](http://jsreport.net/learn/authentication) can be for example applied in this way

```sh
sudo docker run -e "extensions_authentication_admin_username=admin" -e "extensions_authentication_admin_password=xxx" -e "extensions_authentication_cookieSession_secret=yyylong" -p 5488:5488 jsreport/jsreport:3.6.1
```

Notice that `_` is used as a separator for nested configuration properties. This works usually better in docker environments than also supported `:` separator.

Another option is mount the configuration file.
```sh
docker run -p 5488:5488 -v /mylocalapp/jsreport.config.json:/app/jsreport.config.json jsreport/jsreport:3.6.1
```

## Persist templates

The templates are by default persisted inside the container. You may rather want to store them in a mounted directory or in an external [template store](/learn/template-stores).

```sh
docker run -p 5488:5488 -v /mydata:/app/data jsreport/jsreport:3.6.1
```

## Mount app directory

You can mount a directory with data, license key, and config files at once.  This can be done by mounting to the path `/jsreport`.
```sh
sudo docker run -p 80:5488 -v /myapp:/jsreport jsreport/jsreport:3.6.1
```

## Apply license key

You can apply your license key into the jsreport container using one of the following methods:

- pass the license key value through an env var  `docker run -e "licenseKey=xxyour-key-herexx" -p 80:5488 jsreport/jsreport:3.6.1`
- [mount app directory](#mount-app-directory) with file `license-key.txt` containing the key
- mount just file with license key `docker run -v mykey.txt:/app/license-key.txt -p 5488:5488`

## Persist in an external database

The full image like `jsreport/jsreport:3.6.1-full` has all the custom data stores like [mongodb](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-mongodb-store), [mssql](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-mssql-store) or [PostgreSQL](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-postgres-store) already installed. You only need to properly configure the `store` environment variable. For example

```sh
sudo docker run -e "store_provider=mssql" -e "extensions_mssqlStore_uri=Server=tcp:jsreport.database.windows.net,1433;Initial Catalog=jsreport;Persist Security Info=False;User ID=myuser;Password=password;MultipleActiveResultSets=False;Encrypt=True;" -p 80:5488 jsreport/jsreport:3.6.1-full
```

## Customize docker image

Do you need to create your own jsreport docker image, because you want to install additional node modules for scripts or helpers? Or you want to create an image that contains your prepared templates? This is very easy to do...

### The first steps
Assuming you have docker running, create a custom folder with `Dockerfile` file inside.

```
FROM jsreport/jsreport:3.6.1
```

Then run
```
docker build . -t myjsreport
docker run -p 5488:5488 myjsreport
```
You should see jsreport running on port 5488 afterwards.

### Use your own config file
Create your config file `jsreport.config.json`
```js
{
	"trustUserCode": true,
}
```
And edit `Dockerfile` to include it in the image
```
FROM jsreport/jsreport:3.6.1
COPY --chown=jsreport:jsreport jsreport.config.json /app
```
Rebuild and run the image again. You should be able to `require` custom modules now.

### Install custom modules
Add custom module installation step to your `Dockerfile`.

```
FROM jsreport/jsreport:3.6.1
RUN npm i handlebars-intl --save
COPY --chown=jsreport:jsreport jsreport.config.json /app
```

Rebuild the image and run it. You should be able to use [handlebars-intl](https://github.com/formatjs/handlebars-intl) package now. You just need to register it in the template's helpers section.
```js
const handlebars = require('handlebars')
const HandlebarsIntl = require('handlebars-intl')
HandlebarsIntl.registerWith(handlebars)
```
### Copy templates to the image
Run your local jsreport and prepare your templates. Then copy the data folder to the same folder you have `Dockerfile`. Now add the following to the `Dockerfile`.

```
FROM jsreport/jsreport:3.6.1
RUN npm i handlebars-intl --save
COPY --chown=jsreport:jsreport jsreport.config.json /app
COPY --chown=jsreport:jsreport /data /app/data
```
And configure jsreport to use [fs store](/learn/fs-store) using `jsreport.config.json`.

```js
{
	"trustUserCode": true,
	"store": {
		"provider": "fs"
	}
}
```

Rebuild the image and run it. You should see your templates in the studio.
This way you can easily version your templates inside git and be sure your specific docker image has always the exact templates.

### Publish the image
Now you can put the sources to the [github](https://github.com) and let the [dockerhub](https://hub.docker.com/) do the automatic docker build and publish. You just need to push the desired templates in the data folder to the git and let it trigger the build. Or use docker registry of your choice. This is fully up to you.

## Readonly containers
A common requirement is to run containers in readonly environments like [AWS Lambda](https://jsreport.net/learn/aws-lambda-serverless).  The following explains the solutions for specific cases.

### Without stored templates

jsreport always needs a writable temp folder, fortunately, docker provides in-memory temp using  [tmpfs](https://docs.docker.com/storage/tmpfs/)  switch. Then it is just about configuring [template store](https://jsreport.net/learn/template-stores]) to run only in memory and also [logger](https://jsreport.net/learn/configuration#logging-configuration) to write to the temp instead of the app folder.

```bash
docker run -p 5488:5488 --read-only --tmpfs=/tmp -e store_provider=memory -e blobStorage_provider=memory -e logger_file_filename=/tmp/reporter.log -e logger_error_filename=/tmp/error.log jsreport/jsreport:3.6.1
```

Note in [AWS Lambda](https://jsreport.net/learn/aws-lambda-serverless) is the `/tmp` already writable and you only need to configure the logger and store.

### With stored templates

In case you use an external [template store](https://jsreport.net/learn/template-stores), you can use the previous command and additionally configure jsreport to use your database.

In case you want to mount a volume with your templates you can do it the following way. Then only the temp and the data volume will be writable, the rest of the container stays read-only.

```bash
docker run -p 5488:5488 --read-only --tmpfs=/tmp -e logger_file_filename=/tmp/reporter.log -e logger_error_filename=/tmp/error.log -v /mydata:/app/data jsreport/jsreport:3.6.1
```

You may want to ship your templates directly inside the container. This is another approach. In this case, you typically create your `Dockerfile` with a `COPY` part to add the templates. Because you want to run the container in the read-only mode, you need to additionally copy the templates to a temp location during the container start.

```bash
FROM jsreport/jsreport:3.6.1
COPY --chown=jsreport:jsreport ./mydata /mydata
CMD ["sh", "-c", "cp -R /mydata /tmp/data&&node server.js"]

```

Running such command is the same as for previous cases, the only required change is configuring the file system store to get templates from the temp location.
```bash
docker run -p 5488:5488 --read-only --tmpfs=/tmp -e extensions_fsStore_dataDirectory=/tmp/data -e blobStorage_dataDirectory=/tmp/data/storage -e logger_file_filename=/tmp/reporter.log -e logger_error_filename=/tmp/error.log myimage
```
