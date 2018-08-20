
The installation script for CentOS 7

```bash
# install node.js
sudo yum install wget
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
# reopen terminal
nvm install 8.11.3

mkdir jsreportapp
cd jsreportapp
npm i -g jsreport-cli
jsreport init
jsreport configure

# install chrome dependencies
wget -qO- https://intoli.com/install-google-chrome.sh | bash

sudo yum install nano
nano jsreport.config.json

# change in the jsreport.config.json the following
# it makes chrome less secure but currently the only way on CentOS
"chrome": {
  "launchOptions": {
  "args": ["--no-sandbox"]
}

# save and start jsreport to see it running on port 5488
jsreport start

# the next steps are optional to start jsreport on boot
npm install pm2 -g
pm2 start server.js
pm2 startup
# run the output of previous command
```

