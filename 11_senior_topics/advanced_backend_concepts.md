# BÖLÜM — ADVANCED BACKEND CONCEPTS

Bu bölümde production seviyesinde backend sistemlerin nasıl tasarlandığını öğreneceksin.

İçerik:

- Stateless vs Stateful Architecture  
- Scalability (Vertical vs Horizontal Scaling)  
- Load Balancing  
- Advanced Caching Concepts  
- Background Jobs & Task Queues  
- Message Queues & Event-Driven Architecture  
- Idempotency  
- Rate Limiting & Throttling  
- Database Scaling (Replication & Sharding)  
- Consistency Patterns  
- Circuit Breaker Pattern  
- Distributed Systems Fundamentals  
- Observability (Logging, Monitoring, Tracing)  
- Graceful Degradation  
- CAP Theorem  

Bu bölüm seni “backend developer” seviyesinden “production system designer” seviyesine taşır.

---

# 1️⃣ Stateless vs Stateful Architecture

Production backend’in en kritik tasarım kararlarından biridir.

## Stateless Nedir?

Server hiçbir client state’ini memory’de saklamaz.  
Her request bağımsızdır.

Örnek:

```
GET /api/profile
Authorization: Bearer xyz123
```

Server sadece token’ı doğrular.  
State:

- Database’te
- Cache’te
- Token içinde

tutulur.

### Avantajları

✔ Horizontal scaling kolay  
✔ Load balancing sorunsuz  
✔ Fault tolerant  
✔ Server restart güvenli  

### Django Stateless Örneği

```python
def get_profile(request):
    user = request.user
    return Response(user.username)
```

Server memory’de session map tutmaz.

---

## Stateful Nedir?

Server client state’i memory’de tutar.

```python
logged_users = {
    "session123": user_id
}
```

### Problemleri

- Server restart → state kaybolur
- Horizontal scaling zorlaşır
- Sticky session gerekir
- Fault tolerance düşer

Modern production sistemler **stateless tasarlanır**.

---

# 2️⃣ Scalability

**Scalability**, sistemin artan yükü kaldırabilme kapasitesidir.

## Vertical Scaling

Server büyütülür:

- 4 CPU → 16 CPU
- 8GB RAM → 64GB RAM

Kolaydır ama limitlidir.

---

## Horizontal Scaling

Server sayısı artırılır:

```
1 server → 10 server
```

Production standardıdır.

```
User → Load Balancer → Server1
                         Server2
                         Server3
```

Cloud-native sistemler horizontal scaling üzerine kuruludur.

---

# 3️⃣ Load Balancing

Load balancer = trafik dağıtıcı katman.

1000 request:

- Server1 → 333
- Server2 → 333
- Server3 → 334

Popüler load balancer çözümleri:

- :contentReference[oaicite:0]{index=0}  
- :contentReference[oaicite:1]{index=1}  
- Cloud provider load balancer’ları  

Load balancing:

- Availability artırır
- Fault tolerance sağlar
- Horizontal scaling’i mümkün kılar

---

# 4️⃣ Advanced Caching Concepts

Cache performansın anahtarıdır.

## Cache Türleri

### 1️⃣ Application Cache

Genellikle :contentReference[oaicite:2]{index=2} kullanılır.

```python
cache.set("user:1", user_data)
```

---

### 2️⃣ Database Query Cache

Sık çalışan query’ler cache edilir.

---

### 3️⃣ CDN Cache

Static içerikler CDN üzerinden servis edilir.

---

## Cache Pattern — Cache Aside

En yaygın yöntem:

```python
def get_user(user_id):
    user = cache.get(user_id)

    if not user:
        user = db.get(user_id)
        cache.set(user_id, user)

    return user
```

Akış:

Cache → Miss → DB → Cache’e yaz → Return

---

# 5️⃣ Background Jobs & Task Queues

Uzun süren işlemler async yapılmalıdır.

Örnek:

- Email gönderme
- PDF üretme
- Image processing

## ❌ Yanlış

```python
def register():
    send_email()  # blocking
```

## ✅ Doğru

```python
def register():
    send_email_task.delay()
```

Kullanılan araçlar:

- :contentReference[oaicite:3]{index=3}  
- :contentReference[oaicite:4]{index=4}  
- :contentReference[oaicite:5]{index=5}  

---

# 6️⃣ Message Queue & Event-Driven Architecture

Message queue servisler arası async iletişim sağlar.

```
Order Service → Queue → Email Service
```

Queue olmadan:

Order service email’i bekler.

Queue ile:

Order hemen tamamlanır.

Popüler sistemler:

- :contentReference[oaicite:6]{index=6}  
- :contentReference[oaicite:7]{index=7}  
- :contentReference[oaicite:8]{index=8}  

---

# 7️⃣ Idempotency

Idempotent = aynı request tekrar edilirse sonuç değişmez.

Ödeme API örneği:

## ❌ Yanlış

```
POST /pay
```

İki kez çağrılır → 2 ödeme oluşur.

## ✅ Doğru

```
POST /pay
Idempotency-Key: abc123
```

Server:

- Key’i kontrol eder
- Aynı işlemi tekrar etmez

Fintech sistemlerde zorunludur.

---

# 8️⃣ Rate Limiting & Throttling

Amaç:

- Abuse önlemek
- DDoS riskini azaltmak
- Fair usage sağlamak

Örnek:

Max 100 request / minute

```python
count = redis.get(ip)

if count > 100:
    return 429
```

Genellikle Redis tabanlı sayaç kullanılır.

---

# 9️⃣ Database Replication

Database kopyalanır.

- Primary → Write
- Replica → Read

```
App → Read → Replica
App → Write → Primary
```

Avantaj:

✔ Read performance artar  
✔ Primary yükü azalır  

---

# 🔟 Database Sharding

Database yatay bölünür.

Örnek:

- Users 1–1M → Shard1
- Users 1M–2M → Shard2

Avantaj:

✔ Büyük veri ölçeklenebilir  
✔ Tek DB bottleneck olmaz  

---

# 11️⃣ Event-Driven Architecture

Bir event oluşur:

```
UserRegistered
```

Servisler bu event’i dinler:

- Email Service → Welcome mail
- Analytics → Event log
- Billing → Trial başlat

Avantaj:

✔ Loose coupling  
✔ Microservice uyumlu yapı  
✔ High scalability  

---

# 12️⃣ Circuit Breaker Pattern

Bir servis çökerse zincirleme hata oluşmasını engeller.

Örnek:

Payment service down.

Circuit breaker:

- Request göndermez
- Timeout beklemez
- Fallback döner

Bu pattern özellikle microservice mimarilerde kritiktir.

---

# 13️⃣ Observability

Production sistem izlenmelidir.

3 temel bileşen:

## Logging

```python
logger.info("User logged in")
```

## Monitoring

- CPU
- RAM
- Disk
- Response time

## Tracing

Request’in tüm servisler arasındaki yolculuğunu gösterir.

Observability olmadan production yönetilemez.

---

# 14️⃣ Graceful Degradation

Sistem tamamen çökmez.

Örnek:

Recommendation service down.

Ana sistem:

- Çalışmaya devam eder
- “Öneriler şu an mevcut değil” mesajı gösterir

High availability sistemlerin özelliğidir.

---

# 15️⃣ CAP Theorem

Distributed sistemlerde aynı anda şu 3 özelliğin hepsi garanti edilemez:

- Consistency
- Availability
- Partition Tolerance

Sadece 2’si seçilebilir.

Örnek:

- Banka sistemi → Consistency öncelikli  
- Sosyal medya → Availability öncelikli  

Distributed database tasarımı CAP’e göre yapılır.

---

# Production Django Mimarisi Örneği

```
Client
↓
CDN
↓
Load Balancer
↓
Django Servers (Stateless)
↓
Redis Cache
↓
PostgreSQL (Primary + Replica)
↓
Worker (Celery)
```

Bu yapı:

- Scalable
- Fault tolerant
- High performance
- Production ready

---

# Senior Backend Engineer’ın Bildiği Advanced Concepts

✔ Stateless architecture  
✔ Horizontal scaling  
✔ Load balancing  
✔ Advanced caching  
✔ Background jobs  
✔ Message queues  
✔ Database replication & sharding  
✔ Observability  
✔ Fault tolerance  
✔ Distributed systems mantığı  

---

# Real World Production Stack Örneği

:contentReference[oaicite:9]{index=9} backend mimarisi (genel yaklaşım):

- Load balancer
- Stateless application servers
- Redis cache
- Distributed database
- Queue systems
- Horizontal scaling

---

# Özet

Bu bölümden sonra artık biliyorsun:

- Scalable system nasıl tasarlanır
- Production backend nasıl çalışır
- High performance sistem nasıl kurulur
- Distributed system temelleri
- Modern backend engineering prensipleri

Bu noktadan sonra artık sadece API yazmıyorsun.  
Sistem tasarlıyorsun.