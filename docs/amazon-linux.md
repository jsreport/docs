The installation script for Amazon Linux

```bash
# install node.js
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
sudo yum install -y libXScrnSaver 

# start jsreport to see it running on port 5488

jsreport start

# the next steps are optional to start jsreport on boot
npm install pm2 -g
pm2 start server.js
pm2 startup
# run the output of previous command

# optionally if you want to use older phantomjs for pdf rendering
npm i jsreport-phantom-pdf --save --save-exact
```

