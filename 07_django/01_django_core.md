# BÖLÜM 9 — DJANGO CORE

Bu bölümde şunları öğreneceksin:

- Django architecture
- MTV pattern
- Django project vs app mantığı
- Django project & app structure
- settings.py detaylı yapı
- .env yönetimi
- Static & Media config
- Custom user model
- Migration sistemi
- Yönetim komutları
- Signals, middleware, context processors
- Production / Development ayrımı

Bu bölüm Django’nun **iskeletini** ve production’da nasıl doğru kurulduğunu öğretir.

---

# 1️⃣ Django Architecture (Django Mimarisi)

## Django Nedir?

Django, Python ile yazılmış high-level bir web framework’tür.

Amaçları:

- Hızlı geliştirme
- Güvenli uygulama
- DRY prensibi
- Maintainable yapı

---

## Django Request Lifecycle

Akış:

Browser  
↓  
URL  
↓  
View  
↓  
Model  
↓  
Database  
↓  
Template  
↓  
Response  

---

# 2️⃣ MTV Pattern

Django MVC kullanmaz, MTV kullanır.

## MTV

- Model → Data
- Template → UI
- View → Logic

### Kritik Nokta

- Django View = MVC Controller
- Django Template = MVC View

---

# 3️⃣ Django Project Structure

```
config/
    manage.py
    config/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

## manage.py

Komut çalıştırma aracıdır.

```
python manage.py runserver
python manage.py migrate
python manage.py createsuperuser
```

---

## wsgi.py

Production WSGI server için kullanılır.

Genellikle:
- Gunicorn

---

## asgi.py

Async server için.

Genellikle:
- Uvicorn

---

# 4️⃣ Django App Structure

App = Feature modülü

```
products/
    models.py
    views.py
    admin.py
    apps.py
    migrations/
```

App oluşturma:

```
python manage.py startapp products
```

---

# 5️⃣ Django Project vs App (Paketleme & Reusable App)

## Project

Tüm sistemdir.

## App

Tek bir feature modülüdür.

### Reusable App Mantığı

Bir app başka projeye taşınabiliyorsa reusable’dır.

Örnek:

- blog app
- comment app
- notification app

Reusable app:

- Kendi models.py
- Kendi templates/
- Kendi static/
- Kendi urls.py

Bu yaklaşım büyük projelerde modülerlik sağlar.

---

# 6️⃣ settings.py (En Kritik Dosya)

Django’nun kalbidir.

---

## BASE_DIR

```python
from pathlib import Path
BASE_DIR = Path(__file__).resolve().parent.parent
```

Tüm path işlemleri BASE_DIR üzerinden yapılmalıdır.

---

## SECRET_KEY Güvenliği

❌ Yanlış:

```python
SECRET_KEY = "hardcoded-secret"
```

✅ Doğru: .env kullanmak

---

# 7️⃣ .env / Çevresel Değişken Yönetimi

Production’da config kod içinde tutulmaz.

Kullanılabilecek paketler:

- django-environ
- python-decouple

Örnek (python-decouple):

```
pip install python-decouple
```

.env dosyası:

```
SECRET_KEY=super-secret
DEBUG=False
DATABASE_URL=postgres://user:pass@localhost:5432/db
```

settings.py:

```python
from decouple import config

SECRET_KEY = config("SECRET_KEY")
DEBUG = config("DEBUG", default=False, cast=bool)
```

Bu yaklaşım:

✔ Güvenli  
✔ Production uyumlu  
✔ 12-factor app prensibine uygun  

---

# 8️⃣ INSTALLED_APPS (Dev / Production Ayrımı)

Geliştirme araçları production’da olmamalı.

```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "products",
]

if DEBUG:
    INSTALLED_APPS += [
        "debug_toolbar",
    ]
```

Örnek dev tool:

- django-debug-toolbar

Production ortamda dev tool çalıştırılmaz.

---

# 9️⃣ Static & Media Konfigürasyonu

## STATIC (CSS, JS, Images)

```python
STATIC_URL = "/static/"
STATIC_ROOT = BASE_DIR / "staticfiles"
STATICFILES_DIRS = [BASE_DIR / "static"]
```

Production’da:

```
python manage.py collectstatic
```

collectstatic:

- Tüm static dosyaları
- STATIC_ROOT içine toplar

Web server (nginx) buradan servis eder.

---

## MEDIA (User Upload)

```python
MEDIA_URL = "/media/"
MEDIA_ROOT = BASE_DIR / "media"
```

User upload edilen dosyalar buraya kaydedilir.

Production’da genellikle:

- S3
- Cloud storage

kullanılır.

---

# 🔟 Custom User Model (Çok Kritik)

Django default User model kullanmak ileride sorun çıkarabilir.

Doğru yaklaşım:

Projeye başlarken custom user oluşturmak.

```python
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    phone = models.CharField(max_length=20, blank=True)
```

settings.py:

```python
AUTH_USER_MODEL = "users.User"
```

⚠ Bu işlem migration’dan önce yapılmalıdır.

Sonradan değiştirmek zordur.

---

# 1️⃣1️⃣ Migration Sistemi (Arka Planı)

## makemigrations

Model değişimini algılar.

Migration dosyası üretir.

```
python manage.py makemigrations
```

Bu dosya:

- SQL karşılığı içerir
- Schema değişimini temsil eder

---

## migrate

Migration’ı database’e uygular.

```
python manage.py migrate
```

Arka planda:

- SQL generate edilir
- DB schema update edilir

Migration dosyaları version control’e eklenmelidir.

---

# 1️⃣2️⃣ Yönetim Komutları

## startapp

Yeni app oluşturur.

## makemigrations

Model değişikliklerini yakalar.

## migrate

Database’e uygular.

## createsuperuser

Admin kullanıcı oluşturur.

## loaddata

Fixture yükler:

```
python manage.py loaddata initial_data.json
```

---

# 1️⃣3️⃣ Middleware

Request & response arasında çalışan katmandır.

```python
MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
]
```

Middleware:

- Authentication kontrol eder
- Security header ekler
- Logging yapabilir

Request akışı:

Request → Middleware → View → Middleware → Response

---

# 1️⃣4️⃣ Signals

Model olaylarını dinler.

Örnek:

```python
@receiver(post_save, sender=User)
def create_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)
```

Signals:

- post_save
- pre_save
- post_delete

Business logic’i modelden ayırmak için kullanılır.

---

# 1️⃣5️⃣ Context Processors

Template’e global değişken ekler.

settings.py:

```python
TEMPLATES = [
    {
        "OPTIONS": {
            "context_processors": [
                "django.template.context_processors.request",
            ]
        }
    }
]
```

Örnek kullanım:

- Site adı
- Global ayarlar
- Cart item count

---

# 1️⃣6️⃣ Production Flow

Gerçek akış:

User  
↓  
nginx  
↓  
gunicorn  
↓  
Django  
↓  
urls.py  
↓  
view  
↓  
model  
↓  
database  
↓  
response  

---

# 1️⃣7️⃣ Django Built-in Güçlü Özellikler

- Admin panel
- ORM
- Auth sistemi
- Middleware
- Migration sistemi
- Template engine

---

# Özet

Bu bölümden sonra biliyorsun:

✔ Django MTV mimarisi  
✔ Project vs App farkı  
✔ Reusable app mantığı  
✔ settings.py production düzeni  
✔ .env yönetimi  
✔ Static & media config  
✔ Custom user model  
✔ Migration sistemi  
✔ Middleware, signals, context processors  
✔ Dev vs Production ayrımı  

Bu bilgi seviyesi artık “Django kullanabiliyorum” değil,  
“Django’yu doğru mimariyle kurabiliyorum” seviyesidir.