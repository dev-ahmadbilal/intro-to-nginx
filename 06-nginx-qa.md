# ❓ Common Questions and Troubleshooting

This chapter addresses common questions and clarifications related to NGINX, Docker, and their configurations. By exploring these topics, you'll gain a deeper understanding of how NGINX operates in various scenarios and how to troubleshoot or optimize your setups effectively.

## 🌐 HTTP/1.1 and HTTP/2 Compatibility

### Can NGINX Handle Both HTTP/1.1 and HTTP/2 Simultaneously?
Yes, NGINX can handle both protocols on the same port. The protocol is negotiated during the TLS handshake using ALPN (Application-Layer Protocol Negotiation).

### Example Configuration
```nginx
server {
    listen 443 ssl http2;
    server_name example.com;

    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;

    location / {
        root /var/www/html;
        index index.html;
    }
}
```

## 🔍 Docker Container Accessibility

### Why Is My NGINX Container Not Accessible?
Common causes and solutions:

1. **Port Mapping Issues**
   ```bash
   # Correct port mapping
   docker run -d -p 8080:80 --name my-nginx nginx
   ```

2. **Firewall Rules**
   - Check host machine's firewall
   - Verify port accessibility
   - Test with `telnet` or `curl`

3. **Network Configuration**
   - Inspect container networks
   - Verify Docker network settings
   - Check container logs

### Troubleshooting Steps
```bash
# Check container logs
docker logs my-nginx

# Test connectivity
docker exec -it my-nginx curl http://localhost

# Inspect container
docker inspect my-nginx
```

## 🔄 WebSocket Connection Handling

### How Does NGINX Handle WebSocket Connections?
NGINX supports WebSocket connections through proper configuration:

```nginx
server {
    listen 80;

    location /ws/ {
        proxy_pass http://backend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
    }
}
```

### Key Points
- HTTP/1.1 is required
- Upgrade header must be set
- Connection header must be "Upgrade"

## ⏱️ Timeout Configuration Issues

### What Happens If Timeouts Are Not Configured Properly?

#### Common Issues
1. **Backend Server Unreachable**
   - Check `proxy_connect_timeout`
   - Verify network connectivity
   - Adjust timeout values

2. **Slow Backend Responses**
   - Review `proxy_read_timeout`
   - Monitor backend performance
   - Set appropriate values

3. **Client-Side Issues**
   - Check `client_header_timeout`
   - Verify `client_body_timeout`
   - Adjust based on client behavior

### Best Practices
- Set realistic timeout values
- Monitor logs for bottlenecks
- Adjust based on application needs

## 🔧 Docker Network Debugging

### How to Debug Network Issues in Docker Containers?

#### Useful Commands
1. **Inspect Container Networks**
   ```bash
   docker inspect <container_id>
   ```

2. **Test Connectivity**
   ```bash
   docker exec -it <container_id> ping google.com
   ```

3. **Trace Routes**
   ```bash
   docker exec -it <container_id> traceroute google.com
   ```

### Custom Dockerfile for Debugging
```Dockerfile
FROM nginx:latest

RUN apt-get update && apt-get install -y \
    iputils-ping \
    traceroute \
    dnsutils
```

## 🌐 Hostname vs IP Address Usage

### Why Use Hostnames Instead of IP Addresses?

#### Benefits
- **Scalability**: Easy container addition/removal
- **Readability**: Easier to remember and manage
- **Flexibility**: Automatic hostname resolution

#### Example Setup
```bash
# Create network
docker network create my-network

# Run containers
docker run --net=my-network --name app1 -d my-app
docker run --net=my-network --name nginx-proxy -d nginx
```

## ⚖️ Reverse Proxy and Load Balancing

### Can NGINX Act as Both a Reverse Proxy and Load Balancer?

#### Example Configuration
```nginx
http {
    upstream backend {
        server 192.168.1.10;
        server 192.168.1.11;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://backend;
        }
    }
}
```

#### Key Features
- **Reverse Proxy**: Forwards client requests
- **Load Balancing**: Distributes traffic
- **Multiple Algorithms**: Round-robin, IP hash, least connections

## 🚨 Common Error Messages and Solutions

### 1. "502 Bad Gateway"
- Check backend server availability
- Verify proxy_pass configuration
- Check backend server logs

### 2. "504 Gateway Timeout"
- Review timeout settings
- Check backend server performance
- Verify network connectivity

### 3. "413 Request Entity Too Large"
- Adjust `client_max_body_size`
- Check upload limits
- Verify file size restrictions

## ⚡ Performance Optimization Tips

### 1. Worker Processes
```nginx
worker_processes auto;
worker_connections 1024;
```

### 2. Caching
```nginx
proxy_cache_path /path/to/cache levels=1:2 keys_zone=my_cache:10m;
```

### 3. Compression
```nginx
gzip on;
gzip_types text/plain text/css application/json;
```

## Conclusion 🎯

Understanding NGINX and Docker configurations is crucial for building robust, scalable systems. By addressing common questions and providing practical examples, this chapter aims to clarify complex topics and empower you to optimize your infrastructure effectively.

---

⬅️ [Previous: Scaling WebSockets with NGINX](./05-websockets-nginx.md) | ➡️ [Next: Advanced Techniques and Best Practices](./07-advanced-techniques.md) 