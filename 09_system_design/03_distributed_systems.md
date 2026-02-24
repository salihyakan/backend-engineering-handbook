# BÖLÜM 3 — SCALABILITY & PERFORMANCE

Bu bölümde öğreneceğin şeyler:

- Scalability nedir  
- Vertical vs Horizontal scaling  
- Load balancing nedir  
- Caching neden kritik  
- Database scaling  
- Bottleneck nedir ve nasıl çözülür  

Bu bölüm system design’ın kalbidir.

---

## 1. Scalability Nedir

**Scalability = sistemin daha fazla kullanıcıyı kaldırabilme yeteneği**

Örnek:

Başlangıç:  
100 kullanıcı → sorun yok  

Sonra:  
10.000 kullanıcı → yavaşladı  
100.000 kullanıcı → çöktü  

Scalable sistem:  
Kullanıcı arttıkça sistem çökmemeli.

Gerçek hayat örneği:  

Instagram başlangıçta küçük bir server ile başladı.  
Şimdi milyonlarca kullanıcı ve binlerce server var.  
Bu scalability sayesinde mümkün.

---

## 2. Vertical Scaling (Dikey Büyütme)

Server'ı güçlendirmek.

Örnek:

Önce:  
4 GB RAM  
2 CPU  

Sonra:  
64 GB RAM  
16 CPU  

Bu vertical scaling’dir.

Avantaj:  
Basit.

Dezavantaj:  
Limit var.  
Bir noktadan sonra daha fazla RAM veya CPU ekleyemezsin.

---

## 3. Horizontal Scaling (Yatay Büyütme)

Yeni server eklemek.

Örnek:

Önce:  
1 server  

Sonra:  
5 server  

Sonra:  
50 server  

Mantık:

User → Server1  
User → Server2  
User → Server3  
User → Server4  

Yük dağıtılır.

En doğru scaling yöntemi:  
Horizontal scaling.

Modern sistemler bunu kullanır.

---

## 4. Load Balancer Nedir

Load balancer = gelen trafiği server’lara dağıtır.

Mantık:

Load Balancer  
→ Server1  
→ Server2  
→ Server3  

Load balancer olmasaydı:  
Tüm kullanıcı → Server1 → çöker.

Load balancer sayesinde:  
Kullanıcılar dağıtılır.

Load balancer görevleri:

- Traffic dağıtma  
- Server health check  
- Overload önleme  

Gerçek akış:

User request  
↓  
Load balancer  
↓  
Server seçilir  
↓  
Response döner  

---

## 5. Bottleneck Nedir

Bottleneck = sistemin en yavaş noktası.

Örnek:

User → API → Database  

Database yavaşsa:  
Tüm sistem yavaş olur.

Bottleneck kaynakları:

- Database  
- Disk  
- CPU  
- Network  
- API  

System design’da ana görev:  
Bottleneck bulmak ve çözmek.

---

## 6. Caching (Performansın Anahtarı)

Cache = sık kullanılan veriyi hızlı yerde tutmak.

Normal akış:

User → Server → Database → Server → User  

Database yavaş.

Cache ile:

User → Server → Cache → Server → User  

Database’e gitmeye gerek kalmaz.  
Çok hızlı.

Örnek:

Kullanıcı profili.

Cache yoksa:  
Her seferinde database.

Cache varsa:  
RAM’den alınır.

Yaklaşık 100x daha hızlı olabilir.

---

## 7. Database Scaling

Database en büyük bottleneck’tir.

### 7.1 Read Replica

Okuma işlemlerini farklı server’lara dağıtmak.

Mantık:

Master DB (write)  
→ Replica1 (read)  
→ Replica2 (read)  

Write:  
Master  

Read:  
Replica  

Avantaj:  
Database yükü azalır.

---

### 7.2 Database Sharding

Database’i bölmek.

Örnek:

DB1 → users 1–1M  
DB2 → users 1M–2M  
DB3 → users 2M–3M  

Her DB daha az yük taşır.

---

## 8. Stateless vs Stateful

Stateless = server state tutmaz.  
Stateful = server state tutar.

Stateless örnek:

User → herhangi bir server → çalışır.

Stateful örnek:

User → sadece Server1  

Risklidir.  
Server1 çökerse problem.

Modern sistemler stateless tercih eder.

---

## 9. Gerçek Dünya Örneği (Instagram Paylaşım Akışı)

User  
↓  
Load Balancer  
↓  
API Server  
↓  
Cache kontrol  
↓  
Cache varsa → dön  
Cache yoksa →  
↓  
Database  
↓  
Cache’e yaz  
↓  
User’a dön  

Bu performans için standart mimaridir.

---

## 10. Temel Ölçekleme Stratejisi Özeti

Başlangıç:

- 1 server  
- 1 database  

Büyüyünce:

- Load balancer  
- Multiple servers  
- Cache  
- Replica database  
- Sharding  

---

# Bu Bölümün Özeti

Bu bölümde öğrendiğin en kritik şeyler:

- Scalability nedir  
- Vertical vs Horizontal scaling  
- Load balancer  
- Caching  
- Database scaling  
- Bottleneck  

---

# Gerçek Bir Senior System Design Mantığı

Senior engineer her zaman şunu sorar:

“1 milyon kullanıcı gelirse ne olur?”

Ve sistemi buna göre tasarlar.