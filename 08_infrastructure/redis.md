# BÖLÜM 15 — REDIS

## 1️⃣ Redis Nedir?

### Tanım

Redis (Remote Dictionary Server), RAM üzerinde çalışan, çok hızlı bir in-memory data store’dur.

### Redis Kullanım Alanları

- Cache
- Session storage
- Queue
- Real-time analytics
- Pub/Sub messaging
- Rate limiting

---

## Redis Neden Hızlıdır?

Çünkü disk yerine RAM kullanır.

Yaklaşık gecikme süreleri:

- Disk: ~1–10 ms  
- RAM: ~0.0001 ms (0.1 microsecond)

Yaklaşık karşılaştırma:

- Disk → 1 ms  
- RAM → 0.1 microsecond  

Yani Redis, disk tabanlı sistemlere göre:

> 1000x – 10000x daha hızlı olabilir.

---

## Redis vs Database (PostgreSQL)

| Özellik      | Redis        | PostgreSQL |
|-------------|-------------|------------|
| Storage     | RAM         | Disk       |
| Speed       | Çok hızlı   | Daha yavaş |
| Persistence | Opsiyonel   | Kalıcı     |
| Use case    | Cache, Queue| Kalıcı veri |

---

## Redis Nasıl Çalışır?

Redis key-value mantığında çalışır:

```
key → value
```

Örnek:

```
"user:1:name" → "Ahmet"
"user:1:age"  → 25
```

---

## Redis Python Örneği

```python
import redis

r = redis.Redis(host="localhost", port=6379, db=0)

r.set("name", "Ahmet")
print(r.get("name"))
```

Çıktı:

```
b'Ahmet'
```

Not: Redis byte döner, decode edilmesi gerekir.

---

# 2️⃣ Caching Nedir?

### Tanım

Caching, sık kullanılan veriyi hızlı erişim için geçici olarak saklamaktır.

### Amaç

- Database yükünü azaltmak
- Response time düşürmek
- Performansı artırmak

---

## Cache Olmadan Sistem

```
User request
   ↓
Application
   ↓
Database (slow)
   ↓
Response
```

## Cache ile Sistem

```
User request
   ↓
Application
   ↓
Cache (Redis) ← fast
   ↓
Database (fallback)
```

---

## Cache Kullanım Örneği

Normal durumda:

```python
User.objects.get(id=1)
```

Her seferinde DB’ye gider.

Cache ile:

```python
def get_user(user_id):

    cached = r.get(f"user:{user_id}")

    if cached:
        return cached.decode()

    user = User.objects.get(id=user_id)

    r.set(f"user:{user_id}", user.name, ex=60)

    return user.name
```

### ex=60 Nedir?

TTL (Time To Live)

60 saniye sonra veri otomatik silinir.

---

# 3️⃣ Redis Data Types

Redis birden fazla veri tipi destekler.

---

## 3.1 String

En basit veri tipi.

```
SET name "Ahmet"
GET name
```

Python:

```python
r.set("name", "Ahmet")
```

Use case:

- Cache
- Session ID
- Token

---

## 3.2 List

Ordered collection.

```
LPUSH queue task1
LPUSH queue task2
```

Python:

```python
r.lpush("queue", "task1")
```

Use case:

- Job queue
- Message queue

---

## 3.3 Set

Unique values collection.

```
SADD users 1
SADD users 2
```

Duplicate kabul etmez.

Use case:

- Unique IDs
- Tags

---

## 3.4 Hash

Dictionary gibidir.

```
HSET user:1 name Ahmet
HSET user:1 age 25
```

Python:

```python
r.hset("user:1", "name", "Ahmet")
```

Use case:

- Object storage

---

## 3.5 Sorted Set (ZSET)

Score ile sıralı set.

```
ZADD leaderboard 100 ahmet
ZADD leaderboard 200 mehmet
```

Use case:

- Leaderboard
- Ranking

---

## Redis Data Types Özeti

| Type        | Kullanım          |
|------------|-------------------|
| String     | Cache             |
| List       | Queue             |
| Set        | Unique collection |
| Hash       | Object storage    |
| Sorted Set | Ranking           |

---

# 4️⃣ Redis Caching Strategy

Mülakatlarda çok sorulur.

---

## Strategy 1 — Cache Aside (Lazy Loading)

En yaygın yöntemdir.

### Flow

```
Cache → varsa al
Cache → yoksa DB → cache'e yaz → dön
```

### Python Örneği

```python
def get_user(user_id):

    user = r.get(user_id)

    if user:
        return user.decode()

    user = db.get(user_id)

    r.set(user_id, user)

    return user
```

Avantaj:
- Basit
- Efficient

Dezavantaj:
- İlk request yavaş

---

## Strategy 2 — Write Through

Write işlemi hem cache’e hem DB’ye yapılır.

```
Write
  ↓
Cache update
  ↓
DB update
```

Avantaj:
- Cache her zaman güncel

Dezavantaj:
- Write yavaşlar

---

## Strategy 3 — Write Back

Write sadece cache’e yapılır.

DB async update edilir.

Avantaj:
- Çok hızlı write

Dezavantaj:
- Veri kaybı riski

---

## Strategy 4 — TTL Based Cache

```
SET user 1 EX 60
```

60 saniye sonra otomatik silinir.

---

# 5️⃣ Gerçek Dünya Kullanım Örnekleri

## 5.1 Django Cache

```python
from django.core.cache import cache

cache.set("name", "Ahmet", timeout=60)
cache.get("name")
```

Backend olarak Redis kullanılabilir.

---

## 5.2 Session Storage

```
session_id → Redis
```

---

## 5.3 Rate Limiting

```
user:1 → request count
```

Belirli süre içinde limit aşılırsa engellenir.

---

## 5.4 Queue (Celery)

```
Redis → Queue
Worker → Consume
```

Background task için kullanılır.

---

# 6️⃣ Redis vs Memcached

| Özellik      | Redis  | Memcached |
|-------------|--------|------------|
| Data types  | Çoklu  | String     |
| Persistence | Var    | Yok        |
| Features    | Zengin | Basic      |
| Usage       | Modern | Legacy     |

Genellikle Redis tercih edilir.

---

# 7️⃣ Redis Persistence (Advanced)

Redis RAM’de çalışır ama disk’e yazabilir.

İki yöntem:

- RDB (Snapshot)
- AOF (Append Only File)

Restart sonrası veri korunabilir.

---

# 8️⃣ Django + Redis Production Örneği

```python
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
    }
}
```

---

# 9️⃣ Redis Ne Zaman Kullanılır?

Kullan:

- Cache
- Session
- Queue
- Rate limiting
- Real-time data

Kullanma:

- Primary database olarak (kalıcı veri için uygun değildir)

---

# 🔟 Mülakat Soruları

**Redis nedir?**  
RAM üzerinde çalışan key-value data store’dur.

**Redis neden hızlıdır?**  
RAM kullanır, disk değil.

**Redis hangi veri tiplerini destekler?**  
String, List, Set, Hash, Sorted Set

**Cache nedir?**  
Sık kullanılan verinin hızlı erişim için saklanmasıdır.

**Cache Aside nedir?**  
Cache miss → DB → cache write

**Redis neden database yerine kullanılmaz?**  
RAM pahalıdır ve volatile olabilir.

---

# 1️⃣1️⃣ Gerçek Production Mimarisi

```
User
 ↓
Django
 ↓
Redis (Cache)
 ↓
PostgreSQL
```

---

# Özet

Redis:

- RAM based data store
- Çok hızlıdır
- Cache için kullanılır
- Session için kullanılır
- Queue için kullanılır

Caching:

- Performansı artırır
- Database yükünü azaltır