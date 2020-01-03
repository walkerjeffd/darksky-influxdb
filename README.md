Dark Sky Weather Tracker
========================

Simple weather data tracker using the Dark Sky API as a data source, and InfluxDB for data storage and aggregation.

## Configuration

Create a new default configuration file using `config/template.yaml` as a template:

```sh
cp config/template.yaml config/default.yaml
# edit config/default.yaml
```

```yaml
cron:
  schedule: '<CRON INTERVAL, default=*/5 * * * * (every 5 minutes)>'
influx:
  host: <DATABASE HOST>
  database: <DATABASE NAME>
  measurement: <MEASUREMENT NAME>
darksky:
  key: <DARK SKY API>
  units: <DARK SKY UNITS, default=us>
  longitude: <LONGITUDE>
  latitude: <LATITUDE>
```

## Set Up InfluxDB

Run the InfluxDB initialization script to create a new database (named `weather`) and set up retention policies and continuous queries. To use a different database name, run the commands interactively.

```sh
influx -import -path influxdb/init.txt
```

