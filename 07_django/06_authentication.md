| 06_authentication.md |

# BÖLÜM 14 — AUTHENTICATION (Kimlik Doğrulama ve Yetkilendirme)

Bu bölüm backend güvenliğinin temelidir. Production seviyesinde bir backend developer’ın authentication sistemlerini derinlemesine anlaması gerekir.

Bu bölümde öğreneceksin:

- Authentication nedir?
- Authorization nedir?
- Session authentication
- Token authentication
- JWT authentication
- OAuth2 / Social authentication
- Password hashing algoritmaları
- Token rotation / revocation
- Production’da hangisi neden kullanılır?
- Secure token storage best practices

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

---

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
- is_active
- last_login
- date_joined

---

## 4️⃣ Password Hashing (Şifre Hashleme)

Production sistemlerinde şifreler **asla plain text saklanmaz**.

Hashing = Tek yönlü şifreleme işlemidir.

Örnek:

```text
password: 123456

database:
pbkdf2_sha256$600000$abc123$hashed_value
```

Geri çevrilemez.

---

### Django Default Hash Algorithm

Django default:

```text
PBKDF2 + SHA256
```

Alternatif ve daha güçlü algoritma:

```text
Argon2 (önerilir)
```

Aktif etmek için:

```bash
pip install argon2-cffi
```

settings.py:

```python
PASSWORD_HASHERS = [
    'django.contrib.auth.hashers.Argon2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2PasswordHasher',
]
```

---

### Hash Verification

```python
from django.contrib.auth.hashers import check_password

check_password("123456", hashed_password)
```

---

## 5️⃣ Session Authentication

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

Logout:

```python
from django.contrib.auth import logout

logout(request)
```

---

## 6️⃣ Session Nerede Saklanır?

Server tarafında:

- Database (default)
- Cache
- Redis (production’da önerilir)
- File system

Client tarafında:

- Sadece session_id cookie saklanır

---

## 7️⃣ Session Authentication Avantaj & Dezavantaj

Avantaj:

- Çok güvenli
- Cookie HttpOnly olabilir
- Django ile native uyumlu

Dezavantaj:

- Mobile için uygun değil
- Stateless değildir
- Microservice mimaride zor scale edilir

---

## 8️⃣ Token Authentication

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

### DRF Token Authentication

```python
from rest_framework.authtoken.models import Token

token = Token.objects.create(user=user)
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

## 9️⃣ Token Authentication Flow

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

## 🔟 JWT (JSON Web Token)

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
  "exp": 1712345678,
  "iat": 1712340000
}
```

---

## 1️⃣1️⃣ JWT Nasıl Çalışır?

```
Login
   ↓
Server JWT üretir
   ↓
Client JWT saklar
   ↓
Request → Authorization: Bearer <JWT>
   ↓
Server JWT doğrular
   ↓
User bulunur
```

---

## 1️⃣2️⃣ JWT Token Türleri

Access Token:

- Kısa ömürlü (5–15 dk)

Refresh Token:

- Uzun ömürlü (günler / haftalar)

---

## 1️⃣3️⃣ Token Rotation ve Revocation

Problem:

JWT stateless olduğu için revoke etmek zordur.

Çözüm:

- Refresh token rotation
- Blacklist sistemi

Flow:

```
Refresh token kullanılır
   ↓
Yeni refresh token verilir
   ↓
Eski refresh token blacklist’e alınır
```

---

### SimpleJWT Blacklist

install:

```bash
pip install djangorestframework-simplejwt
```

settings.py:

```python
INSTALLED_APPS = (
    'rest_framework',
    'rest_framework.authtoken',
    'rest_framework_simplejwt.token_blacklist',
)
```

Logout:

```python
from rest_framework_simplejwt.tokens import RefreshToken

token = RefreshToken(refresh_token)
token.blacklist()
```

---

## 1️⃣4️⃣ DRF JWT Kurulumu

settings.py:

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    )
}
```

---

## 1️⃣5️⃣ Protected Endpoint

```python
from rest_framework.permissions import IsAuthenticated
from rest_framework.viewsets import ModelViewSet

class ProductViewSet(ModelViewSet):

    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    permission_classes = [IsAuthenticated]
```

---

## 1️⃣6️⃣ OAuth2 ve Social Authentication

OAuth2 = Third-party authentication standardıdır.

Örnek:

- Google login
- GitHub login
- Facebook login

Flow:

```
User → Google login
   ↓
Google → access token verir
   ↓
Backend → user authenticate eder
```

---

### Django allauth (Social Login)

install:

```bash
pip install django-allauth
```

Sağladıkları:

- Google login
- GitHub login
- Facebook login
- Email login

---

### Django OAuth Toolkit

OAuth2 server oluşturmanı sağlar.

install:

```bash
pip install django-oauth-toolkit
```

Kullanım:

- OAuth2 authorization server
- API authentication

---

## 1️⃣7️⃣ OpenID Connect, SSO, LDAP

Production enterprise sistemlerde kullanılır.

OpenID Connect:

- OAuth2 üzerine kuruludur
- Identity layer sağlar

SSO (Single Sign On):

- Tek login ile tüm sistemlere erişim

Örnek:

```text
Google account → tüm uygulamalar
```

LDAP:

- Kurumsal user authentication sistemi
- Active Directory ile kullanılır

---

## 1️⃣8️⃣ HttpOnly Cookie vs localStorage

Token saklama yeri çok kritiktir.

---

### localStorage (Riskli)

Problem:

- XSS saldırısına açık

Example attack:

```javascript
localStorage.getItem("access_token")
```

---

### HttpOnly Cookie (Önerilen)

Avantaj:

- JavaScript erişemez
- XSS’e karşı güvenli

Example:

```http
Set-Cookie: access_token=abc123; HttpOnly; Secure; SameSite=Strict
```

---

### Production Recommendation

En güvenli yöntem:

```
Access token → HttpOnly Cookie
Refresh token → HttpOnly Cookie
```

Alternatif:

```
Access token → memory
Refresh token → HttpOnly Cookie
```

Kaçınılması gereken:

```
Access token → localStorage
```

---

## 1️⃣9️⃣ Authentication vs Permission

Authentication:

> Kullanıcı kim?

Permission:

> Kullanıcı ne yapabilir?

---

Example:

```python
from rest_framework.permissions import IsAdminUser

permission_classes = [IsAdminUser]
```

---

## 2️⃣0️⃣ Authentication Türleri Karşılaştırma

Session Auth:

- Stateful
- Web için ideal

Token Auth:

- Basit API

JWT Auth:

- Stateless
- Production standardı

OAuth2:

- Social login
- Enterprise integration

---

## 2️⃣1️⃣ Production Standard

Modern production stack:

```text
JWT Authentication
+
Refresh Token Rotation
+
Blacklist
+
HttpOnly Cookie Storage
```

---

## 2️⃣2️⃣ Authentication Lifecycle

```
Login request
   ↓
Credential validation
   ↓
Token oluşturulur
   ↓
Client saklar
   ↓
Request gönderilir
   ↓
Authentication yapılır
   ↓
User belirlenir
```

---

## 2️⃣3️⃣ Security Best Practices

Production için kritik kurallar:

- Argon2 kullan
- HTTPS zorunlu kullan
- Access token short-lived yap
- Refresh token rotation kullan
- Token blacklist kullan
- SECRET_KEY gizli tut
- HttpOnly cookie kullan
- Token expiration kullan
- Rate limiting uygula

---

## 2️⃣4️⃣ Mülakat Soruları

Soru:

Authentication nedir?

Cevap:

Kullanıcının kimliğini doğrulama işlemidir.

---

Soru:

Authorization nedir?

Cevap:

Kullanıcının yetkilerini belirleme işlemidir.

---

Soru:

JWT neden kullanılır?

Cevap:

Stateless ve scalable authentication sağlar.

---

Soru:

JWT neden HttpOnly cookie’de saklanmalı?

Cevap:

XSS saldırılarına karşı koruma sağlar.

---

# Özet

Authentication yöntemleri:

Session  
→ Web uygulamaları

Token  
→ Basit API

JWT  
→ Modern production standardı

OAuth2  
→ Social login

En güvenli production setup:

```
JWT + Refresh Rotation + Blacklist + HttpOnly Cookie
```