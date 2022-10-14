# logging

This is a monitoring stack that captures ALL services and container logs on a Docker Swarm into grafana.

Ensure that:
* The Swarm is using Traefik for ingress
* Edit `logging-stack.yml` to set `traefik` to whatever the public traefik network is if different.
* Edit `volumes:` with your settings for persistent cluster aware volumes.
* Replace `example.com` with a wildcard domain that points to your swarm.
* Ensure you are running at least Docker engine 20.10.12 on your swarm nodes.
* Merge the given settings with your `/etc/docker/daemon.json` on your swarm nodes.

`daemon.json`
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "labels-regex": "^com.docker.+"
  }
}
```

## Quickstart

1. Ensure your swarm nodes meet the required docker engine version, log-driver and log-opts.
2. Deploy the stack
3. Connect to grafana.example.com
4. Login as admin, admin
5. Go to Settings to attach a new Datasource.
6. Choose a "Loki" datasource
7. Enter "http://loki:3100" as the loki url.
8. Test and Save
9. Click on the Explore button on the left, ensure "Loki" is the default data source, and "code" is the default mode.
10. Clicking on "Log Browser" should show the labels captures from containers logging in your swarm.
