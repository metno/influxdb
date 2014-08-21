tutum-docker-influxdb
=====================
InfluxDB image


Usage
-----

To create the image `tutum/influxdb`, execute the following command on tutum-docker-influxdb folder:

    docker build -t tutum/influxdb .

You can now push new image to the registry:
    
    docker push tutum/influxdb


Running your InfluxDB image
--------------------------

Start your image binding the external ports `8083` and `8086` in all interfaces to your container. Ports `8090` and `8099` are only used for clustering and should not be exposed to the internet.

    docker run -d -p 8083:8083 -p 8086:8086 --expose 8090 --expose 8099 tutum/influxdb


Configuring your InfluxDB
-------------------------
Open your browse to access `localhost:8083` to configure InfluxDB. Fill the port which maps to `8086`. The default credential is `root:root`. Please change it as soon as possible.

Alternatively, you can use RESTful API to talk to InfluxDB on port `8086`

Initially Create Database
-------------------------
Use `-e PRE_CREATE_DB="db1;db2;db3" to create database named "db1", "db2", and "db3" on the first time the container starts automatically. Each database name is separated by `;`.


Clustering
----------
Use :
*`-e SEEDS="\"host1:8090\", \"host2:8090\""` to pass seeds nodes to your container.
*`-e REPLI_FACTOR=x` where x is the replicator factor of shards through the cluster (defaults to 1)
*`-e FORCE_HOSTNAME="auto"` to force the hostname in the config file to be set to the container IPv4 eth0 address (usefull to test clustering on a single docker host)
*`-e FORCE_HOSTNAME="<whatever>" ` to force the hostname in the config file to be set to <whatever>

Example on a single docker host :
*launch first container :
```
docker run -p 8083:8083 -p 8086:8086 --expose 8090 --expose 8099 \
  -e FORCE_HOSTNAME="auto" -e REPLI_FACTOR=2 \
  -d --name masterinflux tutum/influxdb
```
*then launch one or more "slaves":
```
docker run --link masterinflux:master -p 8083 -p 8086 --expose 8090 --expose 8099 \
  -e SEEDS=\"master:8090\" -e FORCE_HOSTNAME="auto" \
  -d  tutum/influxdb
```
