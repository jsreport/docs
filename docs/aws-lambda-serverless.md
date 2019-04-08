<img src="/blog/lambda.png" alt="lambda" width="100" style="margin-left: auto;margin-right: auto; display: block"/>
<br/>
You can run jsreport also as serverless in [AWS Lambda](https://aws.amazon.com/lambda/). This is the very convenient way how to run cheap and automatically scalable reports rendering without a need for paying for the actual servers.
This documentation helps you with creating the AWS Lambda running jsreport, deploying templates and remote invocation of the rendering.

## Get started

### Clone starter kit repo
We prepared starter github repository [jsreport-aws-lambda-starter-kit](https://github.com/jsreport/jsreport-aws-lambda-starter-kit) for you.
The first you need to clone it.
```
git clone https://github.com/jsreport/jsreport-aws-lambda-starter-kit.git
```

### Prepare templates

Install dependencies and start jsreport...
```
npm i
npm start
```
Then open `http://localhost:5488` and prepare jsreport templates the same way you would do when running normal jsreport.

### Upload headless chrome
The headless chrome that works for you locally won't work in AWS Lambda. It needs to be recompiled for the specific distribution that runs there. Fortunately the recompiled chrome can be just downloaded.

Download and decompress it from    
[https://github.com/adieuadieu/serverless-chrome/releases](https://github.com/adieuadieu/serverless-chrome/releases)

Then upload it to an s3 bucket you have AWS.

### Prepare package
The AWS Lambda expects a compressed package with node application. The first you need to delete the `node_modules/puppeteer/.local-chromium` folder because that will get downloaded from AWS s3 at the function startup. Then you can compress the application. Or just run already prepared script    
```js
node createLambdaPackage.js
```

### Create AWS Lambda
Now it is time to create aws lambda. For example, using AWS console. Just make sure to select node.js 8.10 or higher runtime. Then upload the package prepared in the previous step.

![lambda-upload](/blog/lambda-upload.png)


The next you need to increase the lambda timeout and also increase the memory settings. The rendering gets faster with more memory you assign. The recommendation is to use 60s timeout and 1024MB.

![lambda-settings](/blog/lambda-settings.png)

And assign environment variables specifying the s3 bucket and key of the previously uploaded chrome.

![lambda-env](/blog/lambda-env.png)

And add to the pre-created role the `AWSLambdaS3ExecutionRole` policy.

![lambda-role](/blog/lambda-role.png)
![lambda-role-policies](/blog/lambda-role-policies.png)

### Test the function
It is up to you how you want to integrate the lambda function. 
It expects the standard jsreport rendering request as the parameter and outputs base64 encoded report output.
You can invoke it using many approaches AWS supports like AWS CLI or a specific language SDK.
However, we prepared script which uses AWS node.js SDK to invoke the lambda function and you can use it for testing.

1. Create AWS IAM user with AWSLambdaRolerole
2. Set environment variables AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY
3. Edit test.js and set your AWS region and the lambda name
4. Run `node test.js`
5. Check the `report.pdf` was properly rendered

## Known issues

The re-compiled chrome binary doesn't render some specific fonts like Chinese one. The issue is tracked [here](https://github.com/adieuadieu/serverless-chrome/issues/49) with some mentioned workarounds however this doesn't work us so far. We will update here as soon as we find out how this issue can be solved.