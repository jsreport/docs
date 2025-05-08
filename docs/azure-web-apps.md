

This tutorial describes how to quickly set up jsreport server on [
Azure Web App](https://azure.microsoft.com/en-us/services/app-service/web/) through docker and start creating pdf or excel reports in no time.

Note that jsreport runs also on VMs, cloud services or container instances without limitations.

## Create the Web App

The first you need to open azure portal and hit "Create a Resource" -> "Create Web App".
Then fill the mandatory fields and select publish to the "Docker Container".

![azure web app create](/learn/static-resources/azure-webapp-create.png)

Continue to the next page and fill deployment from the Single Container and image downloaded from the Docker Hub. The current official jsreport image is `jsreport/jsreport:3.0.1`, but always get the latest version number in the [dockerhub](https://hub.docker.com/r/jsreport/jsreport).

![azure web app create](/learn/static-resources/azure-webapp-create-docker.png)

Now you can skip the next steps and directly hit "Review + create" and submit the deployment. When the deployment is ready, go to the resource web app.

![azure web app open](/learn/static-resources/azure-webapp-open.png)

Creating application takes usually several minutes so be patient. You should be able to open web application in the browser afterwards.

![studio](https://jsreport.net/screenshots/studio.png?v=2)

Currently (jsreport `4.6.0`, `4.7.0`, `4.8.0`, `4.9.0`), you need to add the following environment variable in order to make chrome generation working
```
chrome_launchOptions_args=--no-sandbox, --disable-dev-shm-usage, --disable-gpu
```

## Configure jsreport

The easiest way to [configure](/learn/configuration) jsreport is to use the environment variables which can be set right in the `Application Settings` in the azure portal.

Let's enable [jsreport authentication](https://jsreport.net/learn/authentication) for example by setting the following variables

```
extensions_authentication_admin_username=admin
extensions_authentication_admin_password=xxx
extensions_authentication_cookieSession_secret=yoursecret
```

![azure-webapp-config](/learn/static-resources/azure-webapp-config.png)

After hitting save, the changes should be propagated in couple of seconds. When reloading the app in the browser, you should see a login screen.

This was just one of the many configuration options you may need. Please find more configuration options in the [documentation](https://jsreport.net/learn/configuration).

## Persist templates
You would soon realize that the stored templates are lost if you restart the app service. This is because the jsreport stores the data by default right in the container. You may rather want to store templates in an external disk or full database. You can find all the availible options including SQL server, Mongo or Azure Storage blobs in the [template store documentation](/learn/template-stores), but now we just map azure file share to the web app.

The first you need to create Azure storage account and a File share in it.
![azure web app file share](/learn/static-resources/azure-webapp-file-share.png)

Note selecting the "Premium performance" layer improves twice the speed for big imports and templates editing. However, the templates are cached in memory so this doesn't have any effect on the rendering time and the standard storage account should be just fine in the most of the cases.

Then you need to map the file share to the web app.
![azure-webapp-map-file-share](/learn/static-resources/azure-webapp-map-file-share.png)

## Logs
The azure web app by default doesn't store or show logs from the container where jsreport runs. This is opt-in which you can enable in the App Service logs.

![azure-webapp-logs](/learn/static-resources/azure-webapp-logs.png)

Afterwards you can inspect jsreport logs in the Log stream.

![azure-webapp-logstream](/learn/static-resources/azure-webapp-logstream.png)
