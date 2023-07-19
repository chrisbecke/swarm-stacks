# Readme for RabbitMQ

This stack demonstrates a RabbitMQ instance that autoclusters with a minimum of effort.

- Unfortunately DNS based autoclustering does not work with Docker as the reverse DNS needs to resolve to a hostname.
- As a followon to the above issue, the rabbitmq Image does not like fully qualified hostnames (e.g. rabbitmq1.default) and will not cluster with these. so this demo currently uses only short hostnames. This means that two clusters will conflict for as long as both are exposed on Traefik (or any other global network).
- I do not know the degree to which `RABBITMQ_ERLANG_COOKIE` needs to be secret and/or unique and/or persistent. I've used `{{.Service.ID}}` which will change if the service/stack is fully removed and redeployed. This may prevent a backed up cluster being restored.

To deploy:

```
docker stack deploy -c rabbitmq-stack.yml rabbitmq
```

If deployed locally with a local Traefik, then [http://rabbitmq.local.gd/#/](http://rabbitmq.local.gd/#/)

## On Shared Storage

This assumes a single "shared" / cluster aware volume driver is being used. A single volume is provisioned for all instances as RabbitMQ automatically partitions data for each node into subfolders: e.g. Node rabbitmq2's data dir is `/var/lib/rabbitmq/mnesia/rabbit@rabbitmq2`.
