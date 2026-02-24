# BÖLÜM 9 — DJANGO CORE

Bu bölümde şunları öğreneceksin:

- Django architecture
- MTV pattern
- Django project structure
- Django app structure
- settings.py
- .env yönetimi
- Static & Media configuration
- Custom User Model
- Migration mantığı
- Yönetim komutları
- Signals / Middleware / Context Processors
- Production vs Development ayırımı
- Reusable app mantığı

Bu konular Django’nun **iskeleti**dir.

---

# 1️⃣ Django Architecture (Django Mimarisi)

## Django nedir?

Django, Python ile yazılmış yüksek seviyeli bir web framework’tür.

### Amaç

- Hızlı geliştirme
- Güvenli yapı
- Maintainable mimari
- DRY prensibi

---

## Django Request Lifecycle (İstek Yaşam Döngüsü)

### Genel Akış

Browser  
↓  
URL  
↓  
Django  
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

## Detaylı Akış

### 1️⃣ Kullanıcı istek gönderir

    GET /products/

### 2️⃣ URL dispatcher çalışır

    # urls.py
    urlpatterns = [
        path("products/", product_list_view)
    ]

### 3️⃣ View çağrılır

    def product_list_view(request):
        products = Product.objects.all()
        return render(request, "products.html", {"products": products})

### 4️⃣ Model DB ile konuşur

    Product.objects.all()

### 5️⃣ Template render edilir

### 6️⃣ HTML response döner

---

# 2️⃣ MTV Pattern (Model Template View)

Django MVC değil, MTV kullanır.

## Django MTV

- Model → Data
- Template → UI
- View → Business Logic

### Önemli

- Django View = MVC Controller
- Django Template = MVC View

---

# 3️⃣ Model (Data Layer)

    class Product(models.Model):
        name = models.CharField(max_length=255)
        price = models.DecimalField(max_digits=10, decimal_places=2)

Her model bir database tablosudur.

---

# 4️⃣ View (Logic Layer)

    def product_list(request):
        products = Product.objects.all()
        return render(request, "products.html", {"products": products})

---

# 5️⃣ Template (Presentation Layer)

    {% for product in products %}
      <p>{{ product.name }}</p>
    {% endfor %}

---

# 6️⃣ Django Project Structure

Komut:

    django-admin startproject config

## Oluşan yapı

    config/
        manage.py
        config/
            __init__.py
            settings.py
            urls.py
            asgi.py
            wsgi.py

---

## manage.py

Komut çalıştırma dosyasıdır.

    python manage.py runserver
    python manage.py migrate
    python manage.py createsuperuser

---

# 7️⃣ Django App Structure

Komut:

    python manage.py startapp products

## App yapısı

    products/
        models.py
        views.py
        admin.py
        apps.py
        migrations/

App = Feature modülü

Project = Tüm sistem

---

# 8️⃣ Django Project vs App

## Project

Ana sistemdir.

## App

Bağımsız feature modülüdür.

Örnek:

    ecommerce/
        users/
        products/
        orders/

---

# 9️⃣ settings.py (En Kritik Dosya)

Django’nun kalbi.

---

## BASE_DIR

    from pathlib import Path
    BASE_DIR = Path(__file__).resolve().parent.parent

---

# 🔐 .env / Environment Variables Yönetimi

Production’da SECRET_KEY ve DB bilgileri kod içinde tutulmaz.

## 1️⃣ python-decouple kullanımı

Kurulum:

    pip install python-decouple

## .env dosyası

    SECRET_KEY=super-secret-key
    DEBUG=True
    DB_NAME=mydb
    DB_USER=postgres
    DB_PASSWORD=1234

## settings.py

    from decouple import config

    SECRET_KEY = config("SECRET_KEY")
    DEBUG = config("DEBUG", cast=bool)

    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.postgresql",
            "NAME": config("DB_NAME"),
            "USER": config("DB_USER"),
            "PASSWORD": config("DB_PASSWORD"),
        }
    }

---

# 🧱 INSTALLED_APPS (Dev vs Production Ayrımı)

    INSTALLED_APPS = [
        "django.contrib.admin",
        "django.contrib.auth",
        "products",
        "users",
    ]

## Development ortamında

    if DEBUG:
        INSTALLED_APPS += ["debug_toolbar"]

Bu sayede production’da debug toolbar çalışmaz.

---

# 📂 Static & Media Configuration

## Static (CSS, JS)

    STATIC_URL = "/static/"
    STATIC_ROOT = BASE_DIR / "staticfiles"

Production’da:

    python manage.py collectstatic

collectstatic → tüm static dosyaları STATIC_ROOT içine toplar.

---

## Media (User Uploads)

    MEDIA_URL = "/media/"
    MEDIA_ROOT = BASE_DIR / "media"

urls.py:

    from django.conf import settings
    from django.conf.urls.static import static

    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

---

# 👤 Custom User Model (Çok Kritik)

Projeye başlarken yapılmalı.

## users/models.py

    from django.contrib.auth.models import AbstractUser

    class User(AbstractUser):
        phone = models.CharField(max_length=20, blank=True)

## settings.py

    AUTH_USER_MODEL = "users.User"

⚠️ Proje başladıktan sonra değiştirmek migration kaosuna yol açar.

---

# 🔄 Migrations Mantığı

## makemigrations

Model değişikliğini migration dosyasına çevirir.

    python manage.py makemigrations

## migrate

Migration dosyasını DB’ye uygular.

    python manage.py migrate

### Arka Plan

- Django modelleri analiz eder
- SQL üretir
- DB’ye uygular

Migration dosyaları:

    products/migrations/0001_initial.py

---

# ⚙️ Yönetim Komutları

## startapp

    python manage.py startapp blog

## createsuperuser

    python manage.py createsuperuser

## makemigrations

    python manage.py makemigrations

## migrate

    python manage.py migrate

## loaddata

Fixture yükler:

    python manage.py loaddata data.json

---

# 🔔 Signals

Model event’lerini dinler.

    from django.db.models.signals import post_save
    from django.dispatch import receiver

    @receiver(post_save, sender=User)
    def create_profile(sender, instance, created, **kwargs):
        if created:
            Profile.objects.create(user=instance)

Kullanım alanı:

- User oluşturulunca profil oluşturma
- Email gönderme

---

# 🧩 Middleware

Request/response sürecine araya girer.

    MIDDLEWARE = [
        "django.middleware.security.SecurityMiddleware",
    ]

Örnek kullanım:

- Logging
- Authentication kontrolü
- Rate limiting

---

# 🌐 Context Processor

Tüm template’lere global veri gönderir.

    def global_settings(request):
        return {"site_name": "MySite"}

settings.py:

    "context_processors": [
        "django.template.context_processors.request",
        "core.context_processors.global_settings",
    ]

---

# 📦 Reusable App Mantığı

App’ler bağımsız olmalıdır.

Reusable app özellikleri:

- Kendi models
- Kendi templates
- Kendi urls
- Başka projeye pip ile eklenebilir

Bu yaklaşım büyük projelerde clean architecture sağlar.

---

# 🚀 Production Flow

Gerçek akış:

User  
↓  
Nginx  
↓  
Gunicorn  
↓  
Django  
↓  
View  
↓  
Model  
↓  
DB  
↓  
Response  

---

# 🎯 Mülakat Özeti

## Django MVC mi?

Hayır. MTV kullanır.

## Project vs App?

Project tüm sistemdir.  
App feature modülüdür.

## settings.py?

Tüm konfigürasyon merkezidir.

## Migration nedir?

Model değişikliklerini DB’ye uygulama sistemidir.

## Custom User ne zaman yapılmalı?

Projenin en başında.

---

# 🔥 Bu Bölümün Kritik Noktaları

- MTV mantığını bil
- Request lifecycle’ı anlatabil
- settings.py güvenliğini açıkla
- .env kullanımını bil
- Static & Media farkını anlat
- Migration sistemini mantıksal olarak açıkla
- Custom User riskini bil
- Production vs Development ayrımını açıkla

Bu bölüm Django’nun omurgasıdır.