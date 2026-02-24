# 1. Production Nedir?

## Tanım

Production = Uygulamanın gerçek kullanıcılar tarafından kullanılan **canlı ortamıdır**.

Genelde ortamlar:

```
Local → Development → Staging → Production
```

---

## Ortamların Farkı

| Ortam        | Amaç                          |
|--------------|------------------------------|
| Local        | Geliştirici bilgisayarı       |
| Development  | Geliştirme ortamı             |
| Staging      | Production simülasyonu        |
| Production   | Gerçek kullanıcı ortamı       |

---

# 2. Production Mimarisi (Genel Yapı)

Gerçek bir Django production mimarisi:

```
User (Browser)
    ↓
NGINX
    ↓
Gunicorn
    ↓
Django App
    ↓
PostgreSQL
    ↓
Redis
```

Her bileşenin görevi farklıdır.

---

# 3. NGINX Nedir?

## Tanım

NGINX bir **web server** ve **reverse proxy**’dir.

---

## Görevleri

- HTTP request kabul eder
- Static file sunar
- Django’ya request yönlendirir
- Load balancing yapar

---

## Neden Django Doğrudan İnternetten Request Almaz?

Development server:

```bash
python manage.py runserver
```

Production için uygun değildir çünkü:

- Yavaş
- Güvenli değil
- Scalable değil

---

## NGINX Çözümü

```
User → NGINX → Django
```

---

## Static File Serving

Örnek:

```
/static/style.css
```

NGINX sunar. Django değil.

Bu performansı artırır.

---

# 4. Reverse Proxy Nedir?

Reverse proxy:

```
Client → Proxy → Backend
```

```
Client
  ↓
NGINX
  ↓
Gunicorn
  ↓
Django
```

Client Django’yu direkt görmez.

---

# 5. Gunicorn Nedir?

## Tanım

Gunicorn, Django uygulamasını çalıştıran production **WSGI server**’dır.

---

## Development vs Production

Development:

```bash
python manage.py runserver
```

Production:

```bash
gunicorn config.wsgi
```

---

## Gunicorn Ne Yapar?

- Django app’i çalıştırır
- Worker process oluşturur
- Request işler

---

## Worker Nedir?

Worker = Request işleyen process

Örnek:

```bash
gunicorn config.wsgi --workers 4
```

4 request aynı anda işlenebilir.

---

# 6. WSGI Nedir?

WSGI = Python Web Server Interface

Django’da:

```
config/wsgi.py
```

Flow:

```
NGINX → Gunicorn → WSGI → Django
```

---

# 7. Static ve Media Files

## Static

- CSS
- JS
- Images

## Media

- User uploads

Production’da:

NGINX sunar.

```bash
python manage.py collectstatic
```

---

# 8. Linux Nedir ve Neden Kullanılır?

Production server genelde Linux’tur.

Örnek distro:

- Ubuntu Server

---

## Neden Linux?

- Hızlı
- Güvenli
- Stabil

---

## Temel Linux Komutları

```bash
ls
cd
mkdir
rm
```

Process kontrol:

```bash
ps aux
top
```

---

# 9. Process Management (systemd)

Gunicorn sürekli çalışmalıdır.

systemd bunu sağlar.

Service dosyası:

```
/etc/systemd/system/gunicorn.service
```

Başlatma:

```bash
sudo systemctl start gunicorn
```

Stop:

```bash
sudo systemctl stop gunicorn
```

Restart:

```bash
sudo systemctl restart gunicorn
```

---

# 10. CI/CD Nedir?

CI/CD = Continuous Integration / Continuous Deployment

---

## Pipeline

```
Developer push
   ↓
GitHub
   ↓
CI/CD
   ↓
Server
   ↓
Deploy
```

---

## CI Nedir?

Kod:

- Test edilir
- Build edilir

## CD Nedir?

Kod production’a deploy edilir.

---

## Örnek Araçlar

- GitHub Actions
- GitLab CI
- Jenkins

---

# 11. Deployment Nedir?

Deployment = Kodun production server’a yüklenmesi.

---

## Manuel Deployment

```bash
git pull
docker build
docker run
```

## Otomatik Deployment

CI/CD yapar.

---

# 12. Scaling Nedir?

Scaling = Daha fazla kullanıcıyı destekleme

---

## Vertical Scaling

Server güçlendirme:

```
2 CPU → 8 CPU
```

## Horizontal Scaling

Daha fazla server ekleme:

```
Server 1
Server 2
Server 3
```

NGINX load balancing yapabilir.

---

# 13. Load Balancing Nedir?

Request’leri server’lara dağıtır.

NGINX bunu yapabilir.

---

# 14. Production Deployment Adımları (Django)

1. Server kur
2. Python kur
3. PostgreSQL kur
4. Redis kur
5. Gunicorn kur
6. NGINX kur
7. Django deploy
8. Static files collect

---

# 15. Docker ile Production Deployment

Modern yaklaşım:

- Docker
- Docker Compose
- NGINX
- Gunicorn
- Django
- PostgreSQL
- Redis

---

## docker-compose.yml

```yaml
services:

  web:
    build: .

  db:
    image: postgres

  redis:
    image: redis

  nginx:
    image: nginx
```

---

# 16. Production Request Lifecycle

```
User request
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
 ↓
User
```

---

# 17. Production’da Performans Optimizasyonu

Önemli teknikler:

- Caching (Redis)
- Load balancing
- Async workers
- CDN

---

# 18. Production’da Güvenlik

Önemli önlemler:

- HTTPS
- Firewall
- Secure headers
- Authentication

---

# 19. Gerçek Dünya Production Mimarisi

```
User
 ↓
CDN
 ↓
NGINX
 ↓
Gunicorn
 ↓
Django
 ↓
PostgreSQL
 ↓
Redis
```

---

# 20. Senior Seviyede Bilmen Gereken Kritik Noktalar

## Bilmen Gereken Bileşenler

- NGINX
- Gunicorn
- Linux
- Docker
- CI/CD
- PostgreSQL
- Redis

---

## Bilmen Gereken Flow

```
NGINX → Gunicorn → Django → Database
```

---

# Mülakat Soruları

**NGINX nedir?**  
Web server ve reverse proxy

**Gunicorn nedir?**  
Django production server

**CI/CD nedir?**  
Otomatik test ve deployment

**Scaling nedir?**  
Yük kapasitesini artırma

**Reverse proxy nedir?**  
Client ile backend arasındaki proxy

---

# Özet

**NGINX:**  
Web server

**Gunicorn:**  
Django server

**CI/CD:**  
Otomatik deployment

**Linux:**  
Production OS

**Scaling:**  
Performans artırma