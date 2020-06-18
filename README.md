# bioreactor-docker

This project is the dockerization of [Hackuarium/nodered-bioreactor-gui](https://github.com/Hackuarium/nodered-bioreactor-gui).

### Install docker

```bash
curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-\$(uname -m) -o /usr/local/bin/docker-compose
```

## Starting the project

Once you cloned the project, you can simply go to the folder and run:

```bash
docker-compose up
```

## Stopping the project

You might want to do this if you pull some changes from the cloud, then start again.

```bash
docker-compose down
```

## Docker images

We had to install three different docker images in the project:

- Node-red
- Mosquitto
- InfluxDB

The images are installed (or built) thanks to the configuration in the `docker-compose.yml` file. The images were found on [Docker Hub](https://hub.docker.com/).

### Node-Red

Source image: `nodered/node-red`

The node-red image has to be built because we add some packages to it (all the dependencies of the `nodered-bioreactor-gui). All the packages are added in the file`node-red/Dockerfile`.

### Mosquitto

Source image: `eclipse-mosquitto`

### InfluxDB

Source image: `influxdb`

The node-red graphical interface of the bioreactor requires some influxDB databases to be already existing. We create these databases, as well as the continuous queries in the file `influxdb/init/startup.iql`.

The data of the database is in `influxdb/db/`.

Connect to influxdb for debug:

```bash
docker-compose exec influxdb bash
```

## Deployment machine

## Docker folder

Docker is in `/usr/local/docker`. This is where the project is cloned.

## Proxy

**Apache** was used.

There is only one port accessible from the internet: HTTPS - port 443. Many services, which work on various ports, run behind this one entry point. This is why you have to configure a proxy, which will redirect queries to the correct services. In our case, we want to redirect [https://bioreactor.hackuarium.org]() to [http://localhost:1880]().

The proxy config file is: `etc/httpd/conf.d`.

The code underneath is what should be used as the configuration of port 443:

```
<VirtualHost *:443>
	Use SSLConf bioreactor.hackuarium.org

    ServerName bioreactor.hackuarium.org

	ProxyRequests Off
    ProxyPreserveHost On

	RewriteEngine On
	RewriteCond %{HTTP:Upgrade} websocket [NC]
	RewriteRule /(.*) ws://localhost:1880/$1 [P,L]

    ProxyPass       	/	http://localhost:1880/
    ProxyPassReverse 	/ 	http://localhost:1880/
</VirtualHost>
```
