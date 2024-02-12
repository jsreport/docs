
## EC2
This the most common option. Launch a new VM and use the installation manuals for [Ubuntu](/learn/ubuntu), [RedHat](/learn/red-hat), [Amazon Linux](/learn/amazon-linux), [Windows](/learn/windows) or [Docker](https://hub.docker.com/r/jsreport/jsreport/). The size of the VM requirement is relative to your expected load. However even the micro instance is enough for small loads.

jsreport by default stores data on the local file system. This can be fine for single server scenario, but if you want to run multiple instances you need to do further setups. 

One option is to map an external disk to the multiple VM instances. Another option is to use S3 storage or for example mongodb for persisting templates. See all of the options in the [template stores documentation](/learn/template-stores).

## Lambda
You can run jsreport also in [AWS Lambda](https://aws.amazon.com/lambda). See the [dedicated tutorial here](/learn/aws-lambda-serverless) for the details. 

## Elastic Beanstalk

You can use Elastic Beanstalk with Docker and EFS to startup jsreport. In this case see the complete tutorial [Running jsreport on AWS EB, Docker and EFS](https://jsreport.net/learn/aws-eb-docker-efs).

## Elastic container service

We don't have a dedicated notes for this so far, however you can start jsreport here as any other docker container.  See the [notes for docker installation](https://hub.docker.com/r/jsreport/jsreport/).


