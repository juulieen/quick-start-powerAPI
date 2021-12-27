# Tests it locally

```shell
# Deploy MongoDB
docker run -p 27017:27017 --name mongo -v data:/data/db -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=example -d mongo:4.0
# Deploy sensors
docker run --rm --name=sensor --net=host --privileged --pid=host -v /sys:/sys -v /var/lib/docker/containers:/var/lib/docker/containers:ro -v /tmp/powerapi-sensor-reporting:/reporting -v $(pwd):/srv -v $(pwd)/config_sensor.json:/config_sensor.json powerapi/hwpc-sensor --config-file /config_sensor.json
# Deploy InfluxDb
docker run -d --name influx_rapl -p 8086:8086 influxdb:1.8

# Deploy Formula
docker run --rm --name=rapl --net=host -v $(pwd)/config_rapl.json:/config_rapl.json powerapi/rapl-formula --config-file /config_rapl.json
# Or
docker run --rm --name=smartWatt --net=host -v $(pwd)/config_smartWatt.json:/config_smartWatt.json powerapi/smartwatts-formula --config-file /config_smartWatt.json

# Deploy Grafana
docker run -d --name=grafana -p 3000:3000 grafana/grafana
```
