# SQL Nedir? (Django + Production Seviyesi Anlatım)

## 1️⃣ SQL Nedir?

### Tanım

SQL (Structured Query Language), relational database’ler ile iletişim kurmak için kullanılan sorgu dilidir.

SQL ile şunları yaparsın:

- Veri ekleme
- Veri okuma
- Veri güncelleme
- Veri silme
- Tablo oluşturma
- Index ve constraint tanımlama

Django ORM gerçekte SQL üretir.

Örnek:

Django kodu:

```python
User.objects.get(id=1)
```

Arkada çalışan SQL:

```sql
SELECT * FROM users WHERE id = 1;
```

ORM, SQL’in üst soyutlama katmanıdır.  
Ama production seviyede SQL bilmek zorunludur.

---

## 2️⃣ Temel SQL Komut Türleri (CRUD)

SQL komutları 4 ana gruba ayrılır:

| Tür     | Amaç             |
|---------|------------------|
| SELECT  | Veri okuma       |
| INSERT  | Veri ekleme      |
| UPDATE  | Veri güncelleme  |
| DELETE  | Veri silme       |

Buna CRUD denir:

- Create → INSERT  
- Read → SELECT  
- Update → UPDATE  
- Delete → DELETE  

---

## 3️⃣ SELECT — Veri Okuma

En önemli SQL komutudur.

Syntax:

```sql
SELECT column FROM table;
```

Örnek:

```sql
SELECT * FROM users;
```

Anlamı:  
users tablosundaki tüm verileri getir.

Belirli kolon seçme:

```sql
SELECT name, email FROM users;
```

Django karşılığı:

```python
User.objects.all()
```

---

## 4️⃣ WHERE — Filtreleme

Belirli verileri seçmek için kullanılır.

Örnek:

```sql
SELECT * FROM users WHERE id = 1;
```

Django karşılığı:

```python
User.objects.filter(id=1)
```

Birden fazla koşul:

```sql
SELECT * FROM users
WHERE age > 18 AND country = 'Turkey';
```

---

## 5️⃣ INSERT — Veri Ekleme

Syntax:

```sql
INSERT INTO table (columns)
VALUES (values);
```

Örnek:

```sql
INSERT INTO users (name, email)
VALUES ('Salih', 'salih@mail.com');
```

Django karşılığı:

```python
User.objects.create(
    name="Salih",
    email="salih@mail.com"
)
```

---

## 6️⃣ UPDATE — Veri Güncelleme

Syntax:

```sql
UPDATE table
SET column = value
WHERE condition;
```

Örnek:

```sql
UPDATE users
SET name = 'Ahmet'
WHERE id = 1;
```

Django karşılığı:

```python
user = User.objects.get(id=1)
user.name = "Ahmet"
user.save()
```

⚠️ WHERE yazmazsan tüm tablo güncellenir.

---

## 7️⃣ DELETE — Veri Silme

Syntax:

```sql
DELETE FROM table WHERE condition;
```

Örnek:

```sql
DELETE FROM users WHERE id = 1;
```

Django karşılığı:

```python
User.objects.get(id=1).delete()
```

⚠️ WHERE olmadan DELETE → tüm tablo silinir.

---

## 8️⃣ ORDER BY — Sıralama

Verileri sıralamak için kullanılır.

```sql
SELECT * FROM users
ORDER BY age DESC;
```

- DESC → büyükten küçüğe  
- ASC → küçükten büyüğe  

Django karşılığı:

```python
User.objects.order_by("-age")
```

---

## 9️⃣ LIMIT — Sonuç Sayısını Sınırla

```sql
SELECT * FROM users LIMIT 10;
```

Django karşılığı:

```python
User.objects.all()[:10]
```

Production’da:

- Pagination
- Infinite scroll
- Dashboard listeleri

için kritiktir.

---

## 🔟 COUNT — Sayma

```sql
SELECT COUNT(*) FROM users;
```

Django karşılığı:

```python
User.objects.count()
```

---

## 1️⃣1️⃣ JOIN — En Kritik Konu

JOIN tabloları birleştirir.  
Relational database’in kalbidir.

Users:

| id | name  |
|----|-------|
| 1  | Salih |

Orders:

| id | user_id |
|----|----------|
| 1  | 1        |

JOIN sorgusu:

```sql
SELECT users.name, orders.id
FROM users
JOIN orders
ON users.id = orders.user_id;
```

Django karşılığı:

```python
Order.objects.select_related("user")
```

### JOIN Türleri

INNER JOIN — sadece eşleşenleri getirir:

```sql
SELECT *
FROM users
INNER JOIN orders
ON users.id = orders.user_id;
```

LEFT JOIN — sol tablodaki her şeyi getirir:

```sql
SELECT *
FROM users
LEFT JOIN orders
ON users.id = orders.user_id;
```

---

## 1️⃣2️⃣ GROUP BY

Verileri gruplar.

Örnek: Her user’ın order sayısı:

```sql
SELECT user_id, COUNT(*)
FROM orders
GROUP BY user_id;
```

Django karşılığı:

```python
from django.db.models import Count

Order.objects.values("user").annotate(count=Count("id"))
```

---

## 1️⃣3️⃣ DISTINCT

Tekrarlayan verileri kaldırır.

```sql
SELECT DISTINCT country FROM users;
```

---

## 1️⃣4️⃣ Gerçek Production SQL Örneği

Son 10 sipariş:

```sql
SELECT *
FROM orders
ORDER BY created_at DESC
LIMIT 10;
```

User ve order birlikte:

```sql
SELECT users.name, orders.total
FROM users
JOIN orders
ON users.id = orders.user_id;
```

---

## 1️⃣5️⃣ Django ORM → SQL Mapping

Django:

```python
User.objects.filter(age__gt=18)
```

SQL:

```sql
SELECT * FROM users WHERE age > 18;
```

Django:

```python
User.objects.get(id=1)
```

SQL:

```sql
SELECT * FROM users WHERE id = 1;
```

---

## 1️⃣6️⃣ SQL Execution Mantığı (Çok Kritik)

SQL mantıksal olarak şu sırayla çalışır:

1. FROM  
2. JOIN  
3. WHERE  
4. GROUP BY  
5. ORDER BY  
6. LIMIT  
7. SELECT  

Yani önce tablo seçilir, sonra filtre uygulanır.

---

## 1️⃣7️⃣ Production'da En Önemli SQL Gerçekleri

En kritik performans faktörleri:

- Index kullanımı
- JOIN optimizasyonu
- WHERE filtering
- LIMIT kullanımı
- Gereksiz SELECT * kullanmamak

Kötü yazılmış SQL → yavaş sistem  
Optimize edilmiş SQL → yüksek performans

---

## 1️⃣8️⃣ Neden Django Geliştiricisi SQL Bilmek Zorunda?

Çünkü:

- ORM her zaman optimal query üretmez.
- N+1 query problemi oluşabilir.
- Büyük veride performans düşebilir.

Debug için:

```python
print(queryset.query)
```

Örnek:

```python
User.objects.filter(name="Salih")
```

Üretilen SQL:

```sql
SELECT * FROM users WHERE name = 'Salih';
```

---

# 📌 Bölüm Özeti

SQL ile:

- SELECT → veri okursun  
- INSERT → veri eklersin  
- UPDATE → veri güncellersin  
- DELETE → veri silersin  
- JOIN → tabloları birleştirirsin  
- GROUP BY → gruplarsın  
- ORDER BY → sıralarsın  
- LIMIT → sonucu sınırlandırırsın  

Django geliştiricisi için SQL bilmek opsiyon değil, zorunluluktur.