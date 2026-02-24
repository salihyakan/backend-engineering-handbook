# BÖLÜM 12 — DJANGO VIEWS

Bu bölümde şunları öğreneceksin:

- Function Based Views (FBV)
- Class Based Views (CBV)
- Generic Views
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

## Örnek 1 — Basit View

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

Tarayıcı:

```
/hello/
```

Response:

```
Hello World
```

---

# 3️⃣ Template Render Eden View

```python
from django.shortcuts import render

def product_list(request):
    return render(request, "products.html")
```

## render() nedir?

Shortcut fonksiyondur.

Şunları yapar:

- Template’i yükler
- Context ile render eder
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

## Template

```html
{% for product in products %}
  <p>{{ product.name }}</p>
{% endfor %}
```

---

# 5️⃣ Request Object (Çok Önemli)

View’a gelen `request` objesi HTTP bilgilerini taşır.

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

Query parametreleri:

```
/products/?category=1
```

```python
request.GET.get("category")
```

## request.POST

Form verileri:

```python
request.POST.get("name")
```

---

# 6️⃣ GET vs POST Handling

```python
def product_create(request):

    if request.method == "POST":
        name = request.POST.get("name")
        Product.objects.create(name=name)

    return render(request, "create.html")
```

Production’da genelde Django Forms veya ModelForm tercih edilir.

---

# 7️⃣ JsonResponse

API response üretmek için kullanılır.

```python
from django.http import JsonResponse

def api_view(request):
    data = {"name": "Laptop"}
    return JsonResponse(data)
```

Response:

```json
{
  "name": "Laptop"
}
```

---

# 8️⃣ Class Based Views (CBV)

View’ları class olarak yazarsın.

Daha güçlü ve reusable’dır.

## Örnek

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

## as_view() nedir?

Class’ı callable view fonksiyonuna çevirir.

---

# 9️⃣ GET ve POST Handling (CBV)

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

# 🔟 FBV vs CBV Karşılaştırma

## FBV

- Basit
- Okuması kolay
- Küçük projeler için ideal

## CBV

- Daha scalable
- Daha reusable
- Büyük projelerde daha organize

Production’da genelde CBV tercih edilir.

---

# 11️⃣ Generic Views (Çok Önemli)

Django’nun hazır CRUD view class’larıdır.

Production’da çok kullanılır.

---

## 🔹 ListView

Listeleme view’ı

```python
from django.views.generic import ListView
from .models import Product

class ProductListView(ListView):
    model = Product
    template_name = "products.html"
```

Otomatik olarak:

```
SELECT * FROM product
```

Template:

```html
{% for product in object_list %}
    {{ product.name }}
{% endfor %}
```

---

## 🔹 DetailView

Tek object gösterir.

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

# 12️⃣ Context Nedir?

Template’e gönderilen veridir.

```python
return render(request, "template.html", {
    "products": products
})
```

Template içinde:

```html
{{ products }}
```

CBV’de varsayılan context:

```
object
object_list
```

---

# 13️⃣ get_object_or_404 (Production Standard)

```python
from django.shortcuts import get_object_or_404

product = get_object_or_404(Product, id=1)
```

Bulamazsa otomatik 404 döner.

Production’da `try/except` yerine tercih edilir.

---

# 14️⃣ redirect

```python
from django.shortcuts import redirect

return redirect("/products/")
```

Named URL ile kullanım daha doğrudur:

```python
return redirect("product-list")
```

---

# 15️⃣ View Lifecycle

```
request gelir
↓
url match edilir
↓
view çalışır
↓
response döner
```

---

# 16️⃣ Decorators (Çok Önemli)

Login zorunlu yapmak:

```python
from django.contrib.auth.decorators import login_required

@login_required
def dashboard(request):
    return render(request, "dashboard.html")
```

CBV için:

```python
from django.contrib.auth.mixins import LoginRequiredMixin

class DashboardView(LoginRequiredMixin, View):
    pass
```

---

# 17️⃣ Production Best Practice

Production ortamında:

- Generic Views kullan
- CBV tercih et
- View içinde business logic şişirme
- Business logic’i service layer’a taşı
- Fat view, thin model yaklaşımından kaçın

---

# 18️⃣ Gerçek Production Örneği

```python
class ProductListView(ListView):
    model = Product
    paginate_by = 20
    ordering = ["-created_at"]
```

Ek olarak:

```python
def get_queryset(self):
    return Product.objects.filter(is_active=True)
```

---

# 19️⃣ FBV vs CBV vs Generic View — Ne Zaman Hangisi?

## FBV

- Küçük logic
- Basit endpoint
- Hızlı yazım

## CBV

- Reusable logic
- Complex view yapıları

## Generic View

- CRUD işlemleri
- Standart listeleme/detay/form yapıları

Production’da en yaygın kullanım:

> Generic CBV

---

# 🎤 20️⃣ Mülakat Soruları

### Soru:
View nedir?

### Cevap:
Request’i işleyen ve response dönen yapıdır.

---

### Soru:
FBV vs CBV farkı?

### Cevap:
FBV fonksiyon tabanlıdır,  
CBV class tabanlıdır ve daha reusable’dır.

---

### Soru:
Generic View nedir?

### Cevap:
Django’nun hazır CRUD view class’larıdır.

---

# 🎯 Özet

View = request handler

Türleri:

- FBV
- CBV
- Generic Views

Production’da en çok kullanılan yapı:

> Generic Class Based Views