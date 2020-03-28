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
  schedule: '<CRON INTERVAL, default=0 */5 * * * * (every 5 minutes)>'
influx:
  host: <DATABASE HOST, default=localhost>
  database: <DATABASE NAME, default=weather>
darksky:
  key: <DARK SKY API>
  units: <DARK SKY UNITS, default=us>
  longitude: <LONGITUDE>
  latitude: <LATITUDE>
```

## Set Up InfluxDB

Run the InfluxDB initialization script to create a new database (named `weather`) and set up retention policies and continuous queries. To use a different database name, modify the commands and run each one interactively.

```sh
influx -import -path influxdb/init.txt
```

## Run Tracker

Simply run the `index.js` script to run the tracker. Data will be fetched periodically according to the `cron.interval` specified in the configuration file.

```sh
node index.js
```

Alternatively, use a process manage like `pm2` to run the tracker as a daemon.

```sh
pm2 start index.js --name darksky
```

## Data Queries

The tracker will save raw (`currently`) Dark Sky API data to the `darksky_raw` measurement, which will be retained for 30 days per the `autogen` retention policy (RP).

The continuous queries (CQs) will aggregate these raw measurements to hourly and daily timesteps (measurements `darksky_1h` and `darksky_1d`), which will be retained forever per the `infinite` RP.

To query the data from the InfluxDB CLI:

```sh
influx -precision rfc3339 -database 'weather'
```

```sql
select * from darksky_raw order by time desc limit 5; -- last 5 raw measurements
select * from darksky_1h order by time desc limit 24; -- last 24 hourly values
select * from darksky_1d order by time desc limit 7; -- last 7 daily values
```
