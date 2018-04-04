# CentOS

This page contain some jsreport related troubleshooting on CentOS.

## Installing nodejs

``` bash
sudo yum install epel-release
sudo yum install nodejs
sudo yum install npm
```

## Fixing pdf fonts

```bash
sudo yum install bzip2
sudo yum install libXext  libXrender  fontconfig  libfontconfig.so.1
sudo yum install urw-fonts
```

## Install jsreport and start

```bash
sudo npm install -g jsreport-cli
sudo jsreport init
sudo node server.js

```

## wkhtmltopdf

Assuming npm 3 is intalled.
```bash
sudo npm install jsreport-wkhtmltopdf --production
sudo yum install xorg-x11-fonts-75dpi
sudo yum install libjpeg
sudo yum install xorg-x11-fonts-Type1
sudo yum install libpng libpng12 wget
sudo wget http://download.gna.org/wkhtmltopdf/0.12/0.12.2.1/wkhtmltox-0.12.2.1_linux-centos7-amd64.rpm
sudo rpm -Uvh wkhtmltox-0.12.2.1_linux-centos7-amd64.rpm
sudo echo "module.exports = '/usr/local/bin/wkhtmltopdf';" > node_modules/wkhtmltopdf-installer/lib/location.js
sudo node server.js
```


## fop

```bash
sudo yum install java
sudo cd /opt
sudo wget http://apache.miloslavbrada.cz/xmlgraphics/fop/binaries/fop-1.1-bin.tar.gz
sudo tar -xvzf fop-1.1-bin.tar.gz
sudo ln -s -t /usr/bin /opt/fop-1.1/fop
```
