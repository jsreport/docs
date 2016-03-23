#Ubuntu

This page contain some jsreport related troubleshooting on ubuntu.

####Installing nodejs
Official ubuntu `apt-get` distribution contains outdated node.js. I prefer to use [nvm](https://github.com/creationix/nvm) to manage my node.js installation.
```sh
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.31.0/install.sh | bash
```

If you are using terminal like `putty`, you cannot pipe the installation to the bash, so you need to save it to file and run it. Afterwards you need to reopen the terminal.

```sh
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.31.0/install.sh > install.sh
chmod +x install.sh
./install.sh
```

Now install node.js

```sh
nvm install 5
```

To be able to `sudo run` the npm or node you need to also run the following command

```sh
n=$(which node);n=${n%/bin/node}; chmod -R 755 $n/bin/*; sudo cp -r $n/{bin,lib,share} /usr/local
```

####Fixing phantomjs
Official ubuntu distribution is missing some fonts jsreport needs to be able to render pdf reports. Use following to include them.
```
sudo apt-get install build-essential chrpath git-core libssl-dev libfontconfig1-dev
```

[more info](http://phantomjs.org/download.html)

####npm start
Use npm to start the server on `https://localhost`
```sh
sudo npm start --production
```

Change the port in config file or directly through command line and run also without sudo
```sh
npm start --production -- --httpsPort=0 --httpPort=3000
```


####Start jsreport as daemon

Create conf file `/etc/init/jsreport.conf`

```
start on startup
chdir [working directory]
exec sudo npm start --production
```

And add execution rights to it
```
chmod -x jsreport.conf
```

####Installing mongodb
jsreport uses embeded file system based storage and you don't need mongodb to run it. This is only optional.
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | sudo tee /etc/apt/sources.list.d/mongodb.list
sudo apt-get update
sudo apt-get install mongodb-org
sudo apt-get install mongodb-org=2.6.1 mongodb-org-server=2.6.1 mongodb-org-shell=2.6.1 mongodb-org-mongos=2.6.1 mongodb-org-tools=2.6.1
sudo /etc/init.d/mongod start
```
[more info](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/)

####Installing git
```
sudo apt-get install git
```

####Clone github repository
```
git clone https://github.com/jsreport/jsreport.git
```





