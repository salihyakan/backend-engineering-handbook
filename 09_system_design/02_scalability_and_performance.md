# 02 — SCALABILITY AND PERFORMANCE

Bu bölüm şu sorunun cevabıdır:

> Sistem 100 kullanıcıdan 10 milyon kullanıcıya nasıl ölçeklenir?

---

## 1. Scalability Nedir

### Tanım

Scalability, sistemin artan yükü kaldırabilme yeteneğidir.

### Örnek

- 100 user → OK  
- 1.000 user → OK  
- 1.000.000 user → OK  

Sistem artan yük altında bozulmuyorsa scalable’dır.

---

## 2. Scaling Türleri

İki tür scaling vardır:

- Vertical scaling  
- Horizontal scaling  

---

## 3. Vertical Scaling (Scale Up)

Server’ı güçlendirmektir.

### Örnek

- 4 CPU → 16 CPU  
- 8GB RAM → 64GB RAM  

### Avantaj

- Kolay uygulanır  

### Dezavantaj

- Donanımsal limit vardır  
- Pahalıdır  
- Single point of failure oluşturur  

---

## 4. Horizontal Scaling (Scale Out)

Server sayısını artırmaktır.

Server 1  
Server 2  
Server 3  
Server 4  

### Avantaj

- Teorik olarak sınırsız scaling  
- Daha güvenli  
- Modern sistemlerin standardı  

Modern büyük şirketlerin tamamı horizontal scaling kullanır.

---

## 5. Load Balancer

### Tanım

Load balancer, gelen request’leri birden fazla server’a dağıtır.

           Load Balancer
          /      |      \
      Server   Server   Server

### Amaç

- Yük dağıtımı  
- Performans artırma  
- Fault tolerance  

Gerçek araç örnekleri:

- NGINX  
- HAProxy  

---

## 6. Load Balancing Algoritmaları

### Round Robin

Request’leri sırayla dağıtır.

request 1 → server 1  
request 2 → server 2  
request 3 → server 3  

### Least Connections

En az aktif bağlantısı olan server’a gönderir.

### Hash Based

Belirli bir kullanıcıyı aynı server’a yönlendirir.

---

## 7. Stateless Architecture

### Tanım

Server state tutmaz.

Server 1 → works  
Server 2 → works  
Server 3 → works  

Hepsi aynı request’i işleyebilir.

### Neden Önemli?

Stateless mimari horizontal scaling’i mümkün kılar.

---

## 8. Stateful Architecture

Server state tutar.

Örnek: Session bilgisinin server memory’de tutulması.

### Problem

Server sayısını artırmak zorlaşır.

### Çözüm

State external bir sistemde tutulur:

- Redis  
- Database  

---

## 9. Cache (System Design Seviyesi)

Cache, hızlı veri depolama katmanıdır.

### Amaç

Database üzerindeki yükü azaltmak.

### Cache Flow

Request  
↓  
Cache  
↓ (miss)  
Database  
↓  
Cache save  
↓  
Response  

### Performans Farkı

Database → 10ms  
Cache → 0.1ms  

Yaklaşık 100x daha hızlıdır.

---

## 10. Cache Türleri

### Application Cache

Örnek:  
user = redis.get("user:1")

### Database Cache

Query sonuçlarını cache’leme.

### CDN Cache

Static dosyaların cache’lenmesi.

---

## 11. Cache Invalidation

Cache’i silme veya güncelleme işlemidir.

### Problem

Cache eski veri tutuyor olabilir.

### Çözüm

Update sonrası cache invalidate edilir.

---

## 12. Database Scaling Problemi

Basit mimari:

Server  
↓  
Database  

### Problem

Database overload olur.

---

## 13. Database Replication

Database’in kopyasını oluşturma.

Primary Database  
↓  
Replica Database  

### Write

Primary’ye gider.

### Read

Replica’dan yapılır.

Bu yöntem performansı artırır.

---

## 14. Read Scaling

          App  
         /   \  
     Replica  Replica  

Read operasyonları replica’lara dağıtılır.

---

## 15. Database Sharding

Database’i parçalara bölme yöntemidir.

Örnek:

Users 1–1M → Shard 1  
Users 1M–2M → Shard 2  

Bu en güçlü database scaling yöntemlerinden biridir.

---

## 16. CDN (Content Delivery Network)

Static içerikleri kullanıcıya en yakın lokasyondan sunar.

Örnek:

User Türkiye’de  
CDN Türkiye sunucusundan verir  
Ana server USA’de olsa bile  

---

## 17. Latency Optimization

Latency azaltma yöntemleri:

- Caching  
- CDN kullanımı  
- Load balancing  
- Database optimizasyonu  

---

## 18. Throughput Optimization

Throughput artırma yöntemleri:

- Horizontal scaling  
- Async processing  
- Load balancing  

---

## 19. Queue

Queue, asynchronous processing sağlar.

### Örnek

User image upload eder.

Queue ile:

- Image kaydedilir  
- Daha sonra işlenir  

Kullanılan araçlar:

- Redis  
- RabbitMQ  

---

## 20. Real World Scalable Architecture

User  
↓  
CDN  
↓  
Load Balancer  
↓  
App Servers  
↓  
Cache (Redis)  
↓  
Database (Primary)  
↓  
Database (Replica)  

---

## 21. Interview’da Nasıl Anlatılır?

Şöyle ifade edilir:

- Sistem horizontally scalable olacak  
- Load balancer kullanılacak  
- Stateless server mimarisi olacak  
- Redis cache kullanılacak  
- Database replication uygulanacak  

Bu yaklaşım senior seviyedir.

---

## 22. Bu Dosyada Öğrendiğin Kritik Kavramlar

- Scalability  
- Vertical scaling  
- Horizontal scaling  
- Load balancer  
- Stateless architecture  
- Cache  
- Database replication  
- Database sharding  
- CDN  
- Queue  

---

## Dosya Özeti

Dosya adı:

02_scalability_and_performance.md

### Öğrendiklerin

- Scaling mantığı  
- Cache mimarisi  
- Load balancing  
- Database scaling  
- CDN  
- High performance architecture  