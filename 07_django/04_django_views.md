# BÖLÜM 12 — DJANGO VIEWS

Bu bölümde şunları öğreneceksin:

- Function Based Views (FBV)
- Class Based Views (CBV)
- Generic Views
- View lifecycle
- Mixin yapısı
- Form view’lar
- View.as_view() mantığı
- DRF ile API entegrasyonu
- Hangisi ne zaman kullanılır
- Production seviyesinde doğru kullanım

---

# 1️⃣ View Nedir?

View, gelen HTTP request’i işleyen ve response dönen yapıdır.

Basit tanım:

> View = Request → Logic → Response

## Request–Response Akışı

User → URL → View → Model → View → Response → User

Örnek:

```
GET /products/
```

Akış:

```
urls.py → views.py → response
```

---

# 2️⃣ Function Based Views (FBV)

En basit view türüdür.

Normal Python fonksiyonudur.

## Örnek — Basit View

```python
from django.http import HttpResponse

def hello_view(request):
    return HttpResponse("Hello World")
```

## URL Bağlantısı

```python
from django.urls import path
from .views import hello_view

urlpatterns = [
    path("hello/", hello_view),
]
```

---

# 3️⃣ Template Render Eden FBV

```python
from django.shortcuts import render

def product_list(request):
    return render(request, "products.html")
```

## render() ne yapar?

- Template’i yükler
- Context ile birleştirir
- HttpResponse döner

---

# 4️⃣ Database Kullanan FBV

```python
from .models import Product
from django.shortcuts import render

def product_list(request):
    products = Product.objects.all()

    return render(request, "products.html", {
        "products": products
    })
```

---

# 5️⃣ Request Object (Çok Önemli)

```python
def example_view(request):
    print(request.method)
    print(request.GET)
    print(request.POST)
```

## request.method

- GET
- POST
- PUT
- DELETE

## request.GET

```
/products/?category=1
```

```python
request.GET.get("category")
```

## request.POST

```python
request.POST.get("name")
```

---

# 6️⃣ GET vs POST Handling (FBV)

```python
def product_create(request):

    if request.method == "POST":
        name = request.POST.get("name")
        Product.objects.create(name=name)

    return render(request, "create.html")
```

Production’da Django Forms tercih edilir.

---

# 7️⃣ JsonResponse

```python
from django.http import JsonResponse

def api_view(request):
    data = {"name": "Laptop"}
    return JsonResponse(data)
```

---

# 8️⃣ Class Based Views (CBV)

View’ları class olarak yazarsın.

Daha reusable ve scalable’dır.

## Basit CBV

```python
from django.views import View
from django.http import HttpResponse

class HelloView(View):

    def get(self, request):
        return HttpResponse("Hello from CBV")
```

## urls.py

```python
path("hello/", HelloView.as_view())
```

---

# 9️⃣ View.as_view() Nedir?

`as_view()`:

- Class’ı callable fonksiyona çevirir
- Her request için yeni instance oluşturur
- dispatch() metodunu tetikler

Gerçekte olan:

```
as_view()
   ↓
instance oluşturulur
   ↓
dispatch()
   ↓
get() / post() çağrılır
```

---

# 🔟 CBV Lifecycle (Yaşam Döngüsü)

Bir CBV’nin yaşam akışı:

```
request gelir
↓
as_view() çağrılır
↓
class instance oluşturulur
↓
dispatch() çalışır
↓
HTTP method tespit edilir
↓
get() / post() / put() çağrılır
↓
response döner
```

## dispatch()

HTTP method routing yapar.

```python
def dispatch(self, request, *args, **kwargs):
    return super().dispatch(request, *args, **kwargs)
```

---

# 11️⃣ GET ve POST Handling (CBV)

```python
from django.views import View
from django.http import JsonResponse
from .models import Product

class ProductView(View):

    def get(self, request):
        products = Product.objects.all()
        return JsonResponse({"count": products.count()})

    def post(self, request):
        Product.objects.create(name="Test")
        return JsonResponse({"status": "created"})
```

---

# 12️⃣ Generic Views (Çok Önemli)

Django’nun hazır CRUD CBV yapılarıdır.

Production’da yoğun kullanılır.

---

## 🔹 ListView

```python
from django.views.generic import ListView
from .models import Product

class ProductListView(ListView):
    model = Product
    template_name = "products.html"
    paginate_by = 20
```

Varsayılan context:

```
object_list
```

---

## 🔹 DetailView

```python
from django.views.generic import DetailView

class ProductDetailView(DetailView):
    model = Product
    template_name = "product_detail.html"
```

URL:

```python
path("product/<int:pk>/", ProductDetailView.as_view())
```

---

## 🔹 CreateView

```python
from django.views.generic import CreateView

class ProductCreateView(CreateView):
    model = Product
    fields = ["name", "price"]
    success_url = "/products/"
```

---

## 🔹 UpdateView

```python
from django.views.generic import UpdateView
```

---

## 🔹 DeleteView

```python
from django.views.generic import DeleteView
```

---

# 13️⃣ FormView ve Form Handling

FormView daha kontrollü form yönetimi sağlar.

```python
from django.views.generic import FormView
from .forms import ContactForm

class ContactView(FormView):
    template_name = "contact.html"
    form_class = ContactForm
    success_url = "/thanks/"

    def form_valid(self, form):
        form.save()
        return super().form_valid(form)
```

Lifecycle:

```
GET → boş form
POST → form_valid / form_invalid
```

---

# 14️⃣ Mixinler (Çok Kritik)

Mixin = Reusable davranış modülü.

## LoginRequiredMixin

```python
from django.contrib.auth.mixins import LoginRequiredMixin

class DashboardView(LoginRequiredMixin, ListView):
    model = Product
```

## Mixin Sırası Önemlidir

Mixin’ler soldan sağa çalışır.

```
class A(B, C, D)
```

Python MRO (Method Resolution Order) uygulanır.

---

# 15️⃣ get_queryset Override (Production Seviyesi)

```python
class ProductListView(ListView):
    model = Product

    def get_queryset(self):
        return Product.objects.filter(is_active=True)
```

---

# 16️⃣ get_context_data Override

```python
def get_context_data(self, **kwargs):
    context = super().get_context_data(**kwargs)
    context["title"] = "Product List"
    return context
```

---

# 17️⃣ get_object_or_404

```python
from django.shortcuts import get_object_or_404

product = get_object_or_404(Product, id=1)
```

Production’da standarttır.

---

# 18️⃣ redirect

```python
from django.shortcuts import redirect

return redirect("product-list")
```

---

# 19️⃣ FBV vs CBV vs Generic — Ne Zaman?

## FBV

- Küçük endpoint
- Basit logic
- Hızlı geliştirme

## CBV

- Reusable yapı
- Complex view’lar

## Generic CBV

- CRUD işlemleri
- Pagination
- Filtering
- Production standard

En yaygın kullanım:

> Generic Class Based Views

---

# 20️⃣ API Tarafı — DRF Entegrasyonu

Django HTML için optimize edilmiştir.

API için genelde Django REST Framework kullanılır.

---

## DRF View

```python
from rest_framework.views import APIView
from rest_framework.response import Response

class ProductAPIView(APIView):

    def get(self, request):
        return Response({"message": "API working"})
```

---

## DRF Generic API View

```python
from rest_framework.generics import ListAPIView
from .models import Product
from .serializers import ProductSerializer

class ProductListAPI(ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
```

---

## DRF ViewSet (En Production)

```python
from rest_framework.viewsets import ModelViewSet

class ProductViewSet(ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
```

Router otomatik URL üretir.

---

# 21️⃣ Production Best Practices

- Generic CBV kullan
- Mixin ile yetkilendirme yap
- View içinde business logic şişirme
- Service layer oluştur
- API için DRF kullan
- Pagination ve filtering ekle
- get_queryset override et

---

# 🎤 22️⃣ Mülakat Soruları

### Soru:
CBV lifecycle nedir?

### Cevap:
as_view → instance → dispatch → http method → response

---

### Soru:
Mixin nedir?

### Cevap:
Reusable davranış modülüdür.

---

### Soru:
Generic View neden tercih edilir?

### Cevap:
CRUD işlemlerini boilerplate kod yazmadan sağlar.

---

### Soru:
DRF neden kullanılır?

### Cevap:
RESTful API geliştirmek için optimize edilmiştir.

---

# 🎯 Özet

View = Request handler

Türleri:

- FBV
- CBV
- Generic CBV
- DRF API View

Production standard:

> Generic CBV + Mixin + DRF