# bitcoin-cash
Docker images for Bitcoin Cash nodes.

Provided for the community by nChain Ltd.

## Tags & Versions
* unlimited, unlimited-1.3, unlimited-1.3.0, unlimited-1.3.0.0 - [Bitcoin Unlimited Cash v1.3.0.0](https://github.com/nchain-research/bitcoin-cash-docker/blob/unlimited-1.3.0.0/docker/Dockerfile)
* abc, abc-0.17, abc-0.17.0 - [Bitcoin ABC v0.17.0](https://github.com/nchain-research/bitcoin-cash-docker/blob/abc-0.17.0/docker/Dockerfile)

Older versions:
* unlimited-1.2, unlimited-1.2.0.1 - REMOVED, incompatible with May 2018 protocol upgrade
* unlimited-1.2.0.0 - REMOVED, incompatible with May 2018 protocol upgrade
* abc-0.16, abc-0.16.2 - REMOVED, incompatible with May 2018 protocol upgrade
* abc-0.16.1 - REMOVED, incompatible with May 2018 protocol upgrade

## Quick Start

* simple Unlimited node: `docker run -it --name bchunlim nchain/bitcoin-cash:unlimited`
* simple ABC node: `docker run -it --name bchabc nchain/bitcoin-cash:abc`

To use `bitcoin-cli` you can use something like
````
docker exec --user bitcoin $(docker ps --filter name=bchunlim -q) bitcoin-cli -conf=/home/bitcoin/bitcoin.conf getinfo
````

## Data 
Bitcoin Cash data is stored in the container at `/data`. To preserve the data, mount
a volume at this location using `-v host_dir:/data`.

## Configuration
You can include your own `bitcoin.conf` in the `/data` volume.

Some notes on bitcoin.conf parameters:
* `printtoconsole=1` - causes output to be sent to the standard output
* `rpcallowip=::/0` - enables RPC port on docker internal network interface
* do not set `daemon=1` otherwise the container will exit immediately
* do not include RPC usernames/passwords, see below

## RPC Authentication
RPC username and password can be set using either Docker secrets or environment variables. 
Secrets take precendence.

Environment variables are `BCH_RPC_USER` and `BCH_RPC_PASSWORD`.

Docker secrets are `bchrpcuser` and `bchrpcpassword`. The secrets must be just the value
(e.g. `echo "rpcusername" | docker secret create bchrpcuser`). Secrets are useful if other
services need to use RPC.

## Examples
Set RPC username and password through environment variables, expose RPC port:
````
docker run -it --rm -p 8332:8332 -e BCH_RPC_USER='user' -e BCH_RPC_PASSWORD='abcde' nchain/bitcoin-cash:abc
````
You can then use bitcoin-cli from the docker host using something like `bitcoin-cli -rpcuser=user -rpcpassword=abcde getinfo`

### Docker Swarm Service Example
Create a docker swarm service:
````
docker service create --name bchunlim \
	--replicas 1 \
	--restart-max-attempts 5 \
	--publish published=8333,target=8333,mode=host \
	--mount type=bind,source=/var/local/bchunlim,destination=/data \
	--secret bchrpcuser \
	--secret bchrpcpassword \
	--detach=true \
	nchain/bitcoin-cash:unlimited
````

### Docker Stack Example
Docker stack using a compose file. Deploy using `docker stack deploy -c docker-compose.yml`:
````
version: "3.5"

services:
  bchunlim:
    image: nchain/bitcoin-cash:abc
    volumes:
      - bch-data:/data
    networks:
      - bch
    ports:
      - target: 8333
        published: 8333
        protocol: tcp
        mode: host
    configs:
      - source: bchconf
        target: /data/bitcoin.conf
    secrets:
      - bchrpcuser
      - bchrpcpassword
    hostname: bchnode
    deploy:
      replicas: 1
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 300s

networks:
  bch:
    driver: overlay

volumes:
  bch-data:

secrets:
  bchrpcuser:
    external: true
  bchrpcpassword:
    external: true

configs:
  bchconf:
    file: ./bitcoin.conf
````

## Versions, Implementations, & Tags
We will maintain versions of the major Bitcoin Cash node implementations that are compatible with the
current Bitcoin Cash network. Older versions which are no longer compatible will be removed. Versions
may also be removed if they have a significant issue.

The Dockerfile and related files for each implementation and version are stored in their own branch.

We currently have Bitcoin ABC and Bitcoin Unlimited images and are working on others.

## Copyright
Copyright nChain Ltd 2018

Distributed under the GNU GPL v3.0, see accompanying file LICENSE for details

Based on work by Adrian Macneil from https://github.com/amacneil/docker-bitcoin

