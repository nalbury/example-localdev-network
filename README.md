# Example Localdev Network

This is a simple docker compose based ingress/DNS stack for local dev.

## Usage
First ensure you have docker and docker compose installed.

To start the network in the background, simply clone this repo and run `docker compose up -d`.

Once the network has been started you can point your workstation's DNS at `127.0.0.1` which will resolve any `*.dev.local` domain to the local [traefik proxy](https://traefik.io) running as part of this stack.

Other applications can be exposed via this proxy by running their containers on the `localdev` docker network with the following label:
- "traefik.http.routers.examplefastapi.rule=Host(`<APPNAME>.dev.local`)"

Where <APPNAME> is the name of the service you're exposing. This will route any requests to your workstation on port 80 with the hostname <APPNAME>.dev.local to the labeled container, which when combined with the local DNS server, allow host based routing for multiple HTTP containers on the same workstation.

### Example Service with Docker Compose
The following `docker-compose.yml` file can be added to an existing application to run it alongside this localdev networking stack. Replace `example-app` with the name of the application.

```yaml
version: '3.8'
name: pizzacorp
services:
  example-app:
    networks:
      - localdev
    build: .
    labels:
      - "traefik.http.routers.examplefastapi.rule=Host(`example-app.dev.local`)"
    ports:
      - 80
networks:
  localdev:
    external: true
```

