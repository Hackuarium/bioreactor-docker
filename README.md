## mqtt

The goal of this project is to create a fast setup of a docker mqtt broker in javascript.

In this project we are using 4 diffent docker images:
* node-red
* mosca
* mongo
* influxdb

## setup a new server

This documentation is for a CentOS 7.x

### Install docker

curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose

### Install iptables

Add the following rule in iptables:
`-A INPUT -p tcp -m tcp -m multiport --dports http,https,1883 -j ACCEPT`

### Using an apache proxy

Using an apache proxy allows to play with the hostname.

vi /etc/httpd/conf.d/mqtt.conf
```
<VirtualHost *:80>
    ServerName 	mqtt.beemos.org

	ProxyRequests off
	ProxyPreserveHost on
	ProxyPass               "/comms"        "ws://localhost:1880/comms"
	ProxyPassReverse        "/comms"        "ws://localhost:1880/comms"
	ProxyPass               "/"             "http://localhost:1880/"
	ProxyPassReverse        "/"             "http://localhost:1880/"
</VirtualHost>  
```

### Starting the server

`docker-compose up -d`

## Testing



## Some other tools


Node-red will save all the data in the folder `node-red/data`

Using GRAFANA for output: https://www.hackster.io/naresh-krish/visualizing-lora-node-data-with-node-red-and-grafana-8960d3

http://localhost:1880/

The UI

http://localhost:1880/ui/

mosquitto_pub --retain -t "random" -m $RANDOM

Add continously data:
`while true; do mosquitto_pub --retain -t "random" -m $RANDOM; sleep 2; done`

## Mosquitto

Test MQTT server using mosquitto

mosquitto_pub --retain -m "Test" -t "abcd/efgh"

mosquitto_sub -h "localhost" -t "abcd/efgh"


## Connect to influxdb for debug

`docker-compose exec influxdb bash`
`influx -username user -password user`

`show databases`
`use data`
`select * from random`


## Problems with Fedora 31

https://github.com/docker/for-linux/issues/219

$ sudo dnf install -y grubby
$ sudo grubby --update-kernel=ALL --args="systemd.unified_cgroup_hierarchy=0"
$ sudo reboot
