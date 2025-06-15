# 🚀 Advanced Techniques and Best Practices

This chapter provides advanced NGINX techniques, optimizations, and best practices. Whether you're looking to refine your NGINX configuration or explore new use cases, this material will help you take your skills to the next level.

## ⚙️ Optimizing Worker Processes and Connections

### Worker Processes
```nginx
worker_processes auto;
```
- Pins each worker to a CPU core
- Minimizes context switching
- Maximizes performance

### Worker Connections
```nginx
events {
    worker_connections 10240;
}
```
- Total connections = `worker_processes` × `worker_connections`
- Example: 4 cores × 10,240 = 40,960 connections

## ⚡ Leveraging HTTP/2 for Performance

### Enabling HTTP/2
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

### Benefits
- **Multiplexing**: Multiple requests over single connection
- **Header Compression**: Reduced header size
- **Server Push**: Proactive resource delivery

## 🛡️ Implementing Rate Limiting

### Example Configuration
```nginx
http {
    limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;

    server {
        location /api/ {
            limit_req zone=mylimit burst=20 nodelay;
            proxy_pass http://backend;
        }
    }
}
```

### Key Components
- **`limit_req_zone`**: Defines shared memory zone
- **`rate=10r/s`**: Requests per second per IP
- **`burst=20`**: Additional queued requests

## 🔄 Using Maps for Dynamic Configurations

### Example: Geographic Redirection
```nginx
http {
    map $geoip_country_code $redirect_url {
        default "https://example.com";
        US      "https://us.example.com";
        EU      "https://eu.example.com";
    }

    server {
        location / {
            return 301 $redirect_url;
        }
    }
}
```

### Use Cases
- Geographic routing
- A/B testing
- Feature flags
- Custom error pages

## 🔍 Debugging NGINX with Logs and Tools

### Access Logs
```nginx
log_format custom '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" "$http_user_agent"';

access_log /var/log/nginx/access.log custom;
```

### Monitoring Tools
- Prometheus integration
- Grafana dashboards
- Custom metrics
- Performance monitoring

## 📁 Serving Static Content Efficiently

### Enable Gzip Compression
```nginx
gzip on;
gzip_types text/plain text/css application/json application/javascript;
```

### Set Cache Headers
```nginx
location /static/ {
    alias /var/www/static/;
    expires 1y;
    add_header Cache-Control "public";
}
```

## 🔄 Handling WebSocket Connections

### Example Configuration
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

### Key Directives
- `proxy_http_version 1.1`
- `proxy_set_header Upgrade $http_upgrade`
- `proxy_set_header Connection "Upgrade"`

## 🏥 Load Balancing with Health Checks

### NGINX Plus Configuration
```nginx
upstream backend {
    server 192.168.1.10;
    server 192.168.1.11;

    health_check;
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```

### Open Source Alternatives
- Custom health check scripts
- Third-party modules
- External monitoring

## 🔒 Security Best Practices

### 1. SSL/TLS Configuration
```nginx
server {
    listen 443 ssl http2;
    server_name example.com;

    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
}
```

### 2. Security Headers
```nginx
server {
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";
}
```

## ⚡ Performance Optimization Tips

### 1. Buffer Optimization
```nginx
client_body_buffer_size 128k;
client_max_body_size 10m;
client_header_buffer_size 1k;
```

## Advanced Caching Strategies 📦

### 1. Proxy Cache
```nginx
proxy_cache_path /path/to/cache levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=60m;

location / {
    proxy_cache my_cache;
    proxy_cache_use_stale error timeout http_500 http_502 http_503 http_504;
    proxy_pass http://backend;
}
```

### 2. FastCGI Cache
```nginx
fastcgi_cache_path /path/to/cache levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=60m;

location / {
    fastcgi_cache my_cache;
    fastcgi_cache_use_stale error timeout http_500 http_502 http_503 http_504;
    fastcgi_pass backend;
}
```

## Conclusion 🎯

These advanced techniques and best practices empower you to maximize NGINX's potential. By optimizing worker processes, enabling HTTP/2, implementing rate limiting, and leveraging tools like maps and health checks, you can build robust, high-performance systems.

---

⬅️ [Previous: Common Questions and Troubleshooting](./06-nginx-qa.md) | ➡️ [Next: Conclusion and Resources](./08-conclusion-and-resources.md) 