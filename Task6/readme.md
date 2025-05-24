# Настройка Rate Limiting
Доработанный конфиг Nginx с rate limiting — 10 запросов в минуту на IP и ошибкой 429 при превышении:

```nginx
http {
    # Лимит: 10 запросов в минуту (rate = 10r/m)
    limit_req_zone $binary_remote_addr zone=req_limit_per_ip:10m rate=10r/m;

    upstream backend_servers {
        server backend1.example.com;
        server backend2.example.com;
        server backend3.example.com;
    }

    server {
        listen 80;

        location / {
            # Применяем лимит к запросам
            limit_req zone=req_limit_per_ip burst=5 nodelay;

            proxy_pass http://backend_servers;

            # Возвращаем 429 Too Many Requests при превышении лимита
            error_page 429 =429 /rate_limit_exceeded;
        }

        location = /rate_limit_exceeded {
            return 429 'Too Many Requests. Rate limit exceeded.\n';
            add_header Content-Type text/plain;
        }
    }
}
```