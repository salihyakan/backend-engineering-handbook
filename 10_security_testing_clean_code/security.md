# BÖLÜM 1 — APPLICATION SECURITY FUNDAMENTALS

Bu bölümde şunları öğreneceksin:

- Security neden kritiktir?
- OWASP Top 10 mantığı
- Authentication vs Authorization farkı
- En yaygın web saldırıları
- Backend güvenliği nasıl tasarlanır?
- Secure coding prensipleri

Bu bölümün amacı seni sadece “kod yazan developer” olmaktan çıkarıp, riskleri öngörebilen bir **software engineer** seviyesine taşımaktır.

---

## 1️⃣ Security Nedir?

**Application Security**, bir sistemi:

- Yetkisiz erişimden  
- Veri sızıntısından  
- Manipülasyondan  
- Servis kesintisinden (DoS)  

koruma sürecidir.

Security sadece teknik bir konu değildir. Aynı zamanda:

- Finansal risk
- Hukuki sorumluluk
- Marka itibarı
- Kullanıcı güveni

meselesidir.

### Gerçek Hayat Örnekleri

- :contentReference[oaicite:0]{index=0} veri ihlalleri  
- :contentReference[oaicite:1]{index=1} veri sızıntısı (2017)

Bu olayların temel sebepleri:

- Patch uygulanmaması
- Zayıf konfigürasyon
- Input validation eksikliği
- Güncel olmayan dependency’ler

**Security ihlali = Para kaybı + İtibar kaybı + Hukuki kriz**

---

## 2️⃣ OWASP Nedir?

:contentReference[oaicite:2]{index=2}  
(Open Web Application Security Project)

Web uygulama güvenliği için global bir referans organizasyondur.

### OWASP Top 10

En yaygın ve en kritik web güvenlik açıklarını listeler.

Bir backend geliştiricinin OWASP Top 10 mantığını iyi bilmesi beklenir.

Bu liste sana şunları öğretir:

- En sık yapılan güvenlik hataları
- Saldırganların hedeflediği zayıf noktalar
- Risklerin nasıl sınıflandırıldığı

---

## 3️⃣ Authentication vs Authorization

Bu iki kavram sıklıkla karıştırılır.

### Authentication (Kimlik Doğrulama)

> “Sen kimsin?”

Örnekler:

- Email + password
- JWT token
- OAuth login

Kullanıcının kimliğini doğrular.

---

### Authorization (Yetkilendirme)

> “Ne yapabilirsin?”

Örnekler:

- Admin panel erişimi
- Kullanıcı silme yetkisi
- Ödeme işlemi yapma

Kimliği doğrulanmış kullanıcının **hangi işlemleri yapabileceğini** belirler.

⚠ Kritik hata:

Authentication var ama authorization kontrolü yoksa sistem ciddi risk altındadır.

---

## 4️⃣ En Yaygın Web Saldırıları

### 4.1 SQL Injection

Kullanıcı girdisinin doğrudan SQL sorgusuna eklenmesiyle oluşur.

❌ Kötü örnek:

```python
query = "SELECT * FROM users WHERE email = '" + email + "'"
```

Saldırgan şu girdiyi verirse:

```
' OR 1=1 --
```

Tüm kullanıcılar dönebilir.

✅ Doğru örnek (Parameterized Query):

```python
cursor.execute(
    "SELECT * FROM users WHERE email = %s",
    (email,)
)
```

Django ORM kullanıyorsan:

```python
User.objects.get(email=email)
```

ORM parametre bağlama yaptığı için SQL injection riskini büyük ölçüde azaltır.

---

### 4.2 XSS (Cross Site Scripting)

Amaç: Kullanıcının tarayıcısında kötü amaçlı JavaScript çalıştırmak.

Örnek saldırı:

```html
<script>alert("Hacked")</script>
```

Eğer çıktı escape edilmezse, başka kullanıcıların tarayıcısında çalışır.

#### Koruma Yöntemleri:

- Output escaping
- HTML sanitization
- Content Security Policy (CSP)
- Django template auto-escape özelliği

---

### 4.3 CSRF (Cross Site Request Forgery)

Kullanıcı login olmuşken onun adına işlem yapılmasıdır.

Senaryo:

- Kullanıcı bankaya login
- Başka bir sitede gizli form otomatik submit edilir

#### Koruma:

- CSRF token
- SameSite cookie
- Origin/Referer kontrolü

Django varsayılan olarak CSRF koruması sağlar.

---

### 4.4 Broken Authentication

Problemler:

- Zayıf parola politikası
- Rate limit olmaması
- Brute force koruması yok
- Session yönetim hataları

#### Çözüm:

- Güçlü password policy
- Password hashing (bcrypt / Argon2)
- Rate limiting
- 2FA (Two-Factor Authentication)

---

### 4.5 Insecure Direct Object Reference (IDOR)

Örnek:

```
/user/5
```

Kullanıcı ID değiştirirse:

```
/user/6
```

Eğer kontrol yoksa başka kullanıcının datasını görür.

#### Doğru yaklaşım:

```python
if request.user.id != user_id:
    return HttpResponseForbidden()
```

Her request’te authorization kontrolü yapılmalıdır.

---

## 5️⃣ Password Security

Asla:

- Plaintext password saklama
- MD5 veya SHA1 kullanma

Modern yöntemler:

- bcrypt
- Argon2
- PBKDF2

Django varsayılan olarak güvenli password hashing kullanır.

---

## 6️⃣ JWT Güvenliği

JWT kullanırken dikkat edilmesi gerekenler:

- Kısa access token süresi
- Refresh token mekanizması
- Secret key güvenliği
- HTTPS zorunluluğu
- Logout için blacklist mekanizması

Aksi halde token çalınırsa hesap ele geçirilebilir.

---

## 7️⃣ HTTPS Neden Zorunlu?

HTTP:

- Plain text iletişim
- Ortadaki biri (MITM) veriyi okuyabilir

HTTPS:

- TLS encryption
- Veri şifreli iletilir

Production ortamında HTTPS zorunludur.

---

## 8️⃣ Rate Limiting

Amaç:

- Brute force saldırıları engellemek
- DoS riskini azaltmak

Örnek:

Bir IP saniyede 1000 login denemesi yapamamalı.

Çözümler:

- API throttling
- Reverse proxy limit (Nginx)
- WAF
- Django Rest Framework throttling

---

## 9️⃣ Environment Variables

Secret değerler:

- SECRET_KEY
- DB password
- API key
- JWT secret

Asla:

- GitHub’a koyma
- Source code içine yazma

Doğru yöntem:

- `.env` dosyası
- Environment variable
- Secret manager kullanımı

---

## 🔟 Logging & Monitoring

Security sadece kod yazmak değildir.

Aynı zamanda:

- Loglama
- İzleme
- Alarm üretme

gerektirir.

Şüpheli durumlar:

- 100+ başarısız login
- Anormal trafik artışı
- Admin endpoint erişim denemeleri

Bu olaylar loglanmalı ve alarm üretilmelidir.

---

# Secure Backend Checklist

✔ Parameterized query  
✔ ORM kullanımı  
✔ CSRF koruma  
✔ XSS koruma  
✔ Password hashing  
✔ Rate limit  
✔ HTTPS zorunluluğu  
✔ Authorization kontrolü  
✔ Secret management  
✔ Güncel dependency yönetimi  

---

# Senior Seviyesi Güvenlik Bakışı

Senior engineer şu soruyu sorar:

> “Bu endpoint’i kötü niyetli biri kullanırsa ne olur?”

Her endpoint:

- Manipüle edilmeye çalışılır
- Beklenmeyen input alır
- Yetkisiz erişim denenir
- Abuse edilmeye çalışılır

Security bir feature değil, bir tasarım yaklaşımıdır.

---

# Bu Bölümün Özeti

Bu bölümde öğrendiklerin:

- OWASP mantığı  
- SQL Injection  
- XSS  
- CSRF  
- IDOR  
- Authentication vs Authorization  
- Secure coding temelleri  
- Backend güvenlik tasarım prensipleri  