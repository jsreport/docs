

<img src="/learn/static-resources/azure-functions.png" alt="azure functions" width="200" style="margin-left: auto;margin-right: auto; display: block"/>
<br/>

You can run jsreport also as serverless in [Azure Functions](https://azure.microsoft.com/en-us/services/functions/). This is a very convenient way how to run cheap and automatically scalable reports rendering without a need for paying for the actual servers.
This documentation helps you with creating the Azure Function running jsreport, deploying templates and remote invocation of the rendering.

## Prerequisites
This tutorial uses [Visual Studio Code](https://code.visualstudio.com/) for the function creation and deployment. You can use also Azure Portal or command-line tools, but this won't be covered here.

- [LTS nodejs](https://nodejs.org/)
- [Azure Functions Core Tools](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local)
- [Azure account](https://azure.microsoft.com/en-us/)
- [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)

## Create azure function
You can use CLI or portal based on your preference. However you need to select

- consumtion plan
- node.js runtime stack
- Linux operating system

## Clone starter kit repo
We prepared a starter github repository [jsreport-azure-functions-starter-kit](https://github.com/jsreport/jsreport-azure-functions-starter-kit) for you.

```
git clone https://github.com/jsreport/jsreport-azure-functions-starter-kit.git
```

## Deploy the function

If you have installed the [Azure Functions Core Tools](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local) and  [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
prerequisites, you should be able to deploy the function using a single command.
```
func azure functionapp publish <yourfunctionname> --build remote
```

If you aren't logged in with azure CLI, you may need to do the first
```
az login
```

The deployment output will include the url which you can use to test if the function is running properly.
```
Functions in jsrtest:
    MyHttpTrigger - [httpTrigger]
        Invoke url: https://jsrtest.azurewebsites.net/api/myhttptrigger

```

## Prepare your templates
Now it's time to prepare your templates in your local jsreport environment. To do it, run the following commands:

```
npm install
npm start
```

Afterward, you can open `http://localhost:5488` and you should see the running jsreport studio.

![studio](/learn/static-resources/studio.png)

This is the place where you can define your templates.  You can find the tutorials [here](https://jsreport.net/learn) if you are new to jsreport.


## Test and integrate

When your templates are ready, you need to deploy to the azure again using the same command.

To test the remote invocation and integration, open file `test.js` and fill the URL of your Azure Function.
Then fill also your template name and conditionally also your real input data.
The last step is to run the integration and open the file `out.pdf` to see the result.
```
node test.js
```


