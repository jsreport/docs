

How to...

**General questions**
- [Update existing jsreport server to the latest version](#update-server)    
- [Deploy to production](#deploy-to-production)
- [Migrate templates from the test to the production server](#migrate-templates)    
- [Run jsreport on different port](#port-config)
- [Increase performance](#performance)    
- [Windows path too long 256 characters limitation](#windows-path-too-long)    

**Licensing and payments**
- [How to apply license key](#how-to-apply-license-key)    
- [Update payment details](#update-payment-details)
- [Cancel subscription](#cancel-subscription)     
- [5 templates limitation in free plan](#5-templates-limitation-in-free-plan)
- [Subscription renew](#subscription-renew)

### <a name="update-server"></a>Update existing jsreport server to the latest version

```bash
# install helpful tool to check the latest versions
npm install -g npm-check-updates
# check the latest versions of jsreport and additional extensions.
# remember to check release notes when you are planning to update,
# specially if you are going to do a major version update
ncu
# update package.json
ncu -u
# install the latest versions
npm install
```

### <a name="deploy-to-production"></a>Deploy to production

The simplest way to deploy local instance to the production is to compress the whole application folder, upload it to the server and decompress. You can even simply attach the `node` executable to the compressed package if you don't have [node.js](https://nodejs.org/en/) installed on the production server.  Such package should be able to run without other dependencies installed. This approach should work as long as you have the same OS architecture on the local and the production.

The other options are to run install through `npm` on the production server and then just copy the templates.

The most complex and powerful approach is to set up an image through `docker` for example.

### <a name="migrate-templates"></a>Migrate templates from the test to the production server

migrating/moving templates should be easy using the [import-export](https://jsreport.net/learn/import-export) feature, you can export from test server and then import the generated backup (zip file) into the production server. other option can be to copy files, since jsreport stores by default templates in the `[application path]/data` folder. To migrate templates you can just grab content of this folder and copy it to the production server.

### <a name="port-config"></a>Run jsreport on different port

You need to open `jsreport.config.json` file and edit `httpPort` property to desired value. another option is to use cli args or env vars to set the port while starting jsreport. ex: `jsreport start --httpPort 5489` or `httpPort=5489 jsreport start`. For details please explore [configuration documentation](/learn/configuration), which contains different ways to do it.

### <a name="performance"></a>Increase performance

jsreport uses by default dedicated processes for rendering pdf or scripts.  This solution works better in some cloud and corporate environments with proxies. However for other cases, for example when using phantomjs it is better to reuse phantomjs and nodejs workers over multiple requests.

Open `jsreport.config.json` and update following:

```js
"phantom": {     
	"strategy": "phantom-server"
},
"templatingEngines": {       
	"strategy": "http-server"
}
```

See [configuration documentation](/learn/configuration) for details.

### <a name="windows-path-too-long"></a>Windows path too long 256 characters limitation

Windows has limitation for maximum file path set to 256 characters and this doesn't play well with nested paths used by older npm versions. To make sure the windows deployment plays well please update npm to the latest release, which should be greater than >=3.x.x.

```sh
npm install npm
```

### <a name="how-to-apply-license-key"></a>How to apply license key
You can choose one from the options below:

1. The license key can be saved into the application working directory or data folder as `license-key.txt`
2. Filled in the configuration file in the property `license-key`, or `licenseKey`
3. Passed in the command line parameter as `--license-key=xxx` or `--licenseKey=xxx`
4. Set in the environment variable as `license-key` or `licenseKey`

when using the [official jsreport docker image](https://github.com/jsreport/jsreport/blob/master/docker/README.md) you can follow [these instructions](https://github.com/jsreport/jsreport/blob/master/docker/README.md#apply-license-key) to apply your license key.

Make sure to remove all the white spaces around the key.

### <a name="update-payment-details"></a>Update payment details

jsreport enterprise or jsreportonline subscribers can update the payment details in the following way.

1. Open your [gumroad account](https://gumroad.com/library)
2. Login through the email you filled during the initial subscription. It is the same email your receipts are being sent to.
3. Choose `My account/Settings` in the menu
4. Select `Use different card` and update the account details

### <a name="cancel-subscription"></a>Cancel subscription
1. Open your [gumroad account](https://gumroad.com/library)
2. Select the subscription you want to cancel
3. Click red "Cancel subscription" button

You can find more detail description in [gumroad help](https://help.gumroad.com/11167-buyer-faq/how-do-i-cancel-a-subscription).

### <a name="5-templates-limitation-in-free-plan"></a>5 templates limitation in free plan
The free plan can be used in commercial projects with only one limitation. You can store maximum 5 templates in the jsreport store. The template entities are typically created and persisted to store using jsreport studio UI. The templates sent to the jsreport render API with full specification doesn't count to this. In other words the requests which includes in the body `template.content` are not using stored template. To be sure you can always open jsreport studio and verify how many templates are visible there.

The studio also warns in the modal dialog that the maximum number of templates was reached and one month trial in which you can use infinite templates started.

### <a name="subscription-renew"></a>Subscription renew

The subscriptions gets automatically renewed every year. You should receive an email from gumroad informing about the renewal payment every year. The license key stays the same. This means there is no change needed in your application. Keep in mind you should [update your payment details](#update-payment-details) if you change the bank card. 
