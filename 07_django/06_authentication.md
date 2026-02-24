# BÖLÜM 14 — AUTHENTICATION (Kimlik Doğrulama ve Yetkilendirme)

Bu bölüm backend güvenliğinin temelidir. Production seviyesinde bir backend developer’ın authentication sistemlerini derinlemesine anlaması gerekir.

Bu bölümde öğreneceksin:

- Authentication nedir?
- Authorization nedir?
- Session authentication
- Token authentication
- JWT authentication
- Production’da hangisi neden kullanılır?

---

## 1️⃣ Authentication Nedir?

Authentication = Kullanıcının kim olduğunu doğrulama işlemidir.

Soru:

> Bu kullanıcı kim?

Örnek:

```text
username: salih
password: 123456
```

Backend kontrol eder:

- Doğruysa → Authenticated
- Yanlışsa → Rejected

### Gerçek Dünya Örneği

ATM:

Kart + PIN → Authentication

---

### Django’da Authentication Örneği

```python
from django.contrib.auth import authenticate

user = authenticate(username="salih", password="123456")

if user is not None:
    print("Authenticated")
else:
    print("Rejected")
```

- Doğruysa → `user` object döner
- Yanlışsa → `None` döner

---

## 2️⃣ Authorization Nedir?

Authorization = Kullanıcının ne yapmaya yetkili olduğunu belirleme işlemidir.

Soru:

> Bu kullanıcı ne yapabilir?

Örnek:

- Normal user → Ürün görebilir
- Admin → Ürün silebilir

---

### Authentication vs Authorization

Authentication:

> Sen kimsin?

Authorization:

> Ne yapabilirsin?

---

## 3️⃣ Django Authentication Sistemi

Django built-in `User` modeli:

```python
from django.contrib.auth.models import User
```

Database tablosu:

```text
auth_user
```

Önemli alanlar:

- id
- username
- password
- email
- is_staff
- is_superuser

---

## 4️⃣ Session Authentication

Session authentication = Django’nun default authentication sistemidir.

Session = Server tarafında tutulan kullanıcı oturumudur.

---

### Nasıl Çalışır?

1. Login:
   - username + password doğrulanır
2. Server:
   - session oluşturur
   - session_id üretir
3. Browser:
   - session_id cookie olarak saklar
4. Sonraki request:
   - cookie → session_id → user bulunur

---

### Flow

```
Login
   ↓
Server session oluşturur
   ↓
session_id cookie gönderilir
   ↓
Browser cookie saklar
   ↓
Sonraki requestlerde cookie gönderilir
   ↓
User authenticated olur
```

---

### Django Login Örneği

```python
from django.contrib.auth import authenticate, login

user = authenticate(username="salih", password="123")

if user:
    login(request, user)
```

### Logout

```python
from django.contrib.auth import logout

logout(request)
```

---

## 5️⃣ Session Nerede Saklanır?

Server tarafında:

- Database
- Cache
- Redis
- File

Client tarafında:

- Sadece `session_id` saklanır (cookie)

---

## 6️⃣ Session Authentication Avantaj & Dezavantaj

Avantaj:

- Çok güvenli
- Django ile native çalışır

Dezavantaj:

- Mobile app için uygun değil
- Scalable değil (microservice mimaride zor)

---

## 7️⃣ Token Authentication

Session yerine token kullanılır.

Server:

- Token üretir

Client:

- Token saklar

Request:

```http
Authorization: Token xxxxxxxxx
```

---

### Token Örneği

```text
abc123xyz456
```

---

### DRF Token Authentication

Model:

```python
from rest_framework.authtoken.models import Token

token = Token.objects.create(user=user)
```

Request header:

```http
Authorization: Token abc123xyz
```

settings.py:

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ]
}
```

---

## 8️⃣ Token Authentication Flow

```
Login
   ↓
Token oluşturulur
   ↓
Client token saklar
   ↓
Request → Token gönderilir
   ↓
User bulunur
```

---

## 9️⃣ JWT (JSON Web Token)

Modern authentication standardıdır.

Stateless authentication sağlar.

JWT formatı:

```text
header.payload.signature
```

Örnek:

```text
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

### JWT Payload Örneği

```json
{
  "user_id": 1,
  "exp": 1712345678
}
```

---

## 🔟 JWT Nasıl Çalışır?

```
Login (username/password)
   ↓
Server JWT üretir
   ↓
Client JWT saklar
   ↓
Request → Authorization: Bearer <JWT>
   ↓
Server JWT decode eder
   ↓
User bulunur
```

---

## 1️⃣1️⃣ JWT Avantaj & Dezavantaj

Avantaj:

- Stateless
- Scalable
- Mobile friendly
- Microservice friendly

Dezavantaj:

- Revoke zor
- Logout yönetimi zor

---

## 1️⃣2️⃣ JWT Token Türleri

### Access Token
- Kısa ömürlü (5–15 dk)

### Refresh Token
- Uzun ömürlü (günler)

Flow:

```
Access token expire
   ↓
Refresh token ile yeni access token alınır
```

---

## 1️⃣3️⃣ DRF JWT Örneği

Library:

```text
djangorestframework-simplejwt
```

settings.py:

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    )
}
```

---

## 1️⃣4️⃣ Protected Endpoint Örneği

```python
from rest_framework.permissions import IsAuthenticated
from rest_framework.viewsets import ModelViewSet

class ProductViewSet(ModelViewSet):

    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    permission_classes = [IsAuthenticated]
```

JWT olmadan:

```text
401 Unauthorized
```

JWT ile:

```text
200 OK
```

---

## 1️⃣5️⃣ Authentication vs Permission

Authentication:

> Kullanıcı kim?

Permission:

> Kullanıcı ne yapabilir?

---

### Permission Örneği

```python
from rest_framework.permissions import IsAdminUser

permission_classes = [IsAdminUser]
```

---

## 1️⃣6️⃣ Authentication Türleri Karşılaştırma

### Session Auth
- Web sitesi için ideal

### Token Auth
- Basit API için uygun

### JWT Auth
- Modern backend standardı

---

## 1️⃣7️⃣ Production Standard

Production’da genelde:

> JWT Authentication kullanılır

Sebep:

- Scalable
- Stateless
- Mobile friendly

---

## 1️⃣8️⃣ Authentication Lifecycle

```
Login request
   ↓
Credential validation
   ↓
Token / Session oluşturulur
   ↓
Client saklar
   ↓
Request → Authentication
   ↓
User belirlenir
```

---

## 1️⃣9️⃣ Security Best Practices

Production’da kritik konular:

- Password hash (Django otomatik yapar)
- HTTPS kullan
- Token expire süresi belirle
- Refresh token kullan
- SECRET_KEY’i koru
- Access token’ı localStorage yerine mümkünse HttpOnly cookie’de tut

---

## 2️⃣0️⃣ Mülakat Soruları

**Soru:** Authentication nedir?  
**Cevap:** Kullanıcının kimliğini doğrulama işlemidir.

**Soru:** Authorization nedir?  
**Cevap:** Kullanıcının yetkilerini belirleme işlemidir.

**Soru:** Session vs JWT farkı nedir?  
**Cevap:** Session stateful, JWT stateless’tir.

**Soru:** JWT neden kullanılır?  
**Cevap:** Stateless ve scalable authentication sağlar.

---

# Özet

Authentication Türleri:

### Session
Web için ideal

### Token
Basit API için

### JWT
Production standardı