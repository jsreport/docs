
## Tags

[jsreport/jsreport](https://hub.docker.com/r/jsreport/jsreport/) image is automatically pushed with adequate tags into [docker hub](https://www.docker.com/)  public repository in three variations:

- `jsreport/jsreport:4.10.0` ([Dockerfile](https://github.com/jsreport/jsreport/blob/4.10.0/packages/jsreport/docker/default/Dockerfile))  contains default installation from npm
- `jsreport/jsreport:4.10.0-full` ([Dockerfile](https://github.com/jsreport/jsreport/blob/4.10.0/packages/jsreport/docker/full/Dockerfile)) contains default installation plus all the custom extensions, see the list of installed extensions in the dockerfile
- `jsreport/jsreport:4.10.0-windowsservercore` ([Dockerfile](https://github.com/jsreport/jsreport/blob/4.10.0/packages/jsreport/docker/windowsservercore/Dockerfile)) contains default installation with windows server core based image. Use `c:\jsreport` for mounting volume.
- `jsreport/jsreport:nightly` ([Dockerfile](https://github.com/jsreport/jsreport/blob/master/packages/jsreport/docker/default/Dockerfile.nightly))  the default image build running every night

You can find the [list of all available tags and previous versions in the docker hub](https://hub.docker.com/r/jsreport/jsreport/tags/)

## Usage
1. Install [Docker](https://www.docker.com/)
2. `docker run -p 5488:5488 -e "chrome_launchOptions_args=--no-sandbox, --disable-dev-shm-usage, --disable-gpu" jsreport/jsreport:4.10.0`

Start jsreport server on the port 5488 directly in the current shell with data and configuration stored directly in the container.

## Usage on macOS Apple Silicon hardware

To run the docker images on macOS with [Apple Silicon](https://en.wikipedia.org/wiki/Apple_silicon) hardware, you have two options:

- use the `arm64` build of the image, each jsreport image (`default`, `full`) is build for the linux architectures `linux/amd64`, `linux/arm64`, for the Apple Silicon hardware the fastest and the most stable option is to use the `linux/arm64` architecture

	```sh
	docker run --platform=linux/arm64 -p 5488:5488 jsreport/jsreport:4.10.0
	```

	the jsreport docker full arm image requires the usage of custom executable path

	```sh
	docker run --platform=linux/arm64 -p 5488:5488 -e "chrome_launchOptions_executablePath=/usr/bin/chromium" jsreport/jsreport:4.10.0-full
	```
- if you have `Use Rosetta for x86/amd64 emulation on Apple Silicon` enabled in Docker Desktop macos, **you may be** able to use the command without extra options:

	```sh
	docker run --platform=linux/amd64 -p 5488:5488 jsreport/jsreport:4.10.0
	```

	![docker-desktop-macos-rosetta](/learn/static-resources/docker-desktop-macos-rosetta.png)

	or if for some reason the image in the `linux/arm64` does not work or if the `linux/amd64` with Rosetta enabled in docker does not work, you can still use the image for the `linux/amd64`, docker for macOS allows to either use the fastest `linux/arm64` or the `linux/amd64` (through virtualization, that is why it is expected to be slower compared to the `linux/arm64`). To do this you need to pass a flag `--platform linux/amd64` to docker and pass some additional chrome options. The docker run command for it looks like this:

	```sh
	docker run --platform=linux/amd64 -p 5488:5488 -e "chrome_launchOptions_args=--no-sandbox, --disable-dev-shm-usage, --disable-gpu" jsreport/jsreport:4.10.0
	```

## Start on reboot

You may want to additionally run the container as a daemon and restart it on system reboot
```sh
docker run -d -p 5488:5488 --restart always jsreport/jsreport:4.10.0
```

## Configuring jsreport

The easiest way is to pass the [configuration](https://jsreport.net/learn/configuration) as environment variables. The [authentication](http://jsreport.net/learn/authentication) can be for example applied in this way

```sh
sudo docker run -e "extensions_authentication_admin_username=admin" -e "extensions_authentication_admin_password=xxx" -e "extensions_authentication_cookieSession_secret=yyylong" -p 5488:5488 jsreport/jsreport:4.10.0
```

Notice that `_` is used as a separator for nested configuration properties. This works usually better in docker environments than also supported `:` separator.

Another option is mount the configuration file.
```sh
docker run -p 5488:5488 -v /mylocalapp/jsreport.config.json:/app/jsreport.config.json jsreport/jsreport:4.10.0
```

## Persist templates

The templates are by default persisted inside the container. You may rather want to store them in a mounted directory or in an external [template store](/learn/template-stores).

```sh
docker run -p 5488:5488 -v /mydata:/app/data jsreport/jsreport:4.10.0
```

## Mount app directory

You can mount a directory with data, license key, and config files at once.  This can be done by mounting to the path `/jsreport`.
```sh
sudo docker run -p 80:5488 -v /myapp:/jsreport jsreport/jsreport:4.10.0
```

## Apply license key

You can apply your license key into the jsreport container using one of the following methods:

- pass the license key value through an env var  `docker run -e "licenseKey=xxyour-key-herexx" -p 80:5488 jsreport/jsreport:4.10.0`
- [mount app directory](#mount-app-directory) with file `license-key.txt` containing the key
- mount just file with license key `docker run -v mykey.txt:/app/license-key.txt -p 5488:5488`

## Persist in an external database

The full image like `jsreport/jsreport:4.10.0-full` has all the custom data stores like [mongodb](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-mongodb-store), [mssql](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-mssql-store) or [PostgreSQL](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-postgres-store) already installed. You only need to properly configure the `store` environment variable. For example

```sh
sudo docker run -e "store_provider=mssql" -e "extensions_mssqlStore_uri=Server=tcp:jsreport.database.windows.net,1433;Initial Catalog=jsreport;Persist Security Info=False;User ID=myuser;Password=password;MultipleActiveResultSets=False;Encrypt=True;" -p 80:5488 jsreport/jsreport:4.10.0-full
```

## Customize docker image

Do you need to create your own jsreport docker image, because you want to install additional node modules for scripts or helpers? Or you want to create an image that contains your prepared templates? This is very easy to do...

### The first steps
Assuming you have docker running, create a custom folder with `Dockerfile` file inside.

```
FROM jsreport/jsreport:4.10.0
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
FROM jsreport/jsreport:4.10.0
COPY --chown=jsreport:jsreport jsreport.config.json /app
```
Rebuild and run the image again. You should be able to `require` custom modules now.

### Install custom modules
Add custom module installation step to your `Dockerfile`.

```
FROM jsreport/jsreport:4.10.0
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
FROM jsreport/jsreport:4.10.0
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
docker run -p 5488:5488 --read-only --tmpfs=/tmp -e store_provider=memory -e blobStorage_provider=memory -e logger_file_filename=/tmp/reporter.log -e logger_error_filename=/tmp/error.log -e XDG_CONFIG_HOME=/tmp/.chromium -e XDG_CACHE_HOME=/tmp/.chromium jsreport/jsreport:4.10.0
```

Note in [AWS Lambda](https://jsreport.net/learn/aws-lambda-serverless) is the `/tmp` already writable and you only need to configure the logger and store.

### With stored templates

In case you use an external [template store](https://jsreport.net/learn/template-stores), you can use the previous command and additionally configure jsreport to use your database.

In case you want to mount a volume with your templates you can do it the following way. Then only the temp and the data volume will be writable, the rest of the container stays read-only.

```bash
docker run -p 5488:5488 --read-only --tmpfs=/tmp -e logger_file_filename=/tmp/reporter.log -e logger_error_filename=/tmp/error.log -v /mydata:/app/data -e XDG_CONFIG_HOME=/tmp/.chromium -e XDG_CACHE_HOME=/tmp/.chromium jsreport/jsreport:4.10.0
```

You may want to ship your templates directly inside the container. This is another approach. In this case, you typically create your `Dockerfile` with a `COPY` part to add the templates. Because you want to run the container in the read-only mode, you need to additionally copy the templates to a temp location during the container start.

```bash
FROM jsreport/jsreport:4.10.0
COPY --chown=jsreport:jsreport ./mydata /mydata
CMD ["sh", "-c", "cp -R /mydata /tmp/data&&node server.js"]

```

Running such command is the same as for previous cases, the only required change is configuring the file system store to get templates from the temp location.
```bash
docker run -p 5488:5488 --read-only --tmpfs=/tmp -e extensions_fsStore_dataDirectory=/tmp/data -e blobStorage_dataDirectory=/tmp/data/storage -e logger_file_filename=/tmp/reporter.log -e logger_error_filename=/tmp/error.log myimage
```

### Fonts in default image
The default image based on Alpine Linux includes only the default fonts compared to the full image. If you notice problems with fonts in the default image that don't occur in the full image, you may install additional fonts like this.
```dockerfile
FROM jsreport/jsreport:4.10.0

RUN set -x \
    && apk add --no-cache \
    font-terminus font-inconsolata font-dejavu font-noto font-noto-cjk font-awesome font-noto-extra
```

If you need additional fonts, you can look into the Alpine documentation
https://wiki.alpinelinux.org/wiki/Fonts
