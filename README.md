# Quick Start PowerAPI using docker

The Power API official documentation <https://powerapi-ng.github.io/introduction.html>

## Disclaimer

> __This project aims to facilitate the testing of PowerAPI, it is clearly not to follow if you want to do something other than test.__

## Quick Start to deploy a sensor, a formula and a Grafana to visualize the data

```shell
# Gather the config file
git clone git@github.com:juulieen/quick-start-powerAPI.git
cd quick-start-powerAPI
# Sensor
docker run --rm -d --name=sensor --net=host --privileged --pid=host -v /sys:/sys -v /var/lib/docker/containers:/var/lib/docker/containers:ro -v /tmp/powerapi-sensor-reporting:/reporting -v $(pwd):/srv -v $(pwd)/config_sensor-Socket.json:/config_sensor.json powerapi/hwpc-sensor --config-file /config_sensor.json
# InfluxDB
docker run -d --name influx_rapl -p 8086:8086 influxdb:1.8
# Formula
docker run --rm -d --name=smartWatt --net=host -v $(pwd)/config_smartWatt-Socket-InfluxDB.json:/config_smartWatt.json powerapi/smartwatts-formula --config-file /config_smartWatt.json
# Grafana
docker run -d --net=host --name=grafana  grafana/grafana
```

> To setup your grafana with just add an InfluxDB Source targeting the datatbase `test` and create a new Dashboard (if you want a example of Queries please check the official documentation [here](https://powerapi-ng.github.io/grafana.html#:~:text=Click%20on%20the%20%22add%20Query%22%20button%20and%20write%20the%20following%20query%20on%20the%20Query%20field%20to%20request%20the%20power%20estimations%20from%20the%20InfluxDB%20measurement%3A%20SELECT%20power%20FROM%20%22power_consumption%22%20GROUP%20BY%20target))

### Saving into a MongoDB instance

```shell
# Deploy a mongoDB instance
docker run -d -p 27017:27017 --name mongo -v data:/data/db -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=example -d mongo:4.0

# Deploy the sensors
docker run --rm -d --name=sensor --net=host --privileged --pid=host -v /sys:/sys -v /var/lib/docker/containers:/var/lib/docker/containers:ro -v /tmp/powerapi-sensor-reporting:/reporting -v $(pwd):/srv -v $(pwd)/config_sensor-MongoDB.json:/config_sensor.json powerapi/hwpc-sensor --config-file /config_sensor.json

# Deploy the formula to gather information from MongoDB
docker run --rm -d --name=smartWatt --net=host -v $(pwd)/config_smartWatt-MongoDB-InfluxDB.json:/config_smartWatt.json powerapi/smartwatts-formula --config-file /config_smartWatt.json
```

### Using Rapl Formula

Do the same as before but instead of running the command to deploy the smarWatt formula run the one of the following command depending your need:

#### Gathering from a Socket

```shell
docker run --rm -d --name=rapl --net=host -v $(pwd)/config_rapl-Socket-InfluxDB.json:/config_rapl.json powerapi/rapl-formula --config-file /config_rapl.json
```

#### Gathering from MongoDB

```shell
docker run --rm -d --name=rapl --net=host -v $(pwd)/config_rapl-MongoDB-InfluxDB.json:/config_rapl.json powerapi/rapl-formula --config-file /config_rapl.json
```
