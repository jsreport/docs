
## Virtual Machines
This the most common option. Launch a new VM and use the installation manuals for [Ubuntu](/learn/ubuntu), [CentOS](/learn/centos),  [Windows](/learn/windows) or [Docker](https://hub.docker.com/r/jsreport/jsreport/). The size of the VM requirement is relative to your expected load. However even the small instance is enough for small loads.

jsreport by default stores data on the local file system. This can be fine for single server scenario, but if you want to run multiple instances you need to use a full database store or map an external disk and configure jsreport default store to run in cluster. See details in the [template stores documentation](/learn/template-stores).

## Web app 
The windows based Azure Web Apps, unfortunately, block some libraries chrome uses so you won't be able to generate pdf on this platform. However, you can use Linux based Azure Web Apps with docker which doesn't have this issue. See the dedicated article [render reports using azure app service](/blog/render-reports-using-azure-app-service).

## Cloud services
The classic cloud service should be able to host jsreport as well. You just need to add installation commands to the role startup.

## Azure functions
We weren't able to make this working so far because of the platform limitations.

## AKS
You can use [jsreport default docker image](https://hub.docker.com/r/jsreport/jsreport/) which runs just fine in kubernetes.