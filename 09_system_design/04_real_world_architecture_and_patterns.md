# BÖLÜM 4 — REAL WORLD SYSTEM DESIGN EXAMPLES

Bu bölümde şunları öğreneceksin:

- Instagram nasıl tasarlanır  
- WhatsApp nasıl tasarlanır  
- URL Shortener (Bitly gibi) nasıl çalışır  
- Büyük sistemlerin production mimarisi  

Bu bölüm seni gerçek bir system designer seviyesine yaklaştırır.

---

# ÖRNEK 1 — Instagram System Design

## Amaç

- Kullanıcı post paylaşır  
- Diğer kullanıcılar feed’de görür  

---

## Temel Bileşenler

- User  
- Load Balancer  
- API Servers  
- Cache (Redis)  
- Database  
- Object Storage (images)  
- CDN  

---

## Post Paylaşma Akışı

User  
↓  
Load Balancer  
↓  
API Server  
↓  
Image → Object Storage’a yüklenir  
↓  
Post metadata → Database’e kaydedilir  
↓  
İlgili feed cache’leri invalidate edilir  
↓  
Response → User  

### Neden metadata ve image ayrılır?

- Image büyük binary veridir  
- Database’te saklamak pahalı ve yavaştır  
- Bu yüzden Object Storage kullanılır (örneğin S3 benzeri sistemler)  

---

## Feed Görüntüleme Akışı

User  
↓  
Load Balancer  
↓  
API Server  
↓  
Cache kontrol edilir  

- Cache varsa → Feed direkt döner  
- Cache yoksa → Database’e gidilir  
↓  
Sonuç Cache’e yazılır  
↓  
User’a dönülür  

### Neden Cache kullanılır?

Çünkü milyonlarca kullanıcı aynı veya benzer feed’i ister.

Cache sayesinde:

- Database yükü dramatik şekilde azalır  
- Response süresi ciddi şekilde düşer  

Genelde Redis gibi in-memory cache sistemleri kullanılır.

---

## CDN neden kullanılır?

CDN = Content Delivery Network  

Amaç:

- Image’ı kullanıcıya en yakın lokasyondan sunmak  
- Ana sunucu yükünü azaltmak  
- Latency’yi düşürmek  

CDN olmadan büyük ölçekli medya platformu çalışmaz.

---

# ÖRNEK 2 — WhatsApp System Design

## Amaç

- Gerçek zamanlı mesaj gönderme ve alma  

---

## Temel Mimari

User A  
↓  
Load Balancer  
↓  
Chat Server  
↓  
Message Queue  
↓  
Database  
↓  
User B  

---

## Mesaj Gönderme Akışı

User A  
↓  
Chat Server  
↓  
Message Queue  
↓  
Database’e yazılır  
↓  
User B’ye iletilir  

---

## Message Queue neden kullanılır?

Çünkü:

- User B offline olabilir  
- Trafik ani artabilir  
- Mesajlar sıraya alınarak güvenli şekilde işlenir  

Queue sayesinde:

- Mesaj kaybolmaz  
- Sistem spike’lara dayanıklı olur  
- Asenkron işleme mümkün olur  

---

## Stateless Server Mantığı

Chat server’lar stateless tasarlanır.

Bu sayede:

- Herhangi bir server mesajı işleyebilir  
- Horizontal scaling kolaylaşır  
- Load balancer rahat çalışır  

State external sistemlerde tutulur (Database, Cache, Queue).

---

# ÖRNEK 3 — URL Shortener System Design (Bitly gibi)

## Amaç

Uzun URL:

https://example.com/very-long-url  

Kısa URL’ye çevrilir:

bit.ly/abc123  

---

## Temel Mimari

User  
↓  
Load Balancer  
↓  
API Server  
↓  
Database  

---

## URL Oluşturma Akışı

User  
↓  
API Server  
↓  
Unique Short ID generate edilir  
↓  
Database’e (short_id → original_url) kaydedilir  
↓  
Short URL dönülür  

### Short ID nasıl üretilir?

- Auto-increment ID + Base62 encode  
veya  
- Random string (collision kontrolü ile)  

Collision riskine karşı uniqueness garanti edilmelidir.

---

## Redirect Akışı

User → bit.ly/abc123  
↓  
Load Balancer  
↓  
API Server  
↓  
Cache kontrol edilir  

- Cache varsa → direkt original URL  
- Cache yoksa → Database’e gidilir  
↓  
Cache’e yazılır  
↓  
HTTP redirect (302) yapılır  

---

## Cache neden kritik?

Aynı link milyonlarca kez açılabilir.

Cache sayesinde:

- Database’e gitmeye gerek kalmaz  
- Sistem çok daha yüksek throughput kaldırır  

---

# ÖRNEK 4 — YouTube Video System Design

## Amaç

- Video upload  
- Video streaming  

---

## Upload Akışı

User  
↓  
Load Balancer  
↓  
API Server  
↓  
Video Storage’a yüklenir  
↓  
Metadata → Database’e yazılır  

Büyük sistemlerde:

- Video transcoding (farklı kalite seçenekleri)  
- Async processing  
- Queue kullanımı  

---

## İzleme Akışı

User  
↓  
CDN  
↓  
Video stream edilir  

CDN burada kritik.

Çünkü:

- Video büyük veridir  
- Merkezi server’dan dağıtılamaz  
- Global edge lokasyonlardan servis edilmelidir  

CDN olmadan sistem ölçeklenemez.

---

# Production System Genel Mimarisi

Gerçek production mimarisi genelde şu şekildedir:

Users  
↓  
CDN  
↓  
Load Balancer  
↓  
API Servers  
↓  
Cache (Redis)  
↓  
Database  
↓  
Object Storage  

---

# Production’da Kullanılan Teknolojiler

Cache:  
- Redis  

Database:  
- PostgreSQL  
- MySQL  

Load Balancer:  
- Nginx  

Cloud:  
- AWS  
- GCP  

---

# Gerçek Dünya (Meta Seviyesi) Mimari

Büyük şirketlerde:

- Binlerce server  
- Global CDN  
- Sharded database  
- Distributed cache  
- Multi-region deployment  

kullanılır.

Ama temel prensipler aynıdır:

- Stateless application layer  
- Horizontal scaling  
- Cache-first yaklaşım  
- Bottleneck’i erken çözmek  

---

# System Design Interview’da Beklenen Düşünce Yapısı

Bir system design sorusunda şu sırayı izlemelisin:

## 1. Requirements

Ne yapılacak?

Örnek:  
URL shortener

- Functional requirements  
- Non-functional requirements (latency, availability, scale)

---

## 2. High-Level Design

Ana bileşenleri çiz:

User  
API  
Database  
Cache  
Load Balancer  

---

## 3. Scaling Düşün

- Load balancer ekle  
- Stateless server tasarla  
- Cache ekle  
- Replica database ekle  
- Gerekirse sharding yap  

---

## 4. Bottleneck Çöz

- Cache kullan  
- Read replica kullan  
- Sharding yap  
- Async processing ekle  

---

# Bu Bölümün Özeti

Bu bölümde öğrendiğin en kritik şey:

Gerçek dünya sistemleri şu prensiplerle kurulur:

- Stateless architecture  
- Horizontal scaling  
- Cache-first yaklaşım  
- Database replication ve sharding  
- CDN kullanımı  
- Queue ile async processing  

Gerçek bir senior engineer her zaman şunu sorar:

“Bu sistem 10 kat büyürse ne olur?”  

Ve tasarımını buna göre yapar.