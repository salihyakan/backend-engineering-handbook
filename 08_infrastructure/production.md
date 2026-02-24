# PRODUCTION.md — DJANGO PRODUCTION MİMARİSİ VE DEPLOYMENT (Production Seviyesi Rehber)

Bu doküman production ortamında Django uygulamasını güvenli, ölçeklenebilir ve gözlemlenebilir şekilde çalıştırmak için gereken tüm bileşenleri açıklar.

---

# 1. Production Nedir?

## Tanım

Production = Uygulamanın gerçek kullanıcılar tarafından kullanılan canlı ortamıdır.

Ortamlar:

```
Local → Development → Staging → Production
```

---

## Ortamların Farkı

| Ortam | Amaç |
|---|---|
| Local | Geliştirici ortamı |
| Development | Aktif geliştirme |
| Staging | Production simülasyonu |
| Production | Gerçek kullanıcı ortamı |

---

# 2. Production Mimarisi (Genel Yapı)

Modern Django production mimarisi:

```
User
 ↓
CDN
 ↓
NGINX (Reverse Proxy)
 ↓
Gunicorn (WSGI Server)
 ↓
Django App
 ↓
Redis (Cache / Queue)
 ↓
PostgreSQL (Database)
```

---

# 3. NGINX Nedir?

NGINX:

- Web server
- Reverse proxy
- Load balancer

Görevleri:

- HTTP request almak
- Static file serve etmek
- Django’ya yönlendirmek
- Load balancing yapmak

---

## Reverse Proxy Flow

```
Client
 ↓
NGINX
 ↓
Gunicorn
 ↓
Django
```

---

## Static Files Serving

```
/static/css/style.css
```

NGINX sunar.

Django sunmaz.

Bu performans için kritiktir.

---

# 4. NGINX Security Headers

NGINX config örneği:

```
server {

    listen 443 ssl;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";
    add_header Referrer-Policy "same-origin";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";

}
```

Bu header’lar:

- XSS koruması
- Clickjacking koruması
- HTTPS zorunluluğu

sağlar.

---

# 5. Gunicorn Nedir?

Gunicorn = Production WSGI server

Çalıştırma:

```
gunicorn config.wsgi:application --workers 4 --bind 0.0.0.0:8000
```

Worker = paralel request işleyici.

---

# 6. WSGI Nedir?

WSGI = Python Web Server Gateway Interface

Flow:

```
NGINX → Gunicorn → WSGI → Django
```

---

# 7. Static ve Media Files

Static:

- CSS
- JS
- images

Media:

- User upload

Collect:

```
python manage.py collectstatic
```

---

# 8. Production OS: Linux, Container ve Windows Server

Production genelde Linux’tur.

Örnek:

- Ubuntu Server
- Debian
- CentOS

Sebep:

- Stabil
- Güvenli
- Performanslı

Alternatif:

- Docker container içinde çalıştırılabilir
- Windows Server üzerinde de çalışabilir
- Kubernetes cluster içinde çalışabilir

Modern production → containerized environment.

---

# 9. Django Production Security Settings

settings.py:

```
DEBUG = False

SECURE_SSL_REDIRECT = True

SESSION_COOKIE_SECURE = True

CSRF_COOKIE_SECURE = True

SECURE_BROWSER_XSS_FILTER = True

SECURE_HSTS_SECONDS = 31536000

SECURE_HSTS_INCLUDE_SUBDOMAINS = True

SECURE_HSTS_PRELOAD = True

X_FRAME_OPTIONS = "DENY"
```

Bu ayarlar:

- HTTPS zorlar
- Cookie güvenliğini artırır
- XSS korur
- Clickjacking korur

---

# 10. Secrets Management

ASLA secrets kod içinde tutulmaz.

Yanlış:

```
SECRET_KEY = "123456"
```

Doğru:

.env

```
SECRET_KEY=abcxyz
DATABASE_URL=postgres://...
```

.gitignore:

```
.env
```

---

## Professional Secrets Management

Araçlar:

- HashiCorp Vault
- AWS Secrets Manager
- Azure Key Vault

Production’da önerilir.

---

# 11. Docker Compose Production Örneği

docker-compose.yml:

```
version: "3.9"

services:

  web:
    build: .
    command: gunicorn config.wsgi:application --bind 0.0.0.0:8000
    env_file:
      - .env
    volumes:
      - static_volume:/app/static
      - media_volume:/app/media
    depends_on:
      - db
      - redis
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 5

  db:
    image: postgres:15
    env_file:
      - .env
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7

  nginx:
    image: nginx
    volumes:
      - static_volume:/static
      - media_volume:/media
    ports:
      - "80:80"
      - "443:443"

volumes:

  postgres_data:
  static_volume:
  media_volume:
```

---

# 12. CI/CD Pipeline (GitHub Actions)

.github/workflows/deploy.yml:

```
name: CI/CD Pipeline

on:
  push:
    branches: [main]

jobs:

  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Run tests
        run: |
          pip install -r requirements.txt
          python manage.py test

  build:
    needs: test
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Build Docker
        run: docker build -t myapp .

  push:
    needs: build
    runs-on: ubuntu-latest

    steps:
      - name: Push to registry
        run: docker push myapp

  deploy:
    needs: push
    runs-on: ubuntu-latest

    steps:
      - name: Deploy
        run: ssh server "docker pull myapp && docker restart container"
```

Pipeline:

```
Test → Build → Push → Deploy
```

---

# 13. Scaling Nedir?

Scaling = daha fazla kullanıcıyı destekleme.

---

## Vertical Scaling

```
2 CPU → 8 CPU
```

Server güçlendirilir.

---

## Horizontal Scaling

```
Server 1
Server 2
Server 3
```

NGINX load balance eder.

---

# 14. Database Scaling

## Read Replicas

```
Primary DB → Write
Replica DB → Read
```

Read yükü dağıtılır.

---

## Sharding

Data bölünür:

```
Shard 1 → Users 1–1000
Shard 2 → Users 1001–2000
```

---

## Vertical DB Scaling

Daha güçlü database server.

---

## Horizontal DB Scaling

Birden fazla database node.

---

# 15. Load Balancing

NGINX config:

```
upstream django {
    server web1:8000;
    server web2:8000;
}

server {
    location / {
        proxy_pass http://django;
    }
}
```

---

# 16. Monitoring ve Logging

Production’da zorunludur.

---

## Sentry (Error tracking)

```
pip install sentry-sdk
```

Yakalar:

- Exception
- Traceback
- User info

---

## Prometheus + Grafana

Monitor eder:

- CPU
- RAM
- Request rate
- Response time

---

## ELK Stack

- Elasticsearch
- Logstash
- Kibana

Log analiz sistemi.

---

# 17. Process Management (systemd)

Gunicorn service:

```
sudo systemctl start gunicorn
sudo systemctl stop gunicorn
sudo systemctl restart gunicorn
```

---

# 18. Production Request Lifecycle

```
User
 ↓
NGINX
 ↓
Gunicorn
 ↓
Django
 ↓
Database
 ↓
Response
```

---

# 19. Production Performance Optimizasyonu

Kritik teknikler:

- Redis cache
- CDN
- Load balancing
- Horizontal scaling
- Database replicas

---

# 20. Production Deployment Checklist

Production öncesi kontrol listesi:

- DEBUG=False
- HTTPS aktif
- SECRET_KEY secure
- Database backup aktif
- Logging aktif
- Monitoring aktif
- CI/CD aktif
- Docker kullanılıyor
- Load balancing hazır

---

# 21. Senior Level Production Architecture

Modern production mimarisi:

```
User
 ↓
Cloudflare (CDN)
 ↓
NGINX
 ↓
Docker containers
 ↓
Gunicorn
 ↓
Django
 ↓
Redis
 ↓
PostgreSQL Primary
 ↓
PostgreSQL Replica
```

---

# Mülakat Soruları

NGINX nedir?  
Reverse proxy ve web server.

Gunicorn nedir?  
Python WSGI production server.

CI/CD nedir?  
Otomatik test ve deployment pipeline.

Scaling nedir?  
Sistem kapasitesini artırma.

Secrets management nedir?  
Secret bilgileri güvenli saklama sistemi.

---

# Özet

NGINX → Reverse proxy  
Gunicorn → Django server  
Docker → Containerization  
CI/CD → Automation  
Redis → Cache  
PostgreSQL → Database  
Monitoring → System health  
Scaling → Performance

Production ortamı sadece uygulamayı çalıştırmak değil,  
onu güvenli, ölçeklenebilir ve izlenebilir hale getirmektir.