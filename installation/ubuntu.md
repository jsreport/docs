#Ubuntu

This page contain some jsreport related troubleshooting on ubuntu.

####Installing nodejs
jsreport needs latest nodejs. Official ubuntu apt-get distribution contains some weird stuff so rather use following repository.
```
sudo add-apt-repository ppa:chris-lea/node.js
sudo apt-get update
sudo apt-get install python-software-properties python g++ make nodejs
```

####Fixing phantomjs
Official ubuntu distribution is missing some fonts jsreport needs to be able to render pdf reports. Use following to include them.
```
sudo apt-get install build-essential chrpath git-core libssl-dev libfontconfig1-dev
```

[more info](http://phantomjs.org/download.html)

####Start jsreport as daemon

Create conf file `/etc/init/jsreport.conf`

```
start on startup
chdir [working directory]
exec sudo npm start
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
git clone https://github.com/jsreport/playground.git
```





