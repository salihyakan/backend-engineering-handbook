# BÖLÜM 10 — DJANGO ORM

Bu bölümde şunları derinlemesine öğreneceksin:

- ORM nedir
- Model nedir
- QuerySet nedir
- Lazy evaluation nedir
- get vs filter farkı
- exclude nedir
- annotate nedir
- aggregate nedir

Bu konular Django’da database ile **profesyonel çalışmayı** belirler.

---

## 1️⃣ ORM Nedir?

**ORM = Object Relational Mapping**

### Tanım

Database tablolarını Python objeleri olarak kullanmanı sağlar.

Yani:

SQL yazmadan database ile konuşursun.

---

### SQL vs ORM Karşılaştırması

**SQL:**

```sql
SELECT * FROM product WHERE price > 100;
```

**Django ORM:**

```python
Product.objects.filter(price__gt=100)
```

ORM şunu yapar:

```
Python kodu → SQL'e çevirir
```

Sen SQL yazmazsın.

---

### ORM Neden Kullanılır?

Avantajları:

- SQL yazmadan database kullanırsın
- Daha güvenli (SQL injection koruması)
- Daha temiz kod
- Database bağımsız (PostgreSQL, MySQL, SQLite)

---

## 2️⃣ Model Nedir?

Model = Database tablosunun Python karşılığı

### Örnek:

```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=255)
    price = models.FloatField()
```

Bu model şu tabloyu oluşturur:

```
product table

id | name | price
```

### Model instance = row

Database row:

```
id=1
name="Laptop"
price=5000
```

Python karşılığı:

```python
product = Product.objects.get(id=1)
print(product.name)
```

---

## 3️⃣ Model Manager Nedir?

`objects` = manager

```python
Product.objects
```

Database operasyonlarını yapar.

Örnek:

```python
Product.objects.all()
Product.objects.filter()
Product.objects.get()
```

---

## 4️⃣ QuerySet Nedir?

QuerySet = Database query sonucu dönen object listesi

```python
products = Product.objects.all()
```

QuerySet türü:

```
<QuerySet [Product, Product, Product]>
```

Liste gibi davranır:

```python
for product in products:
    print(product.name)
```

### SQL karşılığı

```python
Product.objects.all()
```

```sql
SELECT * FROM product;
```

---

## 5️⃣ Lazy Evaluation (Çok Kritik)

Django ORM **lazy çalışır**.

Yani query hemen çalışmaz.

```python
products = Product.objects.all()
```

Bu anda SQL çalışmaz.

### SQL ne zaman çalışır?

Veriye eriştiğinde:

```python
for product in products:
    print(product.name)
```

Şimdi SQL çalışır.

Başka örnek:

```python
products = Product.objects.filter(price__gt=100)
```

SQL çalışmaz.

Ama:

```python
list(products)
```

SQL çalışır.

### Avantajı

- Performans optimizasyonu
- Gereksiz query çalışmaz

---

## 6️⃣ get()

Tek bir object getirir.

```python
product = Product.objects.get(id=1)
```

SQL:

```sql
SELECT * FROM product WHERE id=1;
```

### Dönen:

- Product object

### Hata durumları:

- Bulamazsa → `Product.DoesNotExist`
- Birden fazla varsa → `MultipleObjectsReturned`

`get()` sadece **tek kayıt** için kullanılır.

---

## 7️⃣ filter()

Bir veya birden fazla object getirir.

```python
products = Product.objects.filter(price__gt=100)
```

SQL:

```sql
SELECT * FROM product WHERE price > 100;
```

### Dönen:

- QuerySet

`filter()` hata vermez.

Boşsa:

```
<QuerySet []>
```

---

### get vs filter farkı

| get | filter |
|------|--------|
| Tek object döner | QuerySet döner |
| Hata verebilir | Hata vermez |
| Unique kayıt için | Liste sonuçlar için |

---

## 8️⃣ exclude()

Belirli kayıtları hariç tutar.

```python
products = Product.objects.exclude(price__gt=100)
```

SQL:

```sql
SELECT * FROM product WHERE NOT price > 100;
```

---

## 9️⃣ Lookup Expressions (Çok Önemli)

Filtreleme operatörleri:

- exact
- gt
- gte
- lt
- lte
- contains
- icontains
- startswith
- endswith
- in

### Örnekler:

```python
Product.objects.filter(price__gt=100)
Product.objects.filter(name__contains="Lap")
Product.objects.filter(id__in=[1,2,3])
```

---

## 🔟 Query Chaining

Query’ler zincirlenebilir:

```python
Product.objects.filter(price__gt=100).exclude(name="Phone")
```

SQL:

```sql
SELECT * FROM product
WHERE price > 100 AND name != "Phone";
```

---

## 11️⃣ create()

Yeni kayıt oluşturur:

```python
Product.objects.create(
    name="Laptop",
    price=5000
)
```

SQL:

```sql
INSERT INTO product (name, price) VALUES ("Laptop", 5000);
```

---

## 12️⃣ save()

```python
product = Product(name="Phone", price=2000)
product.save()
```

---

## 13️⃣ update()

```python
Product.objects.filter(id=1).update(price=3000)
```

SQL:

```sql
UPDATE product SET price=3000 WHERE id=1;
```

---

## 14️⃣ delete()

```python
Product.objects.filter(id=1).delete()
```

SQL:

```sql
DELETE FROM product WHERE id=1;
```

---

## 15️⃣ aggregate()

Summary hesaplar.

```python
from django.db.models import Avg

avg_price = Product.objects.aggregate(Avg("price"))
```

Sonuç:

```python
{'price__avg': 2500}
```

SQL:

```sql
SELECT AVG(price) FROM product;
```

Diğerleri:

- Count
- Sum
- Max
- Min

---

## 16️⃣ annotate()

Her row için hesaplama yapar.

```python
from django.db.models import Count

Category.objects.annotate(product_count=Count("product"))
```

Her category için product sayısı ekler.

SQL:

```sql
SELECT category.*, COUNT(product.id)
FROM category
LEFT JOIN product
GROUP BY category.id;
```

---

### annotate vs aggregate farkı

| aggregate | annotate |
|-----------|----------|
| Tek sonuç döner | Her row için sonuç döner |
| Summary | Row-level hesaplama |

---

## 17️⃣ exists()

```python
Product.objects.filter(id=1).exists()
```

True / False döner.

---

## 18️⃣ first() ve last()

```python
Product.objects.first()
Product.objects.last()
```

---

## 19️⃣ order_by()

```python
Product.objects.order_by("price")
Product.objects.order_by("-price")
```

---

## 20️⃣ values()

Dictionary döner:

```python
Product.objects.values("name", "price")
```

Sonuç:

```python
[
 {'name': 'Laptop', 'price': 5000}
]
```

---

## 21️⃣ values_list()

Tuple döner:

```python
Product.objects.values_list("name", "price")
```

---

## 22️⃣ select_related() (Çok Kritik Performance)

ForeignKey için JOIN optimization yapar.

---

## 23️⃣ prefetch_related()

ManyToMany için optimization yapar.

---

# 🎯 Özet (Mülakat İçin Kritik)

**ORM:**  
Python ile database yönetmeyi sağlar.

**Model:**  
Database tablosu.

**QuerySet:**  
Query sonucu dönen lazy object collection.

**Lazy evaluation:**  
Query sadece ihtiyaç olduğunda çalışır.

**get():**  
Tek kayıt döner.

**filter():**  
Birden fazla kayıt döner (QuerySet).

**aggregate():**  
Summary hesaplar.

**annotate():**  
Row-level hesaplama yapar.

---

# 🎤 Mülakat Soruları

### Soru:
QuerySet nedir?

### Cevap:
Database query sonucu dönen lazy object collection’dır.

---

### Soru:
Lazy evaluation nedir?

### Cevap:
Query sadece ihtiyaç olduğunda çalışır.

---

### Soru:
get ve filter farkı nedir?

### Cevap:
get tek object döner ve hata verebilir.  
filter QuerySet döner ve hata vermez.