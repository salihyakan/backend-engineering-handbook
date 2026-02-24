# 01 — SYSTEM DESIGN FUNDAMENTALS

Bu bölüm system design’ın temelidir. Bunu anlamadan diğer konular tam oturmaz.

---

# 1. System Design Nedir?

## Tanım

System design, büyük ölçekli bir yazılım sisteminin mimarisini planlama sürecidir.

Şunları tasarlarsın:

- Bileşenler
- Veri akışı
- Performans
- Ölçeklenebilirlik (Scalability)
- Güvenilirlik (Reliability)

---

## Örnek System Design Problemi

> “Instagram’ı tasarla”

Şunları düşünmen gerekir:

- Kullanıcı sistemi
- Post sistemi
- Feed sistemi
- Database yapısı
- Cache kullanımı
- Scaling stratejisi

System design sorusu aslında şudur:

> Bu sistemi milyonlarca kullanıcıya nasıl çalıştırırsın?

---

# 2. Temel Mimari Yapı (High Level Architecture)

## Basit Web Sistemi

```
Client
  ↓
Server
  ↓
Database
```

## Gerçek Production Sistemi

```
Client
 ↓
Load Balancer
 ↓
Web Server
 ↓
Application Server
 ↓
Database
 ↓
Cache
```

---

# 3. Client Nedir?

Client = isteği gönderen sistem.

Örnek:

- Browser (Chrome)
- Mobile app
- Frontend uygulaması

Örnek akış:

```
Chrome → HTTP Request → Server
```

---

# 4. Server Nedir?

Server = Request’i işleyen sistem.

Örnek (Django):

```python
def get_users(request):
    return JsonResponse({"users": []})
```

Server görevleri:

- Request işleme
- Business logic çalıştırma
- Database erişimi

---

# 5. Database Nedir? (System Design Perspektifi)

Database veri depolar.

Örnek tablolar:

- users
- posts
- comments

System design açısından database kritik bileşendir.

Çünkü:

- Bottleneck olabilir
- Scaling zordur
- Single point of failure olabilir

---

# 6. Request Flow Nedir?

## Genel Request Lifecycle

```
Client
 ↓
Load Balancer
 ↓
Server
 ↓
Database
 ↓
Server
 ↓
Client
```

## Django Production Flow

```
Browser
 ↓
NGINX
 ↓
Gunicorn
 ↓
Django
 ↓
PostgreSQL
 ↓
Response
```

---

# 7. Vertical Scaling Nedir?

Server gücünü artırma yöntemidir.

Örnek:

```
2 CPU → 8 CPU
4GB RAM → 32GB RAM
```

Avantaj:

- Basit uygulanır

Dezavantaj:

- Donanım limiti vardır
- Sonsuz ölçeklenemez

---

# 8. Horizontal Scaling Nedir?

Daha fazla server ekleme yöntemidir.

```
Server 1
Server 2
Server 3
```

Avantaj:

- Teorik olarak sınırsız ölçeklenebilir

Modern sistemler genelde horizontal scaling kullanır.

---

# 9. Load Balancer Nedir?

Load balancer request’leri server’lara dağıtır.

```
        Load Balancer
        /     |     \
    Server  Server  Server
```

Amaç:

- Performans artırmak
- Reliability sağlamak

Örnek araçlar:

- NGINX
- HAProxy

---

# 10. Latency Nedir?

Latency = Bir request’in cevap süresi.

Örnek:

```
Request → Response = 50ms
```

Düşük latency daha iyidir.

---

# 11. Throughput Nedir?

Throughput = Saniyede işlenen request sayısı.

Örnek:

```
1000 request/sec
```

Yüksek throughput daha iyi kapasite demektir.

---

# 12. Bottleneck Nedir?

Sistemin en yavaş bileşeni.

Örnek:

- Hızlı application server
- Yavaş database

Database bottleneck olur.

---

# 13. Single Point of Failure Nedir?

Tek bir bileşen çökerse tüm sistem çöker.

Örnek:

- Tek database server

Database crash → sistem down

Çözüm:

- Database replication
- Redundant bileşenler

---

# 14. High Availability Nedir?

Sistemin sürekli çalışır durumda olmasıdır.

Genelde uptime yüzdesiyle ifade edilir:

- 99.9%
- 99.99%
- 99.999%

Amaç: Minimum downtime.

---

# 15. Stateless vs Stateful

## Stateless

Server state tutmaz.

Örnek:

- REST API (JWT ile)

Her request kendi auth bilgisini taşır.

---

## Stateful

Server state tutar.

Örnek:

- Session memory

Modern sistemler genelde stateless tercih eder.

Çünkü:

- Scaling daha kolaydır.

---

# 16. Cache Nedir?

Cache = Hızlı veri erişimi için geçici depolama.

Örnek:

- Redis

Cache flow:

```
Request
 ↓
Cache
 ↓ (miss)
Database
```

Cache performansı dramatik şekilde artırır.

---

# 17. CDN Nedir?

CDN (Content Delivery Network), static content dağıtır.

Örnek içerikler:

- Images
- CSS
- JS

Örnek provider:

- Cloudflare

Flow:

```
User
 ↓
CDN
 ↓
Server
```

Amaç:

- Latency düşürmek
- Server yükünü azaltmak

---

# 18. High Level System Design Örneği

Django production mimarisi:

```
Client
 ↓
CDN
 ↓
NGINX
 ↓
Gunicorn
 ↓
Django
 ↓
Redis
 ↓
PostgreSQL
```

---

# 19. System Design Interview’da İlk Yapılan Şey

Her zaman önce high level design çizilir:

```
Client
Server
Database
Cache
```

Sonra detaylandırılır:

- Scaling
- Database tasarımı
- Caching stratejisi
- Failure senaryoları

---

# 20. Bu Bölümde Öğrendiğin En Kritik Kavramlar

Bilmen gereken temel kavramlar:

- Client
- Server
- Database
- Load balancer
- Horizontal scaling
- Vertical scaling
- Latency
- Throughput
- Bottleneck
- Stateless
- Cache
- CDN

---

# Özet

Dosya:  
`01_system_design_fundamentals.md`

Bu bölümde öğrendiklerin:

- System design temeli
- Mimari bileşenler
- Scaling kavramı
- Request flow
- Production yapı mantığı