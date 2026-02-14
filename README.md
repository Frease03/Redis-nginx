# Redis Cluster via Nginx Proxy

## Overview

This setup places the Redis cluster behind an Nginx TCP reverse proxy to enhance security and restrict direct access. All Redis traffic must go through the Nginx proxy.

## Architecture

Redis Nodes: redis-node-1, redis-node-2, redis-node-3

Nginx Proxy: redis-nginx-proxy (TCP reverse proxy for Redis, exposing port 6379)

Monitoring:

Redis metrics: redis-exporter on port 9121

Nginx metrics: nginx-prometheus-exporter on port 9113

Prometheus: 9090

Grafana: 3000

Alertmanager: 9093

## Redis Access

Redis nodes are not directly accessible. All clients must connect via the Nginx proxy:

**From a container or host that can access the proxy:**

redis-cli -h `nginx-proxy-host` -p 6379 -a <REDIS_PASSWORD> ping

**Expected output:**

PONG

## Nginx Proxy Configuration

Listens on port 6379 for Redis TCP traffic.

Provides /stub_status on port 8080 for monitoring.

Prevents direct access to individual Redis nodes.

Example Nginx TCP Stream Configuration
stream {
    upstream redis_backend {
        server redis-node-1:6379;
        server redis-node-2:6379;
        server redis-node-3:6379;
    }

    server {
        listen 6379;
        proxy_pass redis_backend;
    }
}

## Monitoring

Redis metrics: collected via redis-exporter and visible in Grafana.

Nginx metrics: collected via nginx-prometheus-exporter.

Grafana dashboards reflect Redis cluster metrics and Nginx proxy health.

## Dependent Services

All services must update their Redis connection to:

`nginx-proxy-host:6379`

instead of directly connecting to redis-node-1, redis-node-2, or redis-node-3.

## Validation

Confirm Redis is not reachable directly from the host:

redis-cli -h redis-node-1 -p 6379 -a <REDIS_PASSWORD>

### Connection should fail

Confirm Redis is reachable through Nginx proxy:

redis-cli -h redis-nginx-proxy -p 6379 -a <REDIS_PASSWORD> ping

**Output:** PONG

Check Grafana dashboards for Redis and Nginx metrics.
