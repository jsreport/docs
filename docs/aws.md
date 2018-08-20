## EC2
This the most common option. Launch a new VM and use the installation manuals for [Ubuntu](/learn/ubuntu), [CentOS](/learn/centos), [Amazon Linux](/learn/amazon-linux), [Windows](/learn/windows) or [Docker](https://hub.docker.com/r/jsreport/jsreport/). The size of the VM requirement is relative to your expected load. However even the micro instance is enough for small loads.

jsreport by default stores data on the local file system. This can be fine for single server scenario, but if you want to run multiple instances you need to do further setups. 

One option is to map an external disk to the multiple VM instances. Another option is to use S3 storage or for example mongodb for persisting templates. See all of the options in the [template stores documentation](/learn/template-stores).

## Elastic Beanstalk

You can use Elastic Beanstalk docker to startup jsreport. In this case see the [notes for docker installation](https://hub.docker.com/r/jsreport/jsreport/).

In case you want to create the application from scratch and upload the package archive to AWS see article [jsreport on amazon beanstalk](/blog/jsreport-on-amazon-elastic-beanstalk). Or follow these steps
1.  Prepare local jsreport installation using the Windows or Unix installation steps
2.  Zip package.json, jsreport.config.json and server.js
3.  Create Amazon Elastic Beanstalk Web Server with Node.js predefined configuration
4.  Upload previously zipped package


## Elastic container service

We don't have a dedicated notes for this so far, however you can start jsreport here as any other docker container.  See the [notes for docker installation](https://hub.docker.com/r/jsreport/jsreport/).

## Lambda
We plan to try the AWS Lambda with jsreport soon. In the past it was very difficult to run a big project like google chrome there, but there are rumors it is now possible. Please let us know if you had any luck with jsreport there.