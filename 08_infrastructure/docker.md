# 1. Container Nedir?

## Tanım

Container, bir uygulamayı çalıştırmak için gerekli olan her şeyi içeren **izole bir çalışma ortamıdır**.

İçinde şunlar vardır:

- Uygulama kodu
- Python
- Kütüphaneler
- Bağımlılıklar
- Sistem araçları

---

## Problem: “Benim Bilgisayarımda Çalışıyor”

Örnek:

Senin bilgisayarda:

- Python 3.11  
- Django 4.2  

Server’da:

- Python 3.8  
- Django 3.2  

Uygulama çalışmaz.

---

## Docker Çözümü

Docker uygulamayı paketler:

- App
- Python
- Dependencies
- OS libraries

Her yerde aynı çalışır.

---

## Container = Hafif VM Gibi

Ama VM değildir.

### Karşılaştırma

**VM:**

```
App
OS
Hypervisor
Hardware
```

**Docker:**

```
App
Dependencies
Docker Engine
OS
```

Docker daha hızlı ve hafiftir.

---

## Gerçek Örnek

Django app:

```
myproject/
```

Docker container içinde çalışır.

---

## Container Özellikleri

- İzole
- Taşınabilir
- Hızlı
- Reproducible

---

# 2. Image Nedir?

## Tanım

Image = Container’ın blueprint’i  
Container = Image’ın çalışan hali

---

## Analogy

Image:

```
class
```

Container:

```
object
```

---

## Örnek

Image:

```
python:3.11
```

Container:

```
running python container
```

---

## Image İçeriği

- OS layer
- Python
- Libraries
- App code

---

## Örnek Django Image

```dockerfile
FROM python:3.11

WORKDIR /app

COPY . .

RUN pip install django

CMD ["python", "manage.py", "runserver"]
```

---

## Image Build

```bash
docker build -t myapp .
```

---

## Container Run

```bash
docker run myapp
```

---

# 3. Dockerfile Nedir?

## Tanım

Dockerfile = Image oluşturma talimatları

---

## Örnek Django Dockerfile

```dockerfile
FROM python:3.11

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

---

## Komut Açıklamaları

### FROM  
Base image

```dockerfile
FROM python:3.11
```

### WORKDIR  
Çalışma dizini

```dockerfile
WORKDIR /app
```

### COPY  
Dosya kopyalar

```dockerfile
COPY . .
```

### RUN  
Build sırasında çalışır

```dockerfile
RUN pip install django
```

### CMD  
Container başlatıldığında çalışır

```dockerfile
CMD ["python", "manage.py", "runserver"]
```

---

# 4. Docker Build Süreci

```bash
docker build -t myapp .
```

Docker:

```
Dockerfile okur
 ↓
Image oluşturur
```

---

# 5. Container Çalıştırma

```bash
docker run myapp
```

Port mapping:

```bash
docker run -p 8000:8000 myapp
```

Bu sayede:

```
localhost:8000
```

çalışır.

---

# 6. Docker Compose Nedir?

## Problem

Gerçek projede tek container olmaz.

Django app:

- Django
- PostgreSQL
- Redis

3 container gerekir.

---

## docker-compose Çözümü

Hepsini tek dosyada yönetir.

---

## docker-compose.yml Örneği

```yaml
version: "3"

services:

  web:
    build: .
    ports:
      - "8000:8000"

  db:
    image: postgres:15

  redis:
    image: redis:7
```

---

## Çalıştırma

```bash
docker compose up
```

Docker şunu başlatır:

- Django container
- PostgreSQL container
- Redis container

---

# 7. Django Production Docker Örneği

```yaml
version: "3"

services:

  web:
    build: .
    command: gunicorn config.wsgi
    ports:
      - "8000:8000"

  db:
    image: postgres

  redis:
    image: redis
```

---

# 8. Container Lifecycle

Create:

```bash
docker create
```

Start:

```bash
docker start
```

Stop:

```bash
docker stop
```

Delete:

```bash
docker rm
```

---

# 9. Image Lifecycle

Build:

```bash
docker build
```

List:

```bash
docker images
```

Delete:

```bash
docker rmi
```

---

# 10. Production Neden Docker Kullanır?

Çünkü:

- Environment consistency
- Kolay deployment
- Reproducible builds
- Scalable

---

## Production Flow

Developer:

```
build image
```

Push:

```
Docker Hub
```

Server:

```
pull image
run container
```

---

# 11. Docker Registry Nedir?

Image depolama platformu.

Örnek:

- Docker Hub
- GitHub Container Registry

Push:

```bash
docker push myapp
```

Pull:

```bash
docker pull myapp
```

---

# 12. Django + Docker Gerçek Production Mimarisi

```
NGINX
 ↓
Gunicorn
 ↓
Django container
 ↓
PostgreSQL container
 ↓
Redis container
```

---

# 13. Docker vs VM Farkı

**VM:**

- Ağır
- Yavaş

**Docker:**

- Hafif
- Hızlı

---

# 14. Senior Seviyede Bilmen Gereken Kritik Noktalar

## Bilmen Gerekenler

- Container
- Image
- Dockerfile
- Docker Compose
- Port mapping
- Container lifecycle

---

## Bilmen Gereken Production Mimarisi

- Dockerized Django
- Docker Compose services
- Container networking

---

# 15. Mülakat Soruları

**Docker nedir?**  
Container platformu

**Container nedir?**  
İzole çalışma ortamı

**Image nedir?**  
Container blueprint

**Dockerfile nedir?**  
Image oluşturma talimatları

**Docker Compose nedir?**  
Multi-container yönetimi

---

# Özet

**Container:**  
Çalışan ortam

**Image:**  
Blueprint

**Dockerfile:**  
Image talimatları

**Docker Compose:**  
Multi-container yönetimi