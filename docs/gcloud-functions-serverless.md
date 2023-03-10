

<img src="/learn/static-resources/gcloud-functions.png" alt="gcloud functions" width="200" style="margin-left: auto;margin-right: auto; display: block"/>
<br/>

You can run jsreport also as serverless in [Google Cloud Functions](https://cloud.google.com/functions). This is a very convenient way how to run cheap and automatically scalable reports rendering without a need for paying for the actual servers.
This documentation helps you with creating the Google Cloud Function running jsreport, deploying templates and remote invocation of the rendering.

## Clone starter kit repo
We prepared starter github repository [jsreport-gcloud-functions-starter-kit](https://github.com/jsreport/jsreport-gcloud-functions-starter-kit) for you.
First you need to clone it.
```
git clone https://github.com/jsreport/jsreport-gcloud-functions-starter-kit.git
```

## Prerequisites

- [nodejs v16](https://nodejs.org/)
- [Google Cloud account and project](https://cloud.google.com/).
- [gcloud cli](https://cloud.google.com/sdk/docs/install)

## Deploy
To deploy the function to the cloud you need to first init the `gcloud` cli.
```
gcloud init
```

Then run npm deploy script inside the `jsreport-gcloud-functions-starter-kit` folder.
```
npm run deploy
``` 
The cli may ask you to enable some APIs and the deployment may take several minutes.

## Testing
The function can be tested right from the google cloud console.
You just need to send a valid [jsreport API request](https://jsreport.net/learn/api).

![gcloud testing](/learn/static-resources/gcloud-functions-testing.png)

## Develope your templates
Now its time to prepare your templates in your local jsreport environment. To do it, run the following commands:

```
npm install
node server.js
```

Afterward, you can open `http://localhost:5488` and you should see the running jsreport studio.

![studio](/learn/static-resources/studio.png)

This is the place where you can define your templates.  You can find the tutorials [here](https://jsreport.net/learn) if you are new to jsreport.

The templates can be again deployed with the `deploy` script
```
npm run deploy
```
The render request can reference deployed templates simply using names.
```js
{
   "template": {
     "name": "myTemplate"
   },
   "data": { "someProp": "foo" }
}
```
