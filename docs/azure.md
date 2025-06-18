

## Virtual Machines
You can use the installation manuals for [Ubuntu](/learn/ubuntu), [CentOS](/learn/centos),  [Windows](/learn/windows), or [Docker](https://hub.docker.com/r/jsreport/jsreport/). The size of the VM requirement is relative to your expected load. However, even a small instance is enough for small loads.

## Web app 
jsreport runs in Azure Web Apps with docker based deployment. Both Windows and Linux Azure web apps are supported. You can find the list of jsreport docker images as well as the Docker documentation [here](/learn/docker). 

See also the dedicated article on how to [render reports using Azure Web App and jsreport](/learn/azure-web-apps).

Note jsreport won't run in "none docker" Azure Web App because of the platform limitations.

## Azure Function Apps
The Azure Function Apps runtime doesn't include the libs that Chromium requires. Therefore, the chrome-pdf recipe won't work there. One option is to use the premium plan and install the missing libs, or use Docker container. However, Microsoft now recommends using Azure Container Apps for such use-cases.

## Azure Container Apps
See the dedicated tutorial [here](/learn/azure-container-apps).

## AKS
You can use [jsreport default docker image](https://hub.docker.com/r/jsreport/jsreport/), which runs just fine in Kubernetes.