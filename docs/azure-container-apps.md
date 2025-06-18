The [Azure Container Apps](https://azure.microsoft.com/en-us/products/container-apps) lets you host
 containerized jsreport in a serverless fashion. This quick tutorial will help you to deploy jsreport in a minute.

## Get started

Use Azure Portal to create a new Container App and keep all the defaults on the first page.

![create-1](/learn/static-resources/azure-container-app-1.png)

On the second page select "Image source" as "Docker hub" and type [jsreport official image and tag](/learn/docker).
For example, `jsreport/jsreport:4.9.0-full`.
![create-2](/learn/static-resources/azure-container-app-2.png)

On the third page, enable Ingress and select "Accepting traffic from anywhere" and port 5488.
![create-3](/learn/static-resources/azure-container-app-3.png)

This is all, and you should have jsreport studio running if you open the new container app with "Application Url" field.

## Configuration

The jsreport is now up and running. The easiest approach to configure it without building your own image is to use environment variables.

This can be found in the Application/Containers menu.

![create-4](/learn/static-resources/azure-container-app-4.png)

## Templates persistence

By default, the templates are stored directly in the container files. This means after you restart the container, you will lose all the templates you've created.

One option is to use an external SQL or Mongo based database for the [template store](/learn/template-stores). If you have one available, you can use environment variables to configure the jsreport driver and connect to it.

The other option is to use [Azure Files](https://azure.microsoft.com/en-us/products/storage/files) and map it to the container volume. For this, you need to create Azure Storage account and File share. Then, open Azure Container App Environment and add it.

![create-5](/learn/static-resources/azure-container-app-5.png)

Then, back in the Azure Container App add Volume.

![create-6](/learn/static-resources/azure-container-app-6.png)
![create-7](/learn/static-resources/azure-container-app-7.png)

The last point is to add the Volume Mount to the Container
![create-8](/learn/static-resources/azure-container-app-8.png)

That's all. Now you should have the Azure Container App running and storing data in the external Azure Files Share. Your templates will now be retained after the scale or containers restart.

