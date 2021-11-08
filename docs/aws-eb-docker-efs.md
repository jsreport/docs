jsreport runs cross platform almost everywhere and [AWS Elastic Beanstalk](https://aws.amazon.com/documentation/elastic-beanstalk/) is not an exception. It just takes couple of minutes to have it running on the free tier. Here are some notes and a guidance how to make it run there.

## Create Elastic File System
In most of the cases you want to persist templates and use one of the jsreport [template store drivers](/learn/template-stores). The most common one is the default file system driver which we will use also in this tutorial. It's easy for start and later you can scale to the full-blown database if you have the need. Or choose the [s3 driver](/learn/fs-store#aws-s3) which may be a good choice for you as well.

The first step is to create [AWS Elastic File System](https://aws.amazon.com/efs/). Use AWS console to create it and fill just the name and in the network access tab select group with inbound rule to allow port 2049.

![create elastic fs](/learn/static-resources/aws-ebt-create-fs.png)

## Prepare config files
The second step is to prepare AWS Elastic Beanstalk configuration. The configuration sample can be downloaded/cloned form the [jsreport-aws-eb-docker-ef github repository](https://github.com/jsreport/jsreport-aws-eb-docker-efs).

One thing you need to do there is to edit file `.ebextensions/mount.config` and replace line `FILE_SYSTEM_ID: xxx` with the file system id you created in the previous step. In the end the line will look like `FILE_SYSTEM_ID: fs-e7605f4e`

The next thing is to edit `Dockerfile` and update the jsreport version to the current one. You can find the current version in the [github releases](https://github.com/jsreport/jsreport/releases) page.

Then zip the whole folder and you are ready to create Elastic Beanstalk Application.

##  Create Elastic Beanstalk Application
The new EB Application needs just name, docker platform and upload the prepared configuration zip.
![create eb application](/learn/static-resources/aws-ebt-create-app.png)

The application should be reachable in a minute then.

## Configure jsreport
You can [configure](/learn/configuraiton) jsreport using configuration file stored in the EFS or using environment variables passed through the EB configuration.

This is how you can enable jsreport [authentication](/learn/authentication).

![aws-ebt-config](/learn/static-resources/aws-ebt-config.png)

```sh
extensions_authentication_admin_username=admin
extensions_authentication_admin_password=xxx
extensions_authentication_cookieSession_secret=yoursecret
extensions_authentication_enabled=true
```