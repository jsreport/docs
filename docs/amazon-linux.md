The installation script for Amazon Linux 2

```bash
# install node.js
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.38.0/install.sh | bash
# you may need to reopen terminal
nvm install 16.12.0

mkdir jsreportapp
cd jsreportapp
npm i -g @jsreport/jsreport-cli
jsreport init
jsreport configure

# install chrome dependencies
wget -qO- https://intoli.com/install-google-chrome.sh | bash

# start jsreport to see it running on port 5488
jsreport start

# the next steps are optional to start jsreport on boot
npm install pm2 -g
pm2 start server.js
pm2 startup
# run the output of previous command
```
