# Database Temelleri (Python & Django Perspektifi)

Bu doküman şunları net ve production odaklı şekilde açıklar:

- Database nedir?
- Relational database nedir?
- Table, Row, Column
- Primary Key
- Foreign Key
- Index
- Constraint
- Django ORM ile database ilişkisi

---

# 1️⃣ Database Nedir?

## 📌 Tanım

**Database (Veritabanı)**, verileri organize eden, saklayan, yöneten ve sorgulayan sistemdir.

Basitçe:

> Database = Kalıcı veri saklama sistemidir.

---

## 🌍 Gerçek Dünya Örneği (E-Ticaret)

Bir e-ticaret sisteminde database şunları tutar:

- users
- products
- orders
- payments
- reviews

---

## Örnek Veri

### User

| id | name  | email           |
|----|-------|----------------|
| 1  | Salih | salih@mail.com |

### Product

| id | name   | price |
|----|--------|-------|
| 1  | Laptop | 50000 |

---

## ❓ Database Neden Gereklidir?

Çünkü RAM geçicidir.

### RAM
- Uygulama kapanınca veri silinir.

### Database
- Kalıcıdır.
- Disk üzerinde tutulur.
- Restart sonrası veri kaybolmaz.

---

## 🔥 Popüler Database Sistemleri

### Relational (SQL)
- PostgreSQL
- MySQL
- SQLite

### Non-Relational (NoSQL)
- MongoDB
- Redis

---

## 🎯 Django Production’da

En çok kullanılan:

> PostgreSQL

---

# 2️⃣ Relational Database Nedir?

## 📌 Tanım

**Relational database**, verilerin tablolar ve ilişkiler halinde tutulduğu sistemdir.

Relational = İlişkisel

---

## Mantık

- Veriler tablolarda tutulur.
- Tablolar birbirine bağlanır.
- İlişkiler foreign key ile kurulur.

---

## Örnek

### Users

| id | name  |
|----|-------|
| 1  | Salih |
| 2  | Ahmet |

### Orders

| id | user_id | total |
|----|----------|-------|
| 1  | 1        | 500   |
| 2  | 2        | 1000  |

`user_id` → users tablosuna bağlıdır.

Bu relational database’tir.

---

## 🔗 İlişki Türleri

### One to One
User → Profile

### One to Many
User → Orders

### Many to Many
Product ↔ Category

---

## Django Karşılığı

```python
class User(models.Model):
    name = models.CharField(max_length=100)


class Order(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
```

---

# 3️⃣ Table Nedir?

## 📌 Tanım

**Table**, verilerin tutulduğu ana yapıdır.

Excel tablosu gibi düşünebilirsin.

---

### Örnek

User table:

| id | name | email |

Her table:

- Rows (satırlar)
- Columns (sütunlar)

içerir.

---

## Django Karşılığı

```python
class User(models.Model):
    name = models.CharField(max_length=100)
```

Bu model bir table oluşturur.

---

# 4️⃣ Row Nedir?

## 📌 Tanım

**Row**, tablodaki tek bir veri kaydıdır.

---

### Örnek

| id | name  |
|----|-------|
| 1  | Salih |
| 2  | Ahmet |

Her satır bir row’dur.

---

## Django Karşılığı

```python
User.objects.create(name="Salih")
```

Bu bir row oluşturur.

---

# 5️⃣ Column Nedir?

## 📌 Tanım

**Column**, tablodaki veri alanıdır.

---

### Örnek

| id | name | email |

- id → column
- name → column
- email → column

---

## Django Karşılığı

```python
name = models.CharField(max_length=100)
```

---

# 6️⃣ Primary Key Nedir?

## 📌 Tanım

**Primary Key**, her row’u benzersiz tanımlayan alandır.

---

## Özellikleri

- Unique olmak zorundadır
- Null olamaz
- Her tabloda yalnızca 1 tane olabilir

---

## Örnek

| id | name  |
|----|-------|
| 1  | Salih |
| 2  | Ahmet |

`id` primary key’dir.

---

## Django Otomatik Oluşturur

```python
id = models.AutoField(primary_key=True)
```

Eğer yazmazsan Django otomatik ekler.

---

## Production Gerçekleri

Primary key genelde:

- Integer (Auto Increment)
- UUID

---

## UUID Örneği

```python
import uuid

id = models.UUIDField(primary_key=True, default=uuid.uuid4)
```

---

# 7️⃣ Foreign Key Nedir?

## 📌 Tanım

**Foreign Key**, bir tablonun başka bir tabloya referans vermesidir.

İlişki kurar.

---

## Örnek

### Users

| id | name  |
|----|-------|
| 1  | Salih |

### Orders

| id | user_id |
|----|----------|
| 1  | 1        |

`user_id` → Users tablosuna referans verir.

---

## Django Karşılığı

```python
class Order(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
```

---

## on_delete Seçenekleri

- CASCADE → Parent silinirse child da silinir
- SET_NULL → Parent silinirse null olur
- PROTECT → Silinmesini engeller
- SET_DEFAULT → Default değere çeker

---

# 8️⃣ Index Nedir?

## 📌 Tanım

**Index**, database sorgularını hızlandıran veri yapısıdır.

Kitap index’i gibi çalışır.

---

## Index Olmadan

Database tüm table’ı tarar:

O(n)

---

## Index ile

Binary search benzeri yapı:

O(log n)

---

## Örnek

1 milyon user var.

```sql
SELECT * FROM users WHERE email = 'test@mail.com';
```

- Index yoksa → 1 milyon satır taranır
- Index varsa → direkt bulunur

---

## Django Index

```python
email = models.EmailField(db_index=True)
```

veya

```python
class Meta:
    indexes = [
        models.Index(fields=['email'])
    ]
```

---

## ⚠️ Production Gerçeği

Index:

- Read performansını artırır
- Write performansını düşürebilir
- Fazla index → yavaş insert/update

Denge gerekir.

---

# 9️⃣ Constraint Nedir?

## 📌 Tanım

**Constraint**, database kurallarıdır.

Veri bütünlüğünü sağlar.

---

## Türleri

### PRIMARY KEY
Benzersiz olmalı

### UNIQUE
Aynı veri tekrar edemez

```python
email = models.EmailField(unique=True)
```

### NOT NULL
Boş olamaz

```python
name = models.CharField(null=False)
```

### FOREIGN KEY
Referans kontrolü yapar

### CHECK
Belirli şart koyar

Örnek:

age > 18

---

## Gerçek Django Örneği

```python
class User(models.Model):

    id = models.AutoField(primary_key=True)
    email = models.EmailField(unique=True)
    name = models.CharField(max_length=100)
    age = models.IntegerField()
```

---

# Database Yapısının Mantığı

```
Users
id | name
1  | Salih

Orders
id | user_id
1  | 1
```

- Foreign key → ilişki
- Index → hızlı arama
- Constraint → veri güvenliği

---

# Django ORM → Database Mapping

Django modeli:

```python
class User(models.Model):
    name = models.CharField(max_length=100)
```

Database karşılığı (SQL):

```sql
CREATE TABLE user (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100)
);
```

ORM = Object Relational Mapping

Python class ↔ Database table eşleştirmesi yapar.

---

# 🔥 Production Seviyesinde Kritik Gerçekler

En kritik 5 nokta:

1. Primary key her tabloda vardır.
2. Foreign key ilişkileri sağlar.
3. Index performans için kritiktir.
4. Constraint veri bütünlüğünü sağlar.
5. Database disk tabanlıdır.

---

# 🎯 Senior Seviyede Kritik Production Gerçeği

> Database performansının büyük kısmı doğru index kullanımına bağlıdır.

- Kötü index → yavaş sistem
- Doğru index → hızlı sistem
- Gereksiz index → write performansı düşer

---

# 📌 Bölüm Özeti

- Database = veri saklama sistemi
- Table = veri tablosu
- Row = veri satırı
- Column = veri alanı
- Primary key = benzersiz kimlik
- Foreign key = ilişki
- Index = hızlandırıcı
- Constraint = veri kuralı

---

Bu doküman Django backend mülakat ve production seviyesi için temel database bilgisini netleştirmek amacıyla hazırlanmıştır.