# BÖLÜM 13 — DJANGO REST FRAMEWORK (DRF)

Bu bölümde şunları öğreneceksin:

- Serializer nedir?
- ModelSerializer nedir?
- APIView nedir?
- ViewSet nedir?
- Router nedir?

Bu konular profesyonel backend geliştirmenin temelidir.

---

## 1️⃣ Django REST Framework (DRF) Nedir?

Django REST Framework, Django ile REST API geliştirmek için kullanılan framework’tür.

- Django → Web sitesi geliştirme
- DRF → API geliştirme

### API Nedir?

API = Application Programming Interface

Frontend ile backend’in konuşmasını sağlar.

### Örnek

Frontend isteği:

```http
GET /api/products/
```

Backend response:

```json
[
  {
    "id": 1,
    "name": "Laptop",
    "price": 5000
  }
]
```

---

## 2️⃣ Serializer Nedir?

Serializer = Model ↔ JSON dönüşümü yapar.

Çünkü:

- Database → Python object
- API → JSON döner

Serializer bu dönüşümü sağlar.

---

### Örnek Model

```python
class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
```

---

### Serializer Yazımı (Manuel)

```python
from rest_framework import serializers

class ProductSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    name = serializers.CharField()
    price = serializers.DecimalField(max_digits=10, decimal_places=2)
```

---

### Object → JSON Dönüşümü

```python
product = Product.objects.first()

serializer = ProductSerializer(product)

serializer.data
```

Sonuç:

```json
{
  "id": 1,
  "name": "Laptop",
  "price": "5000.00"
}
```

---

### JSON → Object Dönüşümü

```python
data = {
    "name": "Phone",
    "price": "3000.00"
}

serializer = ProductSerializer(data=data)

if serializer.is_valid():
    validated_data = serializer.validated_data
```

> Not: Manuel `Serializer` kullanıyorsan `create()` ve `update()` methodlarını yazman gerekir, aksi halde `serializer.save()` çalışmaz.

---

## 3️⃣ ModelSerializer (Çok Önemli)

En çok kullanılan serializer türüdür.

Model’e göre otomatik alan üretir.

---

### Örnek

```python
from rest_framework import serializers
from .models import Product

class ProductSerializer(serializers.ModelSerializer):

    class Meta:
        model = Product
        fields = "__all__"
```

Bu otomatik üretir:

- id
- name
- price

---

### Specific Fields

```python
class Meta:
    model = Product
    fields = ["id", "name"]
```

---

### Neden ModelSerializer?

- Daha az kod
- Otomatik validation
- Otomatik `create()` ve `update()`
- Production standard

---

## 4️⃣ APIView

DRF’in en temel view class’ıdır.

Django `View`’un API versiyonudur.

---

### GET Örneği

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from .models import Product
from .serializers import ProductSerializer

class ProductListAPIView(APIView):

    def get(self, request):
        products = Product.objects.all()
        serializer = ProductSerializer(products, many=True)
        return Response(serializer.data)
```

---

### POST Örneği

```python
def post(self, request):

    serializer = ProductSerializer(data=request.data)

    if serializer.is_valid():
        serializer.save()
        return Response(serializer.data)

    return Response(serializer.errors, status=400)
```

---

## 5️⃣ request.data

Django:

```python
request.POST
```

DRF:

```python
request.data
```

`request.data`:

- JSON
- Form data
- Multipart

Hepsini destekler.

---

## 6️⃣ ViewSet (Çok Çok Önemli)

APIView’in daha gelişmiş halidir.

CRUD işlemlerini otomatik sağlar.

---

### Örnek

```python
from rest_framework.viewsets import ModelViewSet

class ProductViewSet(ModelViewSet):

    queryset = Product.objects.all()
    serializer_class = ProductSerializer
```

Bu tek class şunları sağlar:

- GET /products/
- GET /products/1/
- POST /products/
- PUT /products/1/
- DELETE /products/1/

Bu production standardıdır.

---

## 7️⃣ Router

URL’leri otomatik oluşturur.

---

### Örnek

```python
from rest_framework.routers import DefaultRouter
from .views import ProductViewSet

router = DefaultRouter()
router.register("products", ProductViewSet)

urlpatterns = router.urls
```

Otomatik oluşan URL’ler:

- /products/
- /products/1/

---

## 8️⃣ DRF Request–Response Flow

```
Request
   ↓
APIView / ViewSet
   ↓
Serializer
   ↓
Model
   ↓
Database
   ↓
Serializer
   ↓
Response (JSON)
```

---

## 9️⃣ many=True Nedir?

Birden fazla object serialize eder.

```python
serializer = ProductSerializer(products, many=True)
```

Tek object:

```python
serializer = ProductSerializer(product)
```

---

## 🔟 serializer.save()

Create veya update yapar.

---

### Create

```python
serializer = ProductSerializer(data=data)

serializer.is_valid()
serializer.save()
```

---

### Update

```python
serializer = ProductSerializer(instance, data=data)
serializer.is_valid()
serializer.save()
```

---

## 1️⃣1️⃣ Validation

Serializer içinde validation yazılabilir.

```python
class ProductSerializer(serializers.ModelSerializer):

    def validate_price(self, value):

        if value < 0:
            raise serializers.ValidationError("Price cannot be negative")

        return value
```

---

## 1️⃣2️⃣ Field Options

### Read Only

```python
id = serializers.IntegerField(read_only=True)
```

### Write Only

```python
password = serializers.CharField(write_only=True)
```

---

## 1️⃣3️⃣ Production Best Practice

Production’da genelde şu kombinasyon kullanılır:

- ModelSerializer
- ModelViewSet
- Router

---

## 1️⃣4️⃣ APIView vs ViewSet Farkı

### APIView

- Daha fazla kontrol
- Daha fazla kod

### ViewSet

- Daha az kod
- Daha hızlı development
- Production’da yaygın kullanım

---

## 1️⃣5️⃣ Gerçek Production Örneği

```python
from rest_framework.permissions import IsAuthenticated

class ProductViewSet(ModelViewSet):

    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    permission_classes = [IsAuthenticated]
```

---

## 1️⃣6️⃣ Serializer Lifecycle

```
Input JSON
   ↓
Serializer validation
   ↓
serializer.save()
   ↓
Model instance
   ↓
Database
   ↓
serializer.data
   ↓
Response JSON
```

---

## 1️⃣7️⃣ Mülakat Soruları

**Soru:** Serializer nedir?  
**Cevap:** Model ile JSON arasında dönüşüm yapan yapıdır.

**Soru:** ModelSerializer nedir?  
**Cevap:** Model’e göre otomatik serializer oluşturan class’tır.

**Soru:** APIView nedir?  
**Cevap:** DRF’in temel API view class’ıdır.

**Soru:** ViewSet nedir?  
**Cevap:** CRUD işlemlerini otomatik sağlayan view class’ıdır.

**Soru:** Router nedir?  
**Cevap:** ViewSet’ler için otomatik URL oluşturan yapıdır.

---

# Özet

### Serializer
Model ↔ JSON dönüşümü

### APIView
Temel API view

### ViewSet
Production standard CRUD view

### Router
Automatic URL generator