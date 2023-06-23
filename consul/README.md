# Hashicorp Consul

Demonstrates a 3 node consul cluster as a single service called "consul" with three replicas that are individually accessible via docker dns as "consul1", "consul2" and "consul3".

Requires: A cluster aware docker volume driver with an alias called "shared".

A consuming app would set its consul url to "http://consul:8500" and be attached to the "consul" network to use this instance.