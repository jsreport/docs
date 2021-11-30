
<img src="/learn/static-resources/azure-functions.png" alt="azure functions" width="200" style="margin-left: auto;margin-right: auto; display: block"/>
<br/>

You can run jsreport also as serverless in [Azure Functions](https://azure.microsoft.com/en-us/services/functions/). This is a very convenient way how to run cheap and automatically scalable reports rendering without a need for paying for the actual servers.
This documentation helps you with creating the Azure Function running jsreport, deploying templates and remote invocation of the rendering.

## Clone starter kit repo
We prepared starter github repository [jsreport-azure-functions-starter-kit](https://github.com/jsreport/jsreport-azure-functions-starter-kit) for you.
First you need to clone it.
```
git clone https://github.com/jsreport/jsreport-azure-functions-starter-kit.git
```

## Prerequisites
This tutorial uses [Visual Studio Code](https://code.visualstudio.com/) for the function creation and deployment. You can use also Azure Portal or command-line tools, but this won't be covered here.

- [nodejs v16](https://nodejs.org/)
- [Azure Functions Core Tools v4](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local).
- [VS Code](https://code.visualstudio.com/)
- [Azure Functions Extensions for VS Code](https://docs.microsoft.com/en-us/azure/azure-functions/functions-develop-vs-code) 
- [Azure account](https://azure.microsoft.com/en-us/)

## Create and deploy function
Now you can deploy the azure function using the VC Code extension.

Select the Azure Functions extension -> Deploy to Function App -> Choose "Create new Function App in Azure Advanced" -> Fill a name -> Select Node.js 16 stack -> Linux OS -> Consumption plan ->  Resource group ->  A storage account -> Region 

![azure-function-deploy](/learn/static-resources/azure-function-deploy.gif)

This can take several minutes, afterward you should see the successful notification.

![azure-function-deploy-ok](/learn/static-resources/img/azure-function-deploy-ok.png)

If you click "View output" you should see these lines:

> 10:39:23 PM jsreportfn: HTTP Trigger Urls:
HttpTrigger1: https://jsreportfn.azurewebsites.net/api/HttpTrigger1

You can open the url and after few seconds the sample invoice pdf should be displayed.

## Prepare your templates
Now its time to prepare your templates in your local jsreport environment. To do it, run the following commands:

```
npm install
npm start
```

Afterward, you can open `http://localhost:5488` and you should see the running jsreport studio.

![studio](/learn/static-resources//studio.png)

This is the place where you can define your templates.  You can find the tutorials [here](https://jsreport.net/learn) if you are new to jsreport.


## Test and integrate

When your templates are ready, you need to deploy to the azure again. This time just select the previously created function app.

To test the remote invocation and integration, open file `test.js` and fill the URL of your Azure Function.
Then fill also your template name and conditionally also your real input data.
The last step is just to run the integration and open the file `out.pdf` to see the result.
```
node test.js
```


