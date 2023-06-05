# Vault HA on Swarm

This demonstrates a semi production ready setup of vault, using 
- integrated (RAFT) storage
- a transit vault to show auto unsealing of the vault. (The transit vault does not auto unseal.)

## Setup the Transit vault

```
docker service ps demo_transit --no-trunc
docker -c lab717 exec -it demo_transit.1.vq873uapj7z3oevvsctdd3ppj /bin/sh
vault operator init -format=json -key-shares 1 -key-threshold 1
vault operator unseal afyqlTG6xZi14HewLVeX/ZRCLkuoAX6WYvGEyVGL99Q=
vault login hvs.5mmuzyT1aHsNbeYwyrWslr9p
vault secrets enable transit
vault write -f transit/keys/unseal_key
```

## Setup the Vault vault

```
docker service ps demo_vault --no-trunc
docker -c lab717 exec -it demo_vault.1.vq873uapj7z3oevvsctdd3ppj /bin/sh
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
vault login hvs.m87UnzjGojNHjVHfCIJ5y2lK
vault secrets enable -path=kv kv-v2
```