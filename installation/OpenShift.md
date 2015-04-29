#OpenShift

This page contain jsreport installation notes for OpenShift by RedHat

jsreport starts internally several web servers to optimize and distribute report rendering. This is a problem for OepnShift because it unlocks for your application only specific host and specific port range.  Fortunately it can be easily configured in jsreport to start up internal web servers on specific host and specific port range.

To configure jsreport for OepnShift edit `server.js` and add following:
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





