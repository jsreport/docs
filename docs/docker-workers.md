


> Sandbox user code evaluation in the dedicated [docker](https://www.docker.com/) container

## Basics

jsreport by default always runs the user code in the extra process to minimize the chance that a wrong user code or hacking request kills or breaks the server. This is sufficient in the most of the cases and user code shouldn't be able to for example reach the server file system or stuck in an endless loop. However, the default sandboxing doesn't limit the resource consumption of the user code in any way and your instance could get overwhelmed by bad requests. This can be a problem in the few cases when the instance is opened to the public internet and you need to make sure the requests don't use all the CPU. This is for example the case of our public services [playground](https://playground.jsreport.net) or [jsreportonline](https://jsreportonline.net).

The `docker-workers` jsreport extension sandboxes the user code into the dedicated [docker](https://www.docker.com/) container and allows you to fully limit the resources consumption of individual requests. The container also uses the read-only file system to be fully isolated from the jsreport instance.

## Installation

Install [docker](https://www.docker.com/) and regular jsreport instance directly on the host. Then install [jsreport-docker-workers]() extension and start jsreport instance

```
npm i jsreport-docker-workers
jsreport start
```

The first start can take some minutes because the extension needs to download and install the docker image used for sandboxing. You can monitor the jsreport output until you see the message "reporter initialized".

Now you can open studio and start rendering templates. The user code evaluation will always  run in the dedicated container with limited resources. By default, the container is limited to the 0.5 CPU and 420 MB of memory and jsreport creates the pool of 4 containers.

## Basic configuration

The extension can be configured using the standard jsreport methods. The most common change is the number of containers running in the pool and memory/CPU consumption limits.

```json
{
  "extensions": {
    "docker-workers": {
      "container": {
        "memory": "320m",
        "cpus": "0.3"
      },
      "numberOfWorkers": 6
    }
  }
}  
```

See the [configuration schema](#configuration-schema) in the last chapter for all available options.

## Reusing containers

The containers are recycled after every render request by default. This means every request runs in the completely isolated environment. This is the best for the isolation but it can also produce performance problems. It is usually better to reuse containers to reach the good balance between performance and isolation. 

The extension supports containers reusing based on the specific condition. You can, for example, reuse containers based on the authenticated user. The extension then keeps the pool of containers and rotate them using LRU (Last recently used) principle. The extension guarantees that the particular user in this case always runs on the dedicated container and the containers are always fresh and recycled before re-associated from one user to another.

The containers reusing can be configured using the property  `discriminatorPath`. This property expects the path on the input request structure which value, in the end, distinguish to which container the request should be delegated. Examples:

```js
// each user will run in the dedicated container
"discriminatorPath": "context.user._id"
// each template will run in the dedicated container
"discriminatorPath": "template._id"
// the client can specify the discriminator in the options
"discriminatorPath": "options.myvalue"
```

## Custom worker image

The default docker [worker image](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-worker) used for sandboxing includes just support for the recipes and engines included in the standard jsreport installation. If you need to use custom extensions you need to create your own image. See the [playground worker image](https://github.com/jsreport/playground-worker) to get an idea how to do it. When you have the image available use config value `container.image` to apply it.

## Running jsreport instance in docker
The official jsreport docker image doesn't include this extension yet and you need to create your image the first.

The next is running it and mount docker socket to it. This is because this extension uses docker commands to run the worker images and prepare the environment. In the other words it needs to access to the docker. You can mount the docker socket using, for example, the `-v` switch
```
docker run ... -v /var/run/docker.sock:/var/run/docker.sock
```

## Multiple servers

The extension is designed with horizontal scaling in mind. Running multiple servers will multiple the number of containers in the pool because the containers are shared between all servers in the cluster. In the other words. The server always resends the request to another server in cluster if it finds out that the client is already registered there. This maximizes the number of active containers associated with the clients and minimizes the number of containers restarts and recycles.

Running multiple servers typically doesn't require extra configuration. However, we recommend using a full blown database for jsreport store and not the default file system. The extension was primarily tested on the [mongodb store ](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-mongodb-store) which is used in our services.

Note the servers in the cluster needs to be able to reach each other. The extension by defaults resolves the local ip address from the network settings. If this isn't working you can override each individual server ip using environment variable `ip`.

## Multiple environments

You can run multiple environments like staging and production baked with the same database/store. You just need to avoid that servers in one environment cluster are not delegating work to the servers from another environment. This can be done by setting environment variable `stack`.

## Block communication between containers

To increase the security we recommend to block the communication between individual containers. This can be done on linux using `iptables` command.

```
iptables -I INPUT -s 172.30.0.0/24 -d ${localIp} -j REJECT
```

## Licensing
This extension requires that jsreport instance has valid enterprise license in production. It is also strongly recommended to purchase the support together with the license because the extension covers very advanced scenario.

## Configuration schema

```json
{
  "ip": { "type":  "string" },
  "stack": { "type":  "string", "default":  "default" },
  "extension": {
    "docker-workers": {		  
          "discriminatorPath": { "type": "string", "default":    "context.reportCounter" },
          "pingServersInterval": { "type": "number", "default": 5000 },
          "pingHealthyInterval": { "type": "number", "default": 20000 },
          "container": {
            "type": "object",
            "properties": {
	          "image": { "type": "string", "default": "jsreport/jsreport-worker" },              
              "namePrefix": { "type": "string", "default": "jsreport_worker" },
			  "exposedPort": { "type": "number", "default": 2000 },
			  "basePublishPort": { "type": "number", "default": 2001 },
			  "containerCustomEnv": {
	            "anyOf": [{
	              "type": "string",
	              "$jsreport-constantOrArray": []
                }, {
	              "type": "array",
	              "items": { "type": "string" }
                }]
	          },
	          "startTimeout": { "type": "number", "default": 10000 },
	          "restartPolicy": { "type": "boolean", "default": true },
	          "restartTimeout": { "type": "number", "default": 5000 },
	          "delegateTimeout": { "type": "number", "default": 50000 },
	          "debuggingSession": { "type": "boolean", "default": false },
	          "memorySwap": { "type": "string", "default": "512m" },
	          "memory": { "type": "string", "default": "420m" },
	          "cpus": { "type": "string", "default": "0.5" },
	          "logDriver": { "type": "string", "default": "json-file" },
	          "logOpt": { "type": "object" },
	          "tempVolumeSourcePrefix": { "type": "string" },
	          "tempVolumeTarget": { "type": "string", "default": "/tmp" },
            }
		  },
          "subnet": { "type": "string", "default": "172.30.0.0/24" },
          "network": { "type": "string", "default": "nw_jsreport_workers_docker_manager" },
          "busyQueueWaitingTimeout": { "type": "number", "default": 10000 },
          "numberOfWorkers": { "type": "number", "minimum": 1, "default": 4 }      
    }
  }  
}
```

