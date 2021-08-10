

<img src="/blog/lambda.png" alt="lambda" width="100" style="margin-left: auto;margin-right: auto; display: block"/>
<br/>
You can run jsreport serverlessly in [AWS Lambda](https://aws.amazon.com/lambda/). This is the very convenient way to run cheap and automatically scalable reports rendering without a need for paying for the actual servers.
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
Then log in to aws console and upload the prepared `layer.zip` to a s3 bucket.
Then create aws lambda layer referencing the uploaded zip with nodejs 12.x runtime.

![lambda layer](/img/lambda-layer.png)

### Prepare lambda function
The next step is preparing the lambda package. This can be done using the following script.
```js
node createLambdaPackage.js
```
Then create lambda function with the prepared `lambda.zip` and merging the previously created layer.

![lambda](/img/lambda.png)

We recommend also increasing the memory and timeout to reflect your needs.

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
