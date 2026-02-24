# BÖLÜM 2 — SECURITY TESTING

Bu bölümde şunları öğreneceksin:

- Security testing nedir?
- Vulnerability (güvenlik açığı) nedir?
- Penetration testing (pentest) nedir?
- Automated security testing nasıl yapılır?
- Backend API security testing nasıl yapılır?
- Production ortamında güvenlik nasıl kontrol edilir?

Bu bölümün amacı: Güvenliği sadece “yazılan kod” olarak değil, **sürekli test edilmesi gereken bir süreç** olarak görmeni sağlamaktır.

---

# 1️⃣ Security Testing Nedir?

**Security testing**, sistemdeki güvenlik açıklarını saldırganlardan önce tespit etme sürecidir.

Amaç:

- Açıkları erken bulmak
- Veri sızıntısını önlemek
- Production sistemini korumak
- Riskleri minimize etmek

Security testing bir defalık değil, **sürekli yapılan bir süreçtir**.

---

## Vulnerability (Güvenlik Açığı) Nedir?

**Vulnerability**, saldırganın kullanabileceği zayıflıktır.

Örnek:

```python
query = f"SELECT * FROM users WHERE email='{email}'"
```

Bu kod bir **SQL Injection vulnerability** içerir.

Bir vulnerability:

- Veri sızıntısına
- Yetkisiz erişime
- Servis kesintisine
- Sistem ele geçirilmesine

neden olabilir.

---

# 2️⃣ Security Testing Türleri

Temel olarak 3 kategori vardır:

1. Manual Testing  
2. Automated Testing  
3. Penetration Testing  

---

# 3️⃣ Manual Security Testing

Engineer sistemi manuel olarak test eder.

Amaç:

- İş mantığı açıklarını bulmak
- Authorization hatalarını yakalamak
- Token manipülasyonlarını test etmek

---

## 🔎 Örnek — Authorization Test

Endpoint:

```
GET /api/users/5
```

Token içeriği:

```
user_id = 3
```

Test sorusu:

> Bu kullanıcı başka kullanıcının datasını görebiliyor mu?

Eğer görebiliyorsa:

🚨 **CRITICAL vulnerability (IDOR)**

---

## 🔎 Örnek — JWT Manipulation Test

JWT decode edilir.

Payload değiştirilir:

```json
{
  "user_id": 1,
  "is_admin": true
}
```

Eğer server bu token’ı kabul ederse:

🚨 Sistem tamamen kırılmıştır.

Doğru sistem:

- Signature verify eder
- Token’ı yeniden üretmeden kabul etmez
- Server-side kontrol yapar

---

# 4️⃣ Automated Security Testing

Tool’lar otomatik olarak açık arar.

Modern production sistemlerde zorunludur.

---

## 🔧 Popüler Security Testing Tool'ları

### 1️⃣ :contentReference[oaicite:0]{index=0}

:contentReference[oaicite:1]{index=1} tarafından geliştirilmiştir.

Yapabildikleri:

- SQL Injection testi
- XSS testi
- CSRF testi
- API endpoint taraması
- Otomatik security scanning

---

### 2️⃣ Bandit (Python Security Tool)

Python kodunda güvenlik açıklarını tarar.

Örnek risk:

```python
eval(user_input)
```

Bandit bunu high risk olarak işaretler.

Kurulum:

```bash
pip install bandit
bandit -r .
```

---

### 3️⃣ Safety (Dependency Scanner)

Dependency’lerdeki bilinen açıkları bulur.

Örnek:

Eski Django versiyonu kullanıyorsan uyarır.

Kurulum:

```bash
pip install safety
safety check
```

---

# 5️⃣ Penetration Testing (Pentest)

Pentest = saldırgan gibi davranarak sistemi kırmaya çalışma süreci.

Amaç:

- Gerçek saldırı simülasyonu
- Kritik açıkları ortaya çıkarmak
- Risk seviyesini ölçmek

Pentest yapan kişi:

- Pentester
- Security Engineer

---

## Pentest Aşamaları

### 1️⃣ Reconnaissance (Keşif)

Sistem hakkında bilgi toplanır:

- Endpoint listesi
- API yapısı
- Teknoloji stack’i
- Açık portlar

---

### 2️⃣ Scanning

Zayıf noktalar aranır:

- Açık servisler
- Yanlış konfigürasyon
- Zayıf endpointler

---

### 3️⃣ Exploitation

Bulunan açık kullanılarak sistem kırılmaya çalışılır.

Örnek:

- SQL injection denemesi
- Authorization bypass
- Token manipulation

---

### 4️⃣ Reporting

Bulunan açıklar raporlanır:

- Risk seviyesi
- Etki analizi
- Çözüm önerisi

Pentest’in en önemli çıktısı: **rapor**dur.

---

# 6️⃣ API Security Testing

Backend engineer için en kritik test alanıdır.

Test edilmesi gerekenler:

---

## 🔐 Authentication Test

```
GET /api/profile
```

Token yok → `401 Unauthorized` dönmeli.

---

## 🔐 Authorization Test

User sadece kendi datasını görebilmeli.

---

## 🔐 Input Validation Test

Test payload:

```json
{
  "email": "' OR 1=1 --"
}
```

Server:

- Crash olmamalı
- 500 dönmemeli
- Güvenli hata mesajı vermeli

---

## 🔐 Rate Limit Test

1000 request gönder.

Server:

```
429 Too Many Requests
```

dönmeli.

---

# 7️⃣ Dependency Security

Kullandığın library açık içerebilir.

Örnek:

Eski Django versiyonu.

Kontrol:

```bash
pip list --outdated
safety check
```

Dependency güncel değilse:

- CVE riski vardır
- Production tehlikeye girer

---

# 8️⃣ Production Security Testing

Production ortamında kullanılan yöntemler:

- Log monitoring
- Intrusion detection
- Rate limiting
- Firewall
- WAF (Web Application Firewall)

Şüpheli durumlar:

- Çok sayıda başarısız login
- Anormal trafik artışı
- Admin endpoint denemeleri

Monitoring sistemi alarm üretmelidir.

---

# 9️⃣ Django Security Testing

Django built-in korumalar sağlar:

- CSRF protection
- XSS protection (auto-escape)
- SQL injection protection (ORM)

Ama bu korumalar test edilmelidir.

---

## Django Test Örneği

```python
def test_unauthorized_access(client):
    response = client.get("/api/profile/")
    assert response.status_code == 401
```

Security testler unit test’lerin parçası olmalıdır.

---

# 🔟 CI/CD Security Testing

Security test pipeline’a entegre edilmelidir.

Pipeline:

```
Code Push
   ↓
Unit Tests
   ↓
Security Scan
   ↓
Deploy
```

Kullanılabilecek araçlar:

- bandit
- safety
- :contentReference[oaicite:2]{index=2}

---

# 11️⃣ Security Test Checklist

Her backend engineer şu kontrolleri yapmalıdır:

✔ Authentication test  
✔ Authorization test  
✔ SQL injection test  
✔ XSS test  
✔ Rate limit test  
✔ Dependency scan  
✔ Token manipulation test  
✔ Error handling test  

---

# Gerçek Dünya Örneği

:contentReference[oaicite:3]{index=3}

Her push’ta:

- Automated test
- Security scan
- Dependency check

çalıştırır.

Security, development lifecycle’ın bir parçasıdır.

---

# Bu Bölümün Özeti

Artık biliyorsun:

- Security testing nedir  
- Vulnerability nasıl tespit edilir  
- Manual vs Automated testing farkı  
- Penetration testing süreci  
- API security nasıl test edilir  
- Production güvenliği nasıl izlenir  
- CI/CD içinde security nasıl uygulanır  

Security test edilmeyen bir sistem, güvenli değildir.