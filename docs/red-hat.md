
The following jsreport installation steps were tested on:    

Red Hat Enterprise Linux 9.3    
Red Hat Enterprise Linux 8.7

```bash
# install node.js
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.4/install.sh | bash
# reopen terminal 
# install nodejs >=18.15.0
nvm install 20.10.0

mkdir jsreportapp
cd jsreportapp
npm i -g @jsreport/jsreport-cli
jsreport init
jsreport configure

# install google chrome with required dependencies
# full article https://www.tecmint.com/install-google-chrome-in-linux
echo "[google-chrome]
name=google-chrome
baseurl=http://dl.google.com/linux/chrome/rpm/stable/x86_64/$basearch
enabled=1
gpgcheck=1
gpgkey=https://dl-ssl.google.com/linux/linux_signing_key.pub" | sudo tee -a /etc/yum.repos.d/google-chrome.repo

sudo yum install -y google-chrome-stable

# eventually open jsreport port on firewall
sudo firewall-cmd --permanent --add-port=5488/tcp

jsreport start
```


