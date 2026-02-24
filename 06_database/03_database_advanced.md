# BÖLÜM 8 — DATABASE ADVANCED

İçerik:

- Indexing Internals  
- Query Optimization  
- Normalization  
- Denormalization  
- Transactions  
- ACID Properties  
- Isolation Levels  

---

# 1️⃣ Indexing Internals (Index’in İç Yapısı)

Index, database performansının en kritik bileşenidir.  
Sadece “hızlandırır” demek yeterli değildir — **nasıl çalıştığını bilmek gerekir.**

---

## 📌 Index Nedir? (Kısa Hatırlatma)

Index, database’in veriye hızlı erişmesini sağlayan özel veri yapısıdır.

Kitap örneği:

Kitap: 500 sayfa  
Index:  
Python → sayfa 320  

Direkt ilgili sayfaya gidersin.

---

## 📌 Index Olmadan Ne Olur?

```sql
SELECT * FROM users WHERE email = 'test@mail.com';
```

Database tüm tabloyu tarar.

Buna denir:

**Full Table Scan (Sequential Scan)**

Time complexity:

```
O(n)
```

1 milyon row → 1 milyon kontrol

---

## 📌 Index ile Ne Olur?

Database direkt index’e bakar.

Time complexity:

```
O(log n)
```

Çok daha hızlıdır.

---

## 📌 Index Veri Yapısı: B-Tree

Çoğu relational database index olarak **B-Tree** kullanır.

Örnek yapı:

```
        50
       /  \
     20    80
    / \    / \
   10 30  60 90
```

90’ı aramak için:

50 → sağ  
80 → sağ  
90 → bulundu  

Full scan yerine logaritmik arama yapılır.

---

## 📌 Index Gerçekte Ne Saklar?

Index şunu saklar:

```
(index_value → row_pointer)
```

Örnek:

```
salih@mail.com → row 100
ahmet@mail.com → row 450
```

Yani veri değil, verinin adresini tutar.

---

## 📌 Index Türleri

### 1️⃣ Primary Index

Primary key otomatik indexlenir.

```sql
PRIMARY KEY(id)
```

---

### 2️⃣ Unique Index

Duplicate engeller.

```sql
CREATE UNIQUE INDEX idx_email ON users(email);
```

---

### 3️⃣ Composite Index

Birden fazla column:

```sql
CREATE INDEX idx_user_email_age
ON users(email, age);
```

Önemli: Sıra kritiktir.

(email, age) index’i → sadece email filtrelerinde de çalışır  
(age, email) index’i → email-only filtrede çalışmaz

---

### 4️⃣ Partial Index

Belirli şart için:

```sql
CREATE INDEX idx_active_users
ON users(email)
WHERE is_active = true;
```

---

## 📌 Django’da Index

```python
class User(models.Model):
    email = models.EmailField(db_index=True)
```

veya:

```python
class Meta:
    indexes = [
        models.Index(fields=["email"])
    ]
```

---

## ⚠️ Index’in Dezavantajları

Index her zaman iyi değildir.

Dezavantajları:

- Disk alanı kullanır  
- INSERT yavaşlar  
- UPDATE yavaşlar  
- DELETE yavaşlar  

Çünkü index de güncellenir.

---

# 2️⃣ Query Optimization

Amaç:

Query’leri minimum CPU, minimum disk, minimum süre ile çalıştırmak.

---

## 📌 Database Query Nasıl Çalıştırır?

Örnek:

```sql
SELECT * FROM users WHERE email = 'test@mail.com';
```

Database:

1. Execution plan oluşturur  
2. Index var mı kontrol eder  
3. En hızlı yolu seçer  

---

## 📌 Execution Plan

```sql
EXPLAIN SELECT * FROM users WHERE email='test@mail.com';
```

Olası çıktılar:

- Index Scan → hızlı  
- Sequential Scan → yavaş  

---

### Sequential Scan

Tüm tabloyu okur.  
Büyük tabloda pahalıdır.

---

### Index Scan

Index kullanır.  
Genelde çok daha hızlıdır.

---

## 📌 Query Optimization Teknikleri

### 1️⃣ Index Kullanmak

En önemli optimizasyon.

---

### 2️⃣ SELECT * Kullanma

Kötü:

```sql
SELECT * FROM users;
```

İyi:

```sql
SELECT id, email FROM users;
```

Daha az data → daha az I/O → daha hızlı

---

### 3️⃣ LIMIT Kullanmak

Kötü:

```sql
SELECT * FROM orders;
```

İyi:

```sql
SELECT * FROM orders LIMIT 10;
```

---

### 4️⃣ JOIN Optimizasyonu

JOIN yapılan kolonlarda index yoksa performans düşer.

---

## 📌 Django Query Optimization

### ❌ Kötü (N+1 Problem)

```python
users = User.objects.all()

for user in users:
    print(user.orders.all())
```

Her user için ayrı query çalışır.

---

### ✅ İyi

```python
User.objects.prefetch_related("orders")
```

veya

```python
User.objects.select_related("profile")
```

---

# 3️⃣ Normalization

Amaç:

Veri tekrarını azaltmak.

---

## ❌ Kötü Tasarım

Orders:

| id | user_name |
|----|------------|
| 1  | Salih      |
| 2  | Salih      |

Duplicate veri.

---

## ✅ İyi Tasarım

Users:

| id | name  |
|----|--------|
| 1  | Salih  |

Orders:

| id | user_id |
|----|----------|
| 1  | 1        |

---

## 📌 Avantajları

- Veri tutarlılığı  
- Daha az storage  
- Update kolaylığı  

---

## 📌 Normal Forms

### 1NF
Atomic data (bölünemez veri)

### 2NF
Partial dependency yok

### 3NF
Transitive dependency yok

Production’da genelde **3NF** kullanılır.

---

# 4️⃣ Denormalization

Performans için bilinçli duplicate veri ekleme.

Amaç:

JOIN sayısını azaltmak.

---

## Örnek

Orders:

| id | user_id | user_name |
|----|----------|-----------|
| 1  | 1        | Salih     |

JOIN gerekmez.

---

## Trade-off

✔ Daha hızlı read  
❌ Daha zor update  
❌ Veri tutarsızlığı riski  

---

## Production’da Kullanım Alanı

- Analytics  
- Reporting  
- Cache tables  
- Read-heavy sistemler  

---

# 5️⃣ Transactions

Transaction:

Bir grup database işleminin tek bir bütün olarak çalışmasıdır.

Ya hep ya hiç.

---

## Örnek (Para Transferi)

```sql
BEGIN;

UPDATE users SET balance=balance-100 WHERE id=1;
UPDATE users SET balance=balance+100 WHERE id=2;

COMMIT;
```

Fail:

```sql
ROLLBACK;
```

---

## Django

```python
from django.db import transaction

with transaction.atomic():
    sender.balance -= 100
    sender.save()

    receiver.balance += 100
    receiver.save()
```

---

# 6️⃣ ACID Properties

Transaction garantileri:

### Atomicity
Ya hep ya hiç.

### Consistency
Database her zaman valid state’te kalır.

### Isolation
Transaction’lar birbirini bozmaz.

### Durability
Commit sonrası veri kalıcıdır.

Server crash olsa bile veri kaybolmaz.

---

# 7️⃣ Isolation Levels

Transaction’ların birbirini nasıl gördüğünü belirler.

---

## 1️⃣ Read Uncommitted

Dirty read olabilir.  
En zayıf seviyedir.

---

## 2️⃣ Read Committed (PostgreSQL default)

Sadece commit edilmiş veri okunur.  
En yaygın kullanılan seviye.

---

## 3️⃣ Repeatable Read

Transaction boyunca okunan veri değişmez.

---

## 4️⃣ Serializable

En güvenli.  
Ama en yavaş.

---

## 📌 Olası Problemler

### Dirty Read
Commit edilmemiş veri okunur.

### Non-repeatable Read
Aynı query farklı sonuç verir.

### Phantom Read
Yeni row ortaya çıkar.

---

## Django Default Isolation Level

```
READ COMMITTED
```

---

# 🔥 Production Gerçekleri (Senior Seviyede Kritik)

En önemli gerçekler:

- Index performansın temelidir  
- Index yoksa büyük tabloda query yavaştır  
- Query optimization production için kritiktir  
- Transaction veri tutarlılığı sağlar  
- ACID database güvenliğinin temelidir  
- Isolation level concurrency davranışını belirler  
- Normalization veri tutarlılığı sağlar  
- Denormalization performans trade-off’tur  
- Execution plan performansı belirler  
- Database performansı çoğunlukla I/O bound’dur  

---

# 📌 Django Production Örneği

### ✅ Doğru

```python
User.objects.select_related("profile").get(id=1)
```

### ❌ Yanlış

```python
User.objects.get(id=1).profile
```

---

# 📌 Genel Özet

Index → hızlı erişim  
Query Optimization → performans  
Normalization → veri tutarlılığı  
Denormalization → performans trade-off  
Transaction → güvenli işlem  
ACID → garanti sistemi  
Isolation Level → concurrency kontrol  