

<img src="/blog/lambda.png" alt="lambda" width="100" style="margin-left: auto;margin-right: auto; display: block"/>
<br/>
You can run jsreport serverless in [AWS Lambda](https://aws.amazon.com/lambda/). This is the very convenient way to run cheap and automatically scalable reports rendering without a need for paying for the actual servers.
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

On Linux, make sure you have the required chrome shared libraries mentioned in the [install docs](/on-prem).

Then open `http://localhost:5488` and prepare jsreport templates the same way you would do when running normal jsreport.

### Prepare layer
The best way is to create lambda layer with node_modules folder and then have just templates and configs inside the lambda function package itself. There is `createLambdaLayer.js` script that helps you with that.
Call it using 
```
node createLambdaLayer.js
```
Then log in to AWS console and upload the prepared `layer.zip` to a s3 bucket.
Then create AWS lambda layer referencing the uploaded zip with nodejs 20.x runtime.

![lambda layer](/learn/static-resources/aws-lambda-create-layer.png)

### Create lambda function

Then create lambda function with node 20.x runtime.

![lambda function](/learn/static-resources/aws-lambda-create-function.png)


### Upload lambda package

The next step is preparing the lambda package. This can be done using the following script.

```js
node createLambdaPackage.js
```

Then upload the prepared `lambda.zip` through the AWS Console.

![lambda upload](/learn/static-resources/aws-lambda-upload-package.png)

### Add layer to the function

Then add the layer you previously created.

![lambda add layer](/learn/static-resources/aws-lambda-add-layer.png)

![lambda add layer2](/learn/static-resources/aws-lambda-add-layer2.png)

### Increase memory and timeout

The default 3s timeout and 128mb memory isn't sufficient for bigger reports.
We recommend increasing timeout to 60s and memory to 1024mb.

![lambda change configuration](/learn/static-resources/aws-lambda-change-configuration.png)

### Test the function
The AWS Console includes convenient way to test the function directly on the web.
The function expects standard jsreport rendering request as the parameter and outputs base64 encoded report output.

![lambda test](/learn/static-resources/aws-lambda-test.png)

You can also use script `test.js` from the starter kit to test function remotely using AWS node.js SDK.

1. Create AWS IAM user with AWSLambdaRolerole
2. Set environment variables AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY
3. Edit test.js and set your AWS region and the lambda name
4. Run `node test.js`
5. Check the `report.pdf` was properly rendered
