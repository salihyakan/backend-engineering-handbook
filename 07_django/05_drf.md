# BÖLÜM 13 — DJANGO REST FRAMEWORK (DRF)

Bu bölümde şunları öğreneceksin:

- Serializer nedir?
- ModelSerializer nedir?
- APIView nedir?
- ViewSet nedir?
- Router nedir?
- Permissions & Authentication
- Throttling (Rate Limiting)
- Pagination
- Filtering & Search
- Custom actions
- Nested serializers
- Versioning & Schema
- CORS
- Performance optimizasyonları
- GenericAPIView & generics
- Logging, audit, signal mantığı

Bu konular production seviyesinde API geliştirmenin temelidir.

---

## 1️⃣ Django REST Framework (DRF) Nedir?

Django REST Framework, Django ile REST API geliştirmek için kullanılan framework’tür.

- Django → Web uygulaması
- DRF → REST API

### API Nedir?

Frontend ile backend’in HTTP üzerinden haberleşmesini sağlar.

Örnek:

```http
GET /api/products/
```

Response:

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

Database → Python object  
API → JSON

---

### Manuel Serializer

```python
class ProductSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    name = serializers.CharField()
    price = serializers.DecimalField(max_digits=10, decimal_places=2)
```

Manuel serializer kullanıyorsan `create()` ve `update()` yazmalısın.

---

## 3️⃣ ModelSerializer

Production’da en çok kullanılan serializer türüdür.

```python
class ProductSerializer(serializers.ModelSerializer):

    class Meta:
        model = Product
        fields = "__all__"
```

Avantaj:

- Otomatik field
- Otomatik validation
- Otomatik create/update

---

## 4️⃣ Nested Serializer & İlişkiler

ForeignKey ilişkisi:

```python
class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ["id", "name"]


class ProductSerializer(serializers.ModelSerializer):
    category = CategorySerializer()

    class Meta:
        model = Product
        fields = "__all__"
```

---

### SerializerMethodField

Dynamic field üretmek için:

```python
class ProductSerializer(serializers.ModelSerializer):

    discounted_price = serializers.SerializerMethodField()

    def get_discounted_price(self, obj):
        return obj.price * 0.9
```

---

## 5️⃣ APIView

Temel view class.

```python
class ProductListAPIView(APIView):

    def get(self, request):
        products = Product.objects.all()
        serializer = ProductSerializer(products, many=True)
        return Response(serializer.data)
```

---

## 6️⃣ GenericAPIView & Generics (Önerilen)

APIView yerine çoğu durumda generics kullanılır.

### CreateAPIView

```python
from rest_framework.generics import CreateAPIView

class ProductCreateAPIView(CreateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
```

### ListCreateAPIView

```python
class ProductListCreateAPIView(ListCreateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
```

Daha az kod, daha temiz yapı.

---

## 7️⃣ ViewSet (Production Standard)

```python
class ProductViewSet(ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
```

CRUD otomatik sağlanır.

---

### Custom Action (@action)

```python
from rest_framework.decorators import action
from rest_framework.response import Response

class ProductViewSet(ModelViewSet):

    @action(detail=True, methods=["post"])
    def mark_as_featured(self, request, pk=None):
        product = self.get_object()
        product.is_featured = True
        product.save()
        return Response({"status": "featured"})
```

Endpoint:

```http
POST /products/1/mark_as_featured/
```

---

## 8️⃣ Router

```python
router = DefaultRouter()
router.register("products", ProductViewSet)
```

---

## 9️⃣ Permissions

### Built-in Permissions

```python
IsAuthenticated
IsAuthenticatedOrReadOnly
IsAdminUser
AllowAny
```

Örnek:

```python
permission_classes = [IsAuthenticatedOrReadOnly]
```

---

### Custom Permission

```python
from rest_framework.permissions import BasePermission

class IsOwner(BasePermission):

    def has_object_permission(self, request, view, obj):
        return obj.user == request.user
```

---

## 🔟 Authentication Türleri Karşılaştırma

| Tür            | Stateful | Kullanım Alanı        |
|---------------|----------|----------------------|
| SessionAuth   | Evet     | Web app              |
| BasicAuth     | Hayır    | Test / internal      |
| TokenAuth     | Kısmen   | Basit API            |
| JWT           | Hayır    | Production standard  |

settings.py:

```python
REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "rest_framework.authentication.SessionAuthentication",
        "rest_framework.authentication.BasicAuthentication",
        "rest_framework.authentication.TokenAuthentication",
    ]
}
```

---

## 1️⃣1️⃣ Throttling (Rate Limit)

API abuse’u engeller.

### Built-in

- AnonRateThrottle
- UserRateThrottle

settings.py:

```python
REST_FRAMEWORK = {
    "DEFAULT_THROTTLE_CLASSES": [
        "rest_framework.throttling.AnonRateThrottle",
        "rest_framework.throttling.UserRateThrottle",
    ],
    "DEFAULT_THROTTLE_RATES": {
        "anon": "100/day",
        "user": "1000/day",
    }
}
```

Mantık:

```
Belirli zaman aralığında max request sayısı
```

---

## 1️⃣2️⃣ Pagination

### PageNumberPagination

```
/products/?page=2
```

### LimitOffsetPagination

```
/products/?limit=10&offset=20
```

### CursorPagination (Önerilir)

- Büyük veri için ideal
- Offset yerine cursor kullanır

settings.py:

```python
REST_FRAMEWORK = {
    "DEFAULT_PAGINATION_CLASS": 
        "rest_framework.pagination.PageNumberPagination",
    "PAGE_SIZE": 10
}
```

---

## 1️⃣3️⃣ Filter, Search, Ordering

### django-filter

```bash
pip install django-filter
```

```python
filterset_fields = ["category", "price"]
```

---

### SearchFilter

```python
search_fields = ["name"]
```

```
/products/?search=laptop
```

---

### OrderingFilter

```python
ordering_fields = ["price"]
```

```
/products/?ordering=-price
```

---

## 1️⃣4️⃣ Versioning

API version kontrolü sağlar.

```python
REST_FRAMEWORK = {
    "DEFAULT_VERSIONING_CLASS":
        "rest_framework.versioning.URLPathVersioning"
}
```

URL:

```
/api/v1/products/
```

---

## 1️⃣5️⃣ Schema & Swagger (OpenAPI)

Otomatik API dokümantasyonu.

Kütüphane:

- drf-spectacular
- drf-yasg

Swagger endpoint:

```
/api/schema/
/api/docs/
```

Production API’lerde zorunlu sayılır.

---

## 1️⃣6️⃣ CORS

Frontend farklı domain’den geliyorsa gerekir.

install:

```bash
pip install django-cors-headers
```

settings.py:

```python
INSTALLED_APPS = [
    "corsheaders",
]

MIDDLEWARE = [
    "corsheaders.middleware.CorsMiddleware",
]

CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",
]
```

---

## 1️⃣7️⃣ Performance Optimizasyonları

### select_related

ForeignKey için:

```python
Product.objects.select_related("category")
```

### prefetch_related

ManyToMany için:

```python
Product.objects.prefetch_related("tags")
```

---

### only / defer

```python
Product.objects.only("id", "name")
```

---

### Cache

#### Page cache

```python
from django.views.decorators.cache import cache_page

@cache_page(60)
def product_list(request):
    ...
```

#### Per-object cache

Redis ile object bazlı cache yapılır.

---

## 1️⃣8️⃣ DRF Signal, Logging, Audit

- Model save/delete signal’ları
- API request logging middleware
- Audit log modeli

Örnek audit model:

```python
class AuditLog(models.Model):
    user = models.ForeignKey(User, on_delete=models.SET_NULL, null=True)
    action = models.CharField(max_length=100)
    timestamp = models.DateTimeField(auto_now_add=True)
```

Production’da:

- Kim ne yaptı?
- Ne zaman yaptı?
- Hangi IP’den yaptı?

Loglanmalıdır.

---

## 1️⃣9️⃣ DRF Request–Response Flow

```
Request
   ↓
Authentication
   ↓
Permission
   ↓
Throttling
   ↓
View
   ↓
Serializer
   ↓
Database
   ↓
Response
```

---

## 2️⃣0️⃣ Production Best Practice Stack

Modern DRF production setup:

```
ModelSerializer
+
ModelViewSet
+
JWT Authentication
+
Custom Permissions
+
Throttling
+
CursorPagination
+
Filtering
+
Redis Cache
+
Swagger/OpenAPI
+
CORS config
```

---

# Özet

### Serializer
Model ↔ JSON dönüşümü

### ViewSet
Production CRUD standardı

### Permissions
Erişim kontrolü

### Throttling
Rate limit

### Pagination
Büyük veri yönetimi

### Filtering
Query kontrolü

### Performance
select_related + cache

Production API sadece çalışmakla kalmaz,  
ölçeklenebilir, güvenli ve gözlemlenebilir olmalıdır.