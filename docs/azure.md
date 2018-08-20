
## Virtual Machines
This the most common option. Launch a new VM and use the installation manuals for [Ubuntu](/learn/ubuntu), [CentOS](/learn/centos),  [Windows](/learn/windows) or [Docker](https://hub.docker.com/r/jsreport/jsreport/). The size of the VM requirement is relative to your expected load. However even the small instance is enough for small loads.

jsreport by default stores data on the local file system. This can be fine for single server scenario, but if you want to run multiple instances you need to do further setups. 

One option is to map an external disk to the multiple VM instances. Another option is to use azure blob storage or for example SQL server. See all of the options in the [template stores documentation](/learn/template-stores).

## Web app 
The easiest is to use Azure Web App with Docker. See the dedicated article [render reports using azure app service](/blog/render-reports-using-azure-app-service).

## Cloud services
The classic cloud service should be able to host jsreport as well. You just need to add installation commands to the role startup.

## Azure functions
We weren't able to make this working so far because of the platform limitations.