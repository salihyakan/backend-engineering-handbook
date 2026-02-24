# BÖLÜM 9 — DJANGO CORE

Bu bölümde şunları öğreneceksin:

- Django architecture
- MTV pattern
- Django project structure
- Django app structure
- settings.py

Bu konular Django’nun **iskeleti**dir.

---

# 1️⃣ Django Architecture (Django Mimarisi)

## Django nedir?

Django, Python ile yazılmış bir web framework’tür.

### Amaç

- Web uygulamalarını hızlı geliştirmek
- Güvenli yapmak
- Maintainable (bakımı kolay) yapmak
- DRY prensibini uygulamak

---

## Django Request Lifecycle (İstek Yaşam Döngüsü)

Bir kullanıcı siteye girince ne olur?

### Genel Akış

Browser → URL → Django → View → Model → Database → View → Template → Response → Browser

---

## Detaylı Akış

### 1️⃣ Kullanıcı istek gönderir

    GET /products/

### 2️⃣ Django URL’i yakalar

    # urls.py
    urlpatterns = [
        path("products/", product_list_view)
    ]

### 3️⃣ Django ilgili view’ı çağırır

    def product_list_view(request):
        products = Product.objects.all()
        return render(request, "products.html", {"products": products})

### 4️⃣ View → Model ile konuşur

    Product.objects.all()

### 5️⃣ Database’ten veri gelir

### 6️⃣ Template render edilir (products.html)

### 7️⃣ HTML response kullanıcıya döner

Bu akış Django’nun temel mimarisidir.

---

# 2️⃣ MTV Pattern (Model Template View)

Django klasik MVC kullanmaz. Onun yerine MTV kullanır.

## MTV Bileşenleri

- Model
- Template
- View

---

## MVC vs MTV

### MVC

- Model → data
- View → UI
- Controller → logic

### Django MTV

- Model → data
- Template → UI
- View → logic

### Önemli

- Django View = MVC Controller
- Django Template = MVC View

---

## MTV Diyagramı

User  
↓  
URL  
↓  
View  ← business logic  
↓  
Model ← database access  
↓  
Template ← HTML render  
↓  
Response  

---

# 3️⃣ Model (Veri Katmanı)

Database’i temsil eder.

    class Product(models.Model):
        name = models.CharField(max_length=255)
        price = models.FloatField()

Bu bir database tablosuna karşılık gelir:

    product table

    id | name | price

---

# 4️⃣ View (Logic Katmanı)

Request alır, logic çalıştırır ve response döner.

    def product_list(request):
        products = Product.objects.all()
        return render(request, "products.html", {"products": products})

---

# 5️⃣ Template (UI Katmanı)

HTML render eder.

    {% for product in products %}
      <p>{{ product.name }}</p>
    {% endfor %}

---

# 6️⃣ Django Project Structure

Komut:

    django-admin startproject config

## Oluşan Yapı

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

Ana kontrol dosyasıdır.

Komutlar:

    python manage.py runserver
    python manage.py migrate
    python manage.py createsuperuser

---

## urls.py

Routing tanımları:

    urlpatterns = [
        path("admin/", admin.site.urls),
    ]

---

## wsgi.py

Production server için kullanılır.

WSGI = Web Server Gateway Interface  
Gunicorn bunu kullanır.

---

## asgi.py

Async server için kullanılır.

ASGI = Asynchronous Server Gateway Interface  
Uvicorn bunu kullanır.

---

# 7️⃣ Django App Structure

Django project birden fazla app içerir.

Örnek:

    config/
    products/
    users/
    orders/

App oluşturma:

    python manage.py startapp products

## App Yapısı

    products/
        __init__.py
        admin.py
        apps.py
        models.py
        views.py
        tests.py
        migrations/

### models.py

    class Product(models.Model):
        name = models.CharField(max_length=255)

### views.py

    def product_list(request):
        pass

### admin.py

    admin.site.register(Product)

### migrations/

Database migration dosyaları.

---

# 8️⃣ Django Project vs App

Çok kritik fark.

## Project

Tüm sistemdir.

## App

Tek bir feature modülüdür.

Örnek:

Project: ecommerce

Apps:

- users
- products
- orders
- payments

Bu yapı clean architecture yaklaşımıdır.

---

# 9️⃣ settings.py (En Kritik Dosya)

Django’nun kalbidir. Tüm config burada yapılır.

## INSTALLED_APPS

    INSTALLED_APPS = [
        'django.contrib.admin',
        'products',
        'users',
    ]

## DATABASES

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': 'mydb',
            'USER': 'user',
            'PASSWORD': 'pass',
        }
    }

## DEBUG

    DEBUG = True

Production:

    DEBUG = False

## ALLOWED_HOSTS

    ALLOWED_HOSTS = ["example.com"]

## MIDDLEWARE

    MIDDLEWARE = [
        'django.middleware.security.SecurityMiddleware',
    ]

## TEMPLATES

Template configuration burada yapılır.

## STATIC_URL

Static dosyalar:

- css
- js
- images

---

# 1️⃣0️⃣ Django Nasıl Çalışır (Production Flow)

Gerçek production akışı:

User → nginx → gunicorn → Django → urls.py → view → model → db → view → template → response

---

# 1️⃣1️⃣ Gerçek Proje Örneği

    ecommerce/
        manage.py

        config/
            settings.py
            urls.py

        products/
            models.py
            views.py

        users/
            models.py

---

# 1️⃣2️⃣ Django’nun Avantajları

- Built-in admin panel
- ORM
- Authentication
- Security
- Scalability

---

# 1️⃣3️⃣ Django’nun Built-in Özellikleri

- auth system
- ORM
- admin panel
- middleware
- migrations
- template engine

---

# Özet (Mülakat İçin Kritik)

## Django Architecture

MTV pattern kullanır.

- Model → data layer
- View → business logic
- Template → presentation layer

## Project

Tüm sistem.

## App

Feature modülüdür.

## settings.py

Django’nun config merkezidir.

## Request Flow

URL → View → Model → Template → Response

---

# Mülakat Soruları

## Soru

Django MVC mi kullanır?

## Cevap

Hayır, MTV kullanır.

---

## Soru

Project ve app farkı nedir?

## Cevap

Project tüm sistemdir, app feature modülüdür.

---

## Soru

settings.py ne işe yarar?

## Cevap

Django’nun tüm config ayarlarını içerir.