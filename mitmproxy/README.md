# MITM-Proxy

Deploys a man-in-the-middle proxy. 
This is just configured as an ad-hoc development assist.

## Quickstart

Start the mitm stack.

```bash
export DOMAIN=yourdomain.com
export DOCKER_CONTEXT=your-swarm-context
make deploy browser
```

Test the Stack

```bash
docker run --rm -it --network traefik nicolaka/netshoot
curl 
```


