# Readme for RabbitMQ

This stack demonstrates a RabbitMQ instance that autoclusters with a minimum of effort.

- Unfortunately DNS based autoclustering does not work with Docker as the reverse DNS needs to resolve to a hostname.
- The cluster is configured with long names. This prevents collisions when RabbitMQ is exposed on global overlay networks like `traefik` where there will be potentially many `rabbitmq1` hosts.
- I do not know the degree to which `RABBITMQ_ERLANG_COOKIE` needs to be secret and/or unique and/or persistent. I've used `{{.Service.ID}}` which will change if the service/stack is fully removed and redeployed. This may prevent a backed up cluster being restored.

To deploy:

```
docker stack deploy -c rabbitmq-stack.yml rabbitmq
```

If deployed locally with a local Traefik, then [http://rabbitmq.local.gd/#/](http://rabbitmq.local.gd/#/)

## On Shared Storage

This assumes a single "shared" / cluster aware volume driver is being used. A single volume is provisioned for all instances as RabbitMQ automatically partitions data for each node into subfolders: e.g. Node rabbitmq2's data dir is `/var/lib/rabbitmq/mnesia/rabbit@rabbitmq2`.
