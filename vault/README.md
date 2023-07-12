# Vault HA on Swarm

This demonstrates a semi production ready setup of vault, using 
- integrated (RAFT) storage
- a transit vault to show auto unsealing of the vault. (The transit vault does not auto unseal.)

## More Information

There are TWO vaults instantiated in this project.
The first is a single node vault that is definately not in any way production ready. It must be manually configured, and unseals using a docker healthcheck. (The healtcheck hack is less of a problem than the secrets being backed into the deployment.)
We enable the transit engine on this vault and create an unseal_key for the production vault to use.

The production vault can then be initialized, and its nodes will subsequently auto-unseal when restarted or migrated for as long as the transit vault is reachable.

Lastly, vault agents are created. These would normally be installed in your application container, or as sidecars if using an orchestrator like K8s. The agents simplify the work of connecting to the active vault task: A niave connection to http://vault:8200 will get routed through dockers round robin routing, and about 2 thirds of the time the requesting applicaiton will get a 304 redirect to http://vault{x}:8200 as vault clusters do not internally forward requests to the active node. Vault agents on the other hand do track and forward to the cluster leader.

## Setup the Transit vault

```bash
# Deploy the initial transit vault stack
docker stack deploy -c transit.yml demo
# Find out where it is, and access its terminal
docker service ps demo_transit --no-trunc
docker -c lab717 exec -it demo_transit.1.vq873uapj7z3oevvsctdd3ppj /bin/ash
# Initialize the vault and set it up to act as a Transit
vault operator init -format=json -key-shares 1 -key-threshold 1
vault operator unseal afyqlTG6xZi14HewLVeX/ZRCLkuoAX6WYvGEyVGL99Q=
vault status
vault login hvs.5mmuzyT1aHsNbeYwyrWslr9p
vault secrets enable transit
vault write -f transit/keys/unseal_key
exit
```

## Setup the Vault vault

```bash
# Redeploy the larger stack, telling transit vault how to auto unseal, (a healthcheck hack)
# And giving vault the token necessary to access the transit vault
UNSEAL_KEY=afyqlTG6xZi14HewLVeX/ZRCLkuoAX6WYvGEyVGL99Q= \
VAULT_TOKEN=hvs.5mmuzyT1aHsNbeYwyrWslr9p \
docker stack deploy -c stack.yml demo
# Check that the Transit auto unseal is working
docker service ps demo_transit --no-trunc
docker -c lab717 exec -it demo_transit.1.vq873uapj7z3oevvsctdd3ppj /bin/ash
vault status
exit
# Find an instance of the HA vault and get a terminal. Verify its sealed and unconfigured
docker service ps demo_vault --no-trunc
docker -c lab717 exec -it demo_vault.1.vq873uapj7z3oevvsctdd3ppj /bin/ash
vault status
# 
vault operator init -format=json -recovery-shares 1 -recovery-threshold 1
{
  "unseal_keys_b64": [],
  "unseal_keys_hex": [],
  "unseal_shares": 1,
  "unseal_threshold": 1,
  "recovery_keys_b64": [
    "4IPwwQr/RE+wkietCM40ytmjBa1XczQ8SKmMIDS8rJg="
  ],
  "recovery_keys_hex": [
    "e083f0c10aff444fb09227ad08ce34cad9a305ad5773343c48a98c2034bcac98"
  ],
  "recovery_keys_shares": 1,
  "recovery_keys_threshold": 1,
  "root_token": "hvs.m87UnzjGojNHjVHfCIJ5y2lK"
}
# verify it unsealed automatically
vault status
# Check the raft members are all there
vault operator members
# login and perform any setup needed...
vault login hvs.m87UnzjGojNHjVHfCIJ5y2lK
vault secrets enable -path=kv kv-v2
```

## Using the Agent

```
docker service ps demo_agent --no-trunc --format '{{.Node}} {{.Name}}.{{.ID}}' --filter desired-state=running
docker -c lab722 exec -it demo_agent.1.erbrxz98q7db5tn0bibevwzbu /bin/sh
vault login hvs.m87UnzjGojNHjVHfCIJ5y2lK
# Now issue any commands. Vault agent will direct the command to the active vault automatically.
```
