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
docker build -t docker-tcp-proxy
```

### Running the Container

The following example forward TCP requests on port `6379` to an Elasticache instance on `my-elasticache.domain.com:6379`

```
docker run -it -p 6379:6379 --rm -e "BACKEND_HOST=my-elasticache.domain.com" -e "BACKEND_PORT=6379 docker-tcp-proxy
```
