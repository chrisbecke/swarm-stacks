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
