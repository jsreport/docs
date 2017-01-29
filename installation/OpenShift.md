#Starting with jsreport on OpenShift (step by step instruction)

This description helps you get up and running quickly with a jsreport installation on OpenShift.

##Running jsreport app on OpenShift

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

The application should include a package.json file in the root of their project. OpenShift and npm are both configured to check your (scripts.start and main) fields for information on how to launch your server. So be sure that you have these files and the jsreport in dependencies:

```js
{
	"name": "Your-jsreportOpenShift-App",

	...

	"dependencies": {
		"jsreport": "^0.6.0"
	},
	"scripts": {
			"start": "node server.js"
	},
	"main": "server.js"
}
```

###3. modify server.js

OpenShift’s Node.js cartridge automatically publishes server connection information to your application’s environment via the environment variables. These values needs to be forwarded to jsreport in this case in `server.js`.

```js
#!/bin/env node
//  OpenShift jsreport Node application

var jsreport = require('jsreport');

var servHost = process.env.OPENSHIFT_NODEJS_IP || '127.0.0.1';
var servPort = process.env.OPENSHIFT_NODEJS_PORT || 5000;

var options = { 
  tasks: {
    host: servHost,
    portLeftBoundary: 15000,
    portRightBoundary: 35530
  },
  phantom: {
    host: servHost,
    portLeftBoundary: 15000,
    portRightBoundary: 35530
  },
  hostname: servHost,
  httpPort: servPort
};


jsreport.bootstrapper(options).start().then(function() {
  console.log("isreport is running!");
}).catch(function(e) {
  console.log(e);
});
```

###4. push your jsreport app to OpenShift
commit and push back up to your OpenShift application using:
		git add --all
    git commit -m "jsreport added"
    git push

##Done

You can now checkout your working jsreport application at:

	http://<appname>-<yournamespace>.rhcloud.com

See the [documentation](https://jsreport.net/learn) for other options you can use for jsreport like adding authentication/authotization or how to use [config files](https://github.com/jsreport/docs/blob/master/docs/configuration.md).

<br /> <br />
Appendix
-----------
###Store the data in MongoDB on OpenShift

####1. add the mongodb cartridge on OpenShift

	$ rhc cartridge add mongodb-2.4 -a <appname>

####2. add the mongodb node package to the app

	$ npm install mongodb --save

####3. modify server.js

The mongodb cartridge provides several environment variables, we need to reference these variables in our app to be able to connect with MongoDB on OpenShift from our jsreport app.

#####3.1 variables
```js
var dbHost = process.env.OPENSHIFT_MONGODB_DB_HOST || '127.0.0.1';
var dbPort = process.env.OPENSHIFT_MONGODB_DB_PORT || 27017;
var dbUser = process.env.OPENSHIFT_MONGODB_DB_USERNAME || 'admin';
var dbPass = process.env.OPENSHIFT_MONGODB_DB_PASSWORD || 'secret';
```
#####3.2 options
```js
connectionString: {
		name: 'mongoDB',
		address: dbUser + ':' + dbPass + '@' + dbHost,
		port: dbPort,
		databaseName: <appname>
}
```
####4. push your changed app to OpenShift
commit and push back up to your OpenShift application using:
		git add --all
    git commit -m "mongodb added"
    git push
