# ⚙️ Advanced NGINX Configurations

This chapter delves into advanced NGINX configurations, exploring practical examples and techniques to optimize its performance and functionality. By the end of this chapter, you will have a deeper understanding of how to configure NGINX for specific use cases, including reverse proxying, serving multiple websites, and enabling modern protocols like HTTP/2.

## ⚖️ Layer 4 vs. Layer 7 Load Balancing

### 🌐 Layer 4 (Transport Layer)
- Operates at the transport layer
- Deals with TCP/IP packets
- Inspects source and destination IPs and ports
- Faster but less flexible

Example:
```nginx
stream {
    upstream backend {
        server 192.168.1.10:80;
        server 192.168.1.11:80;
    }

    server {
        listen 80;
        proxy_pass backend;
    }
}
```

### 🔄 Layer 7 (Application Layer)
- Works at the application layer
- Inspects HTTP headers, cookies, and URLs
- Enables advanced routing decisions
- More flexible but slightly slower

Example:
```nginx
http {
    upstream backend {
        server 192.168.1.10;
        server 192.168.1.11;
    }

    server {
        listen 80;

        location /api/v1 {
            proxy_pass http://backend;
        }

        location /api/v2 {
            proxy_pass http://backend_v2;
        }
    }
}
```

## 🏗️ Serving Multiple Websites

NGINX can serve multiple websites from a single instance using **location blocks** and **root directives**.

### Example Configuration
```nginx
http {
    server {
        listen 80;

        location /site1 {
            root /var/www/site1;
            index index.html;
        }

        location /site2 {
            root /var/www/site2;
            index index.html;
        }
    }
}
```

### Directory Structure
```
/var/www/
├── site1/
│   └── index.html
├── site2/
│   └── index.html
```

## ⚡ Enabling HTTP/2 for Improved Performance

HTTP/2 offers significant performance improvements over HTTP/1.1, including multiplexing, header compression, and reduced latency.

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

### Benefits of HTTP/2
- **Multiplexing**: Multiple requests and responses over a single connection
- **Header Compression**: Reduced header size
- **Server Push**: Proactive resource delivery

## 🔄 Reverse Proxy with Load Balancing

### Basic Load Balancing
```nginx
http {
    upstream backend {
        server 192.168.1.10;
        server 192.168.1.11;
        server 192.168.1.12;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://backend;
        }
    }
}
```

### Load Balancing Algorithms
1. **Round Robin (Default)**
   - Distributes requests evenly
   - Simple and effective

2. **IP Hash**
   - Routes same client to same server
   - Good for session persistence
   ```nginx
   upstream backend {
       ip_hash;
       server 192.168.1.10;
       server 192.168.1.11;
   }
   ```

3. **Least Connections**
   - Routes to server with fewest connections
   - Good for varying server capacities

## 👷 Worker Processes and Connections

### Example Configuration
```nginx
events {
    worker_connections 1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    server {
        listen 80;
        server_name example.com;

        location / {
            root /var/www/html;
            index index.html;
        }
    }
}
```

### Key Directives
- **worker_processes**: Number of worker processes
- **worker_connections**: Maximum simultaneous connections per worker

## 📁 Serving Static Content

### Example Configuration
```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        root /var/www/html;
        index index.html;
    }
}
```

### Directory Structure
```
/var/www/html/
├── index.html
├── styles.css
└── script.js
```

## 🚀 Advanced Features

### 1. Rate Limiting
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

### 2. Gzip Compression
```nginx
http {
    gzip on;
    gzip_types text/plain text/css application/json application/javascript;
}
```

### 3. Caching
```nginx
http {
    proxy_cache_path /path/to/cache levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=60m;

    server {
        location / {
            proxy_cache my_cache;
            proxy_cache_use_stale error timeout http_500 http_502 http_503 http_504;
            proxy_pass http://backend;
        }
    }
}
```

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

## 🎯 Conclusion

NGINX's flexibility and performance make it an ideal choice for modern web architectures. By mastering advanced configurations like Layer 4 and Layer 7 load balancing, serving multiple websites, enabling HTTP/2, and optimizing worker processes, you can unlock its full potential.

---

⬅️ [Previous: Understanding NGINX Timeouts](./03-nginx-timeouts.md) | ➡️ [Next: Scaling WebSockets with NGINX](./05-websockets-nginx.md) 