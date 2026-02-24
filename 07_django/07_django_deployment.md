# BÖLÜM 07 — DJANGO DEPLOYMENT

Bu bölümde şunları öğreneceksin:

- Django production ortamına deploy
- Nginx ve Gunicorn yapılandırmaları
- Systemd / Supervisor ile process yönetimi
- Celery ve Redis entegrasyonu
- Docker + Docker Compose ile containerization
- Kubernetes manifest örnekleri
- Loglama ve monitoring (Sentry)
- 12-factor app prensipleri

---

# 1️⃣ Production ve Development Ayrımı

Development:

- `DEBUG = True`
- Local SQLite veya dev DB
- Basit runserver

Production:

- `DEBUG = False`
- PostgreSQL / MySQL
- Gunicorn + Nginx
- HTTPS
- Load balancing, caching, logging

---

# 2️⃣ Nginx Konfigürasyonu

Örnek `nginx.conf`:

```nginx
server {
    listen 80;
    server_name example.com;

    location /static/ {
        alias /home/user/project/static/;
    }

    location /media/ {
        alias /home/user/project/media/;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

---

# 3️⃣ Gunicorn + Systemd

## Gunicorn komut:

```bash
gunicorn config.wsgi:application --bind 127.0.0.1:8000 --workers 3
```

## systemd unit örneği

`/etc/systemd/system/gunicorn.service`:

```ini
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/project
ExecStart=/home/ubuntu/project/venv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind 127.0.0.1:8000 \
          config.wsgi:application

[Install]
WantedBy=multi-user.target
```

Komutlar:

```bash
sudo systemctl daemon-reload
sudo systemctl start gunicorn
sudo systemctl enable gunicorn
```

---

# 4️⃣ Supervisor Örneği

`/etc/supervisor/conf.d/project.conf`:

```ini
[program:project]
command=/home/ubuntu/project/venv/bin/gunicorn config.wsgi:application -b 127.0.0.1:8000
directory=/home/ubuntu/project
user=ubuntu
autostart=true
autorestart=true
stderr_logfile=/var/log/project.err.log
stdout_logfile=/var/log/project.out.log
```

---

# 5️⃣ Celery + Redis

## Celery Worker

```python
# config/celery.py
from __future__ import absolute_import, unicode_literals
import os
from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'config.settings')
app = Celery('config')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()
```

Başlat:

```bash
celery -A config worker -l info
celery -A config beat -l info
```

## Redis Broker

```python
CELERY_BROKER_URL = 'redis://localhost:6379/0'
CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
```

---

# 6️⃣ Dockerfile Örneği

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["gunicorn", "config.wsgi:application", "--bind", "0.0.0.0:8000"]
```

---

# 7️⃣ Docker Compose Örneği

```yaml
version: "3.9"

services:
  web:
    build: .
    command: gunicorn config.wsgi:application --bind 0.0.0.0:8000
    volumes:
      - .:/app
    ports:
      - "8000:8000"
    depends_on:
      - redis
      - db

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: project

  redis:
    image: redis:7
```

---

# 8️⃣ Kubernetes Manifest Örneği

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: django
  template:
    metadata:
      labels:
        app: django
    spec:
      containers:
      - name: django
        image: myregistry/project:latest
        ports:
        - containerPort: 8000
```

Service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: django-service
spec:
  selector:
    app: django
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8000
  type: LoadBalancer
```

---

# 9️⃣ Loglama ve Monitoring

## Django LOGGING setting

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'file': {
            'level': 'INFO',
            'class': 'logging.FileHandler',
            'filename': '/var/log/django/info.log',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'INFO',
            'propagate': True,
        },
    },
}
```

## Sentry Kurulumu

```python
import sentry_sdk
from sentry_sdk.integrations.django import DjangoIntegration

sentry_sdk.init(
    dsn="YOUR_SENTRY_DSN",
    integrations=[DjangoIntegration()],
    traces_sample_rate=1.0,
    send_default_pii=True
)
```

---

# 🔟 12-Factor App İlkeleri (Deployment Odaklı)

1. **Config**: Environment variables ile yönet
2. **Dependencies**: requirements.txt / Pipfile
3. **Backing Services**: DB, Redis gibi external services
4. **Build, release, run**: Ayrı adımlar (Dockerfile + Compose)
5. **Processes**: Stateless processes, worker + web
6. **Port binding**: Container / service port binding
7. **Concurrency**: Gunicorn worker sayısı, Celery concurrency
8. **Disposability**: hızlı start/stop
9. **Dev/prod parity**: Production environment mirror development
10. **Logs**: STDOUT/monitoring
11. **Admin processes**: migrations, management commands
12. **Monitoring**: Sentry, Prometheus

---

# 🎯 Özet

- Production = Gunicorn + Nginx + Celery + Redis
- Docker / Compose ile kolay deploy
- K8s ile scalable deployment
- Logging ve monitoring şart
- 12-factor principles ile temiz yapı