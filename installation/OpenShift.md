#OpenShift

This page contain jsreport installation notes for OpenShift by RedHat

jsreport starts internally several web servers to optimize and distribute report rendering. This is a problem for OpenShift because it unlocks for your application only specific host and specific port range.  Fortunately it can be easily configured in jsreport to start up internal web servers on specific host and specific port range.

To configure jsreport for OpenShift edit `server.js` and add following:
```js
var options = {
	tasks: {
		host: process.env.OPENSHIFT_NODEJS_IP,
		portLeftBoundary: 15000,
		portRightBoundary: 35530
	},
	phantom: {
		host: process.env.OPENSHIFT_NODEJS_IP,
		portLeftBoundary: 15000,
		portRightBoundary: 35530
	}
}

require('jsreport').bootstrapper(options).start();
```


#Starting with jsreport on OpenShift (step by step instruction)

This description helps you get up and running quickly w/ a jsreport installation on OpenShift.

Running jsreport app on OpenShift
-------------------------------

Create an account at https://www.openshift.com

Install git, rubygems and rhc client for interacting with openshift infrastructure.
Detailed instructions per OS are available at https://www.openshift.com/developers/rhc-client-tools-install.

Create a nodejs-0.10 application using rhc:

    rhc app create <appname> nodejs-0.10

you can now checkout your template application at:

    http://<appname>-<yournamespace>.rhcloud.com

Now you have to add jsreport - modify the template code:

###1. add jsreport

	npm install jsreport --save


###2. check package.json

The application should include a package.json file in the root of their project. OpenShift and npm are both configured to check your (scripts.start and main) fields for information on how to launch your server. So be sure that you have these files and jsreport in dependencies:

```js
{
	"name": "Your-jsreportOpenShift-App",

	...

	"dependencies": {
		"jsreport": "^0.5.0"
	},
	"scripts": {
			"start": "node server.js"
	},
	"main": "server.js"
}
```

###3. modyif server.js

OpenShift’s Node.js cartridge automatically publishes server connection information to your application’s environment via the following environment variables: OPENSHIFT_NODEJS_PORT and OPENSHIFT_NODEJS_IP

Include a small check to test for the presence of these configuration strings to ensure your project’s portability:

```js
#!/bin/env node
 //  OpenShift jsreport Node application

var servHost = process.env.OPENSHIFT_NODEJS_IP || '127.0.0.1';
var servPort = process.env.OPENSHIFT_NODEJS_PORT || 3000;
var jsreport = require('jsreport');
var app = require('express')();

console.log('Starting jsreport on ' + servHost + ':' + servPort);

require('jsreport').bootstrapper({
    express: {
        app: app
    },
    options : {
        tasks: {
            host: servHost,
            portLeftBoundary: 15000,
            portRightBoundary: 35530
        },
        phantom: {
            host: servHost,
            portLeftBoundary: 15000,
            portRightBoundary: 35530
        }
    }
}).start().then(function() {
    app.listen(servPort, servHost);
    console.log((new Date()) + ' Server is listening on ' + servHost + ':' + servPort);

}).catch(function(e) {
    console.log('Error: ' + err);
});
```

###2. add dev / prod.config.json for jsreport
Your jsreport application should include a dev / prod.config.json file in the root of their project. In this file you can configure jsreport, detailed instructions are available at: ...https://github.com/jsreport/docs/blob/master/docs/configuration.md

for the start we can use a very simple configuration like below:
```js
{
    "connectionString": {
        "name": "neDB"
    },
    "blobStorage": "inMemory"
}
```
###4. add dev / prod.config.json for jsreport
Your jsreport application should include a dev / prod.config.json file in the root of their project. In this file you can configure jsreport, detailed instructions are available at: ...https://github.com/jsreport/docs/blob/master/docs/configuration.md

for the start we can use a very simple configuration like below:
```js
{
    "connectionString": {
        "name": "neDB"
    },
    "blobStorage": "inMemory"
}
```

###5. push your jsreport app to OpenShift
commit and push back up to your OpenShift application using:
		git add --all
    git commit -m "jsreport added"
    git push

Done
-----------
you can now checkout your working jsreport application at:

	http://<appname>-<yournamespace>.rhcloud.com
