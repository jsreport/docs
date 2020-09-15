
## Virtual Machines
This the most common option. Launch a new VM and use the installation manuals for [Ubuntu](/learn/ubuntu), [CentOS](/learn/centos),  [Windows](/learn/windows) or [Docker](https://hub.docker.com/r/jsreport/jsreport/). The size of the VM requirement is relative to your expected load. However even the small instance is enough for small loads.

jsreport by default stores data on the local file system. This can be fine for a single server scenario, but if you want to run multiple instances you need to use a full database store or map an external disk and configure jsreport default store to run in the cluster. See details in the [template stores documentation](/learn/template-stores).

## Web app 
jsreport runs in Azure Web Apps with docker based deployment. Both Windows and Linux Azure web apps are supported. You can find the list of jsreport docker images as well as the docker documentation [here](/learn/docker). See also dedicated article [render reports using azure app service](/blog/render-reports-using-azure-app-service).

Note jsreport won't run in "none docker" Azure Web App because of the platform limitations.

## Cloud services
The classic cloud service should be able to host jsreport as well. You just need to add installation commands to the role startup.

## Azure functions
jsreport runs also in serverless Azure Functions, see the [dedicated tutorial here](/learn/azure-functions-serverless).

## AKS
You can use [jsreport default docker image](https://hub.docker.com/r/jsreport/jsreport/) which runs just fine in Kubernetes.