# Traefik + Loki + Grafana : Web Analytics setup



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
