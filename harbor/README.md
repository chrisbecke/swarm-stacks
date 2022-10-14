# Harbor

Deploys goharbor's Harbor to Docker Swarm using a stack file derived from [bitnami/harbor-portal](https://github.com/bitnami/containers/tree/main/bitnami/harbor-portal)

To make this deployment work on your docker swarm you will need at least to:

* Edit the Volumes directives to point to your own cluster aware volume driver. Its fine to use `local` in the case of a single node swarm.
* Replace `example.com` with a wildcard DNS that points at your own swarm.
* Have Traefik installed as a global ingress and handling https for that domain.
* Replace the `traefik` network with the name of your traefik public network.

The compose file this is derived from is used for testing the bitnami/harbor-portal container and is not fully functional, but it serves to get swarm hosted Harbor working.
