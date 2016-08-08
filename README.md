[![](https://images.microbadger.com/badges/image/demandbase/docker-tcp-proxy.svg)](http://microbadger.com/images/demandbase/docker-tcp-proxy "Get your own image badge on microbadger.com")
[![](https://images.microbadger.com/badges/version/demandbase/docker-tcp-proxy.svg)](http://microbadger.com/images/demandbase/docker-tcp-proxy "Get your own version badge on microbadger.com")

# docker-tcp-proxy
Simple TCP Proxy in a Docker Container using [HAproxy](http://www.haproxy.org/)

## Why?
Sometimes you may need to forward a TCP connection and don't want to setup IPTables or a complicated application proxy. This container was originally created to route Redis requests to an AWS Elasticache backend from a network that did not have access to the VPC elasticache was configured in.

AWS documentation [recommends](https://docs.aws.amazon.com/AmazonElastiCache/latest/UserGuide/Access.Outside.html) setting up a NAT instance with iptable rules. This container makes it possible to setup a simple HAproxy configuration that proxies requests instead.

## Usage
### Environment Variables

When running the container two Environment Variables are required

- `BACKEND_HOST`
  - IP or hostname to proxy connections to
- `BACKEND_PORT`
  - The port number to proxy connections to

### Building the Container

```
docker build -t docker-tcp-proxy .
```

### Running the Container

The following example forward TCP requests on port `6379` to an Elasticache instance on `my-elasticache.domain.com:6379`

```
docker run -it -p 6379:6379 --rm -e "BACKEND_HOST=my-elasticache.domain.com" -e "BACKEND_PORT=6379 docker-tcp-proxy
```

### Deploying to Rancher
This repository contains a compose file to deploy a Redis proxy to a [Rancher](https://www.rancher.com) cluster. By default it listens on port 23201.

#### Environment File
Create the environment `.env.export` file with the `BACKEND_HOST` and `BACKEND_PORT` values,

```
BACKEND_HOST=redis.your-domain.com
BACKEND_PORT=6379
```

#### Deploying
Deploy the container to Rancher with [rancher-compose](http://docs.rancher.com/rancher/v1.0/zh/rancher-compose/)

```
rancher-compose --file redis-tcp-proxy.yml \
                --env-file .env.export \
                --project-name redis-proxy \
                up \
                --force-upgrade \
                --confirm-upgrade \
                --pull \
                -d
```
