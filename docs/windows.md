
Download and install the LTS version of [node.js](https://nodejs.org).
Afterwards open command line terminal and type:

```bash
mkdir jsreportapp
cd jsreportapp
npm i -g @jsreport/jsreport-cli
jsreport init
jsreport configure
jsreport start
```

Now you should be able to open a browser and reach jsreport on http://localhost:5488

You can install jsreport as windows service to automatically start it on the system boot. This can be done using the following handy command if you reopen terminal with administrator rights.

```bash
jsreport win-install
```

To uninstall the service use

```bash
jsreport win-uninstall
```

If you run the service with a system user and get chrome generation timeout, you will need to add the following config to the root.

```js
 "chrome": { "launchOptions": { "args": ["--no-sandbox"] } }
```
