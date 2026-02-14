# Use Ubuntu 24.04 as base for newer glibc and libraries
FROM ubuntu:24.04

LABEL maintainer="Your Name <you@example.com>" \
      description="Custom Redis 7 image for cluster setup on Ubuntu 24.04" \
      version="1.0"

# Prevent interactive prompts during package installs
ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies and add official Redis PPA to get Redis 7
RUN apt-get update && apt-get install -y --no-install-recommends \
      curl \
      gnupg2 \
      software-properties-common \
      ca-certificates \
      libssl3 \
      libstdc++6 \
    && curl -fsSL https://packages.redis.io/gpg | gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" \
       | tee /etc/apt/sources.list.d/redis.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends redis-server \
    && rm -rf /var/lib/apt/lists/*

# Set working directory for Redis config
WORKDIR /usr/local/etc/redis

# Expose Redis cluster ports
EXPOSE 6379 16379

# Default command to start Redis with config file
CMD ["redis-server", "/usr/local/etc/redis/redis.conf"]