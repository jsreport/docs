# install node.js
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.4/install.sh | bash
# reopen terminal 
nvm install 20.10.0

mkdir jsreportapp
cd jsreportapp
npm i -g @jsreport/jsreport-cli
jsreport init
jsreport configure

# install chrome dependencies
sudo dnf install -y https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm

jsreport start