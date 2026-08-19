# Traefik + Loki + Grafana : Web Analytics setup

You can find an article about his repo here: https://oglimmer.medium.com/web-analytics-with-grafana-loki-ad64e05cf9f3

## Geo location lookup

Go to https://dev.maxmind.com/geoip/geolite2-free-geolocation-data and download `GeoLite2-City.mmdb`. Place it into this directory.

## Start

Tested under Debian 12.

```bash
docker compose up -d
```

### macOS

In docker-compose.yml uncomment `network_mode: host` and add ports: 80:80, 8080:8080

## Links

```
http://<IP>:80 to generate a website request

http://<IP>:8080 access Traefik Admin UI

http://<IP>:3000 access Grafana UI
```

## Setup Grafana dashboard

Import `Webanalytics.json` as a dashboard.

## A note on cardinality

Every distinct combination of label values creates a stream in Loki, and every
stream costs a directory plus at least one chunk file on the filesystem store.
Putting `client_host`, `user_agent` or `request_path` into labels therefore
creates roughly one stream per request - which runs a server out of inodes long
before it runs out of disk space, and makes Loki reject new streams once the
default limit of 5000 active streams per tenant is reached.

Those fields are attached as structured metadata instead. They stay queryable
(`{host="localhost"} | client_host="1.2.3.4"`) and the dashboard is unchanged,
but they are stored with the log line rather than indexed. Only bounded fields
- `OS`, `Device`, `Browser`, `geoip_country_name` - are labels.

Structured metadata requires Loki with schema v13 and the tsdb store, which is
what the `grafana/loki:3.0.0` image ships by default.

Retention is disabled in Loki by default, so the compactor flags in
`docker-compose.yml` are what keeps the chunk directory from growing forever.
