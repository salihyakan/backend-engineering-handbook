# BÖLÜM 11 — DJANGO MODELS

Bu bölümde şunları net şekilde öğreneceksin:

- Field types
- ForeignKey
- OneToOneField
- ManyToManyField
- Migrations
- Signals

Bu bölüm mülakatlarda çok sorulur ve production projelerde en çok hata yapılan yerdir.

---

# 1️⃣ Model Nedir? (Kısa Hatırlatma)

Model = Database tablosunun Python temsili.

```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=255)
    price = models.DecimalField(max_digits=10, decimal_places=2)
```

Bu kod şu tabloyu üretir:

```
product table

id | name | price
```

Her model otomatik olarak `id` (AutoField / BigAutoField) üretir.

---

# 2️⃣ Field Types (Alan Türleri)

Field = Column karşılığıdır.

## 🔹 CharField

Kısa metinler için.

```python
name = models.CharField(max_length=255)
```

- `max_length` zorunludur.
- SQL karşılığı: VARCHAR

---

## 🔹 TextField

Uzun metinler için.

```python
description = models.TextField()
```

- SQL karşılığı: TEXT

---

## 🔹 IntegerField

```python
stock = models.IntegerField()
```

---

## 🔹 DecimalField (ÇOK ÖNEMLİ)

Para işlemleri için kullanılır.

```python
price = models.DecimalField(max_digits=10, decimal_places=2)
```

- `max_digits` → toplam basamak sayısı
- `decimal_places` → virgülden sonraki basamak

⚠ Para işlemlerinde **FloatField kullanılmaz** (precision hatası).

---

## 🔹 FloatField

Ondalıklı sayılar için ama finansal işlemler için önerilmez.

---

## 🔹 BooleanField

```python
is_active = models.BooleanField(default=True)
```

---

## 🔹 DateField / DateTimeField

```python
created_at = models.DateTimeField(auto_now_add=True)
updated_at = models.DateTimeField(auto_now=True)
```

- `auto_now_add` → sadece oluşturulurken set edilir
- `auto_now` → her save işleminde güncellenir

---

## 🔹 EmailField

Validation içerir.

```python
email = models.EmailField()
```

---

## 🔹 FileField / ImageField

```python
image = models.ImageField(upload_to="products/")
```

Gereken ayar:

```python
MEDIA_ROOT
MEDIA_URL
```

---

# 3️⃣ Field Options (Çok Önemli)

Field’lara ek davranış kazandırır.

---

## 🔹 null vs blank (Mülakat Klasiği)

```python
name = models.CharField(max_length=100, null=True, blank=True)
```

- `null` → database seviyesi (NULL olabilir mi?)
- `blank` → form validation seviyesi

Önemli:

- CharField ve TextField için genelde `null=True` kullanılmaz.
- Boş değer için empty string tercih edilir.

---

## 🔹 default

```python
is_active = models.BooleanField(default=True)
```

---

## 🔹 unique

```python
email = models.EmailField(unique=True)
```

Database seviyesinde UNIQUE constraint oluşturur.

---

## 🔹 db_index

```python
name = models.CharField(max_length=100, db_index=True)
```

Index oluşturur → sorgu performansını artırır.

---

# 4️⃣ ForeignKey (Many-to-One)

Bir ürünün bir kategorisi vardır.  
Bir kategorinin birçok ürünü olabilir.

```python
class Category(models.Model):
    name = models.CharField(max_length=100)

class Product(models.Model):
    name = models.CharField(max_length=100)
    category = models.ForeignKey(Category, on_delete=models.CASCADE)
```

---

## 🔹 on_delete (Zorunlu)

### CASCADE

Kategori silinirse ürünler de silinir.

### SET_NULL

```python
category = models.ForeignKey(
    Category,
    on_delete=models.SET_NULL,
    null=True
)
```

Kategori silinirse NULL olur.

### PROTECT

Silinmesini engeller.

---

## 🔹 Reverse Relation

Varsayılan:

```python
category.product_set.all()
```

Daha temiz kullanım:

```python
category = models.ForeignKey(
    Category,
    on_delete=models.CASCADE,
    related_name="products"
)
```

Artık:

```python
category.products.all()
```

---

# 5️⃣ OneToOneField (One-to-One)

Her kullanıcıya bir profil.

```python
from django.contrib.auth.models import User

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField()
```

Her User için bir Profile.

### Nerede kullanılır?

- User extension
- Detay modeli
- Ayrı tabloda ek bilgiler

---

# 6️⃣ ManyToManyField (Many-to-Many)

Bir ürün birden fazla etiket alabilir.  
Bir etiket birden fazla ürüne ait olabilir.

```python
class Tag(models.Model):
    name = models.CharField(max_length=50)

class Product(models.Model):
    name = models.CharField(max_length=100)
    tags = models.ManyToManyField(Tag)
```

Django arka planda otomatik ara tablo oluşturur.

### Kullanım:

```python
product.tags.add(tag)
product.tags.remove(tag)
product.tags.all()
```

---

# 7️⃣ Through Table (Advanced)

Ara tabloya ekstra alan eklemek için:

```python
class ProductTag(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    tag = models.ForeignKey(Tag, on_delete=models.CASCADE)
    added_at = models.DateTimeField(auto_now_add=True)
```

ManyToManyField içinde:

```python
tags = models.ManyToManyField(Tag, through="ProductTag")
```

---

# 8️⃣ Migrations (Çok Kritik)

Model değişikliklerini database’e uygular.

### Komutlar:

```bash
python manage.py makemigrations
python manage.py migrate
```

- `makemigrations` → migration dosyası üretir
- `migrate` → database’e uygular

Migration dosyaları aslında SQL üretir.

Önemli:

Migration dosyaları **version control’e eklenmelidir.**

---

# 9️⃣ Signals

Signal = Bir olay olduğunda otomatik çalışan kod.

En sık kullanılanlar:

- post_save
- pre_save
- post_delete

---

## Örnek: User oluşturulunca Profile oluştur

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

@receiver(post_save, sender=User)
def create_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)
```

⚠ Dikkat:

- Signal’ler gizli side-effect üretir.
- Abartılı kullanım anti-pattern olur.
- Genelde services katmanı tercih edilir.

---

# 🔟 Meta Class (Profesyonel Seviye)

```python
class Product(models.Model):
    name = models.CharField(max_length=100)

    class Meta:
        ordering = ["-id"]
        verbose_name = "Product"
        verbose_name_plural = "Products"
```

---

## 🔹 db_table

```python
class Meta:
    db_table = "custom_product_table"
```

---

# 11️⃣ __str__ Method (Çok Önemli)

Admin panel için gereklidir.

```python
def __str__(self):
    return self.name
```

---

# 12️⃣ Model Validation

```python
from django.core.exceptions import ValidationError

def clean(self):
    if self.price < 0:
        raise ValidationError("Price cannot be negative")
```

`full_clean()` çağrıldığında çalışır.

---

# 13️⃣ Abstract Model

Ortak alanları paylaşmak için:

```python
class BaseModel(models.Model):
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        abstract = True
```

Bu model tablo oluşturmaz.  
Başka modeller miras alır.

---

# 🎤 14️⃣ Mülakat Soruları

### Soru:
null ile blank farkı nedir?

### Cevap:
null database seviyesidir, blank validation seviyesidir.

---

### Soru:
ForeignKey ve ManyToMany farkı?

### Cevap:
ForeignKey many-to-one ilişkidir.  
ManyToMany many-to-many ilişkidir.

---

### Soru:
Migration nedir?

### Cevap:
Model değişikliklerini version kontrollü şekilde database’e uygulama mekanizmasıdır.

---

### Soru:
Signal nedir?

### Cevap:
Model lifecycle olaylarını yakalayan event mekanizmasıdır.

---

# 🎯 Özet

Model = tablo  
Field = kolon  

ForeignKey = many-to-one  
OneToOne = one-to-one  
ManyToMany = many-to-many  

Migration = schema versiyonlama  
Signal = event-driven otomasyon