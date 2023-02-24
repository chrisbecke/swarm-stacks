
## Setup a 3 node etcd cluster

To run a 3 node cluster, choose 3 swarm nodes and label them

```bash
docker node update node1 --label-add "etcd=true"
docker node update node2 --label-add "etcd=true"
docker node update node2 --label-add "etcd=true"
```

In order to consistenly bind each etcd instance to its host and avoid hardcoding IP addresses, the deployment uses the host hostname as the cluster member hostname and identity. So edit `ETCD_INITIAL_CLUSTER` with the actual node names and then deploy.

```bash
export ETCD_INITIAL_CLUSTER=node1=http://node1:2380,node2=http://node2:2380,node3=http://node3:2380
docker stack deploy --compose-file etcd-stack.yml etcd
```

This deployment does not make a public ETCD instance - it creates a swarm scoped service. Swarm deployments wanting to use etcd need to attach to the public etcd network and use `etcd:2379` as the etcd url.

## Test with compose

```bash
docker compose run etcdctl
# etcdctl endpoint health
```
