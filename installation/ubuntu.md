
# Ubuntu

This script is tested on Ubuntu 16.04

```sh
# install node.js
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
# you may need to reopen terminal
nvm install 8.11.3

mkdir jsreportapp
cd jsreportapp
npm i -g jsreport-cli
jsreport init
jsreport configure

# chrome dependencies
sudo apt-get install -y libgconf-2-4
sudo wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
sudo sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'
sudo apt-get update
sudo apt-get install -y google-chrome-unstable fonts-ipafont-gothic fonts-wqy-zenhei fonts-thai-tlwg fonts-kacst --no-install-recommends

# start jsreport to see it running on port 5488
jsreport start

# the next steps are optional to start jsreport on boot
npm install pm2 -g
pm2 start server.js
pm2 startup
# run the output of previous command

# optionally if you want to use older phantomjs for pdf rendering
sudo apt-get install -y --no-install-recommends gnupg git curl wget ca-certificates
sudo apt-get install -y --no-install-recommends xfonts-base xfonts-75dpi
npm i jsreport-phantom-pdf --save --save-exact
```



