
## Setup a 3 node etcd cluster

```bash
make cluster nodes=node1,node2,node3
export ETCD_INITIAL_CLUSTER=node1=http://node1:2380,node2=http://node2:2380,node3=http://node3:2380
docker stack deploy --compose-file etcd-stack.yml etcd
```
