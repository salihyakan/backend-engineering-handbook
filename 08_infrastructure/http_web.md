# 1. HTTP Nedir?

## Tanım

HTTP (HyperText Transfer Protocol), istemci (client) ile sunucu (server) arasında veri iletişimini sağlayan bir protokoldür.

**Basit tanım:**

> HTTP = Tarayıcı ile sunucu arasındaki iletişim dili

---

## Gerçek Hayatta Örnek

Tarayıcıya yazdın:

```
https://example.com/users
```

Tarayıcı şunu yapar:

```
Client (browser)
    ↓ HTTP Request
Server (backend)
    ↓ HTTP Response
Client (browser)
```

---

## Django Örneği

### Frontend:

```
GET /users/
```

### Django:

```python
def users(request):
    return JsonResponse({"users": []})
```

---

## HTTP Özellikleri

| Özellik | Açıklama |
|----------|-----------|
| Stateless | Her request bağımsızdır |
| Client-server | İstemci ve sunucu ayrıdır |
| Text based | Okunabilir metindir |
| Request-response model | Soru-cevap modeli |

---

# 2. Request Nedir?

## Tanım

Request, client’ın server’a gönderdiği istektir.

---

## İçeriği

Bir HTTP request şunlardan oluşur:

```
METHOD PATH VERSION
HEADERS

BODY
```

---

## Gerçek HTTP Request Örneği

```
GET /users HTTP/1.1
Host: example.com
User-Agent: Chrome
Authorization: Bearer 123abc
```

---

## HTTP Method Nedir?

Method = Ne yapmak istediğin

| Method | Anlam |
|---------|--------|
| GET | Veri al |
| POST | Veri oluştur |
| PUT | Veri güncelle (tam) |
| PATCH | Veri güncelle (kısmi) |
| DELETE | Veri sil |

---

## Django Request Örneği

```python
def users(request):
    print(request.method)  # GET
    print(request.headers)
    print(request.body)

    return JsonResponse({})
```

---

## POST Request Örneği

```
POST /users
Content-Type: application/json

{
  "name": "Ali"
}
```

### Django:

```python
import json

def create_user(request):
    data = json.loads(request.body)
    name = data["name"]

    User.objects.create(name=name)

    return JsonResponse({"status": "created"})
```

---

# 3. Response Nedir?

## Tanım

Response, server’ın client’a verdiği cevaptır.

---

## İçeriği

```
STATUS CODE
HEADERS

BODY
```

---

## Örnek Response

```
HTTP/1.1 200 OK
Content-Type: application/json

{
  "name": "Ali"
}
```

---

## Django Response Örneği

```python
return JsonResponse({
    "name": "Ali"
})
```

---

## Response Lifecycle

```
Client → request → Django view → response → Client
```

---

# 4. Headers Nedir?

## Tanım

Headers = Request ve response hakkında metadata

Metadata = Veri hakkında veri

---

## Request Headers Örneği

```
Authorization: Bearer token123
Content-Type: application/json
User-Agent: Chrome
```

---

## Response Headers Örneği

```
Content-Type: application/json
Content-Length: 123
Cache-Control: no-cache
```

---

## En Önemli Headers

### Authorization

Auth için kullanılır:

```
Authorization: Bearer token123
```

Django:

```python
token = request.headers.get("Authorization")
```

---

### Content-Type

Veri tipi belirtir:

```
application/json
text/html
multipart/form-data
```

---

### Accept

Client ne bekliyor:

```
Accept: application/json
```

---

### Cache-Control

Cache kontrolü:

```
Cache-Control: no-cache
```

---

# 5. Status Codes

Status code = Server sonucu

---

## Status Code Kategorileri

| Range | Anlam |
|--------|--------|
| 200–299 | Başarı |
| 300–399 | Redirect |
| 400–499 | Client error |
| 500–599 | Server error |

---

## En Önemli Status Code’lar

### 200 OK

Başarılı

```python
return JsonResponse(data, status=200)
```

---

### 201 Created

Oluşturuldu

```python
return JsonResponse(data, status=201)
```

---

### 204 No Content

Response body yok

```python
return HttpResponse(status=204)
```

---

### 400 Bad Request

Client hatası

```python
return JsonResponse({"error": "invalid"}, status=400)
```

---

### 401 Unauthorized

Login gerekli

---

### 403 Forbidden

Yetki yok

---

### 404 Not Found

Bulunamadı

```python
get_object_or_404(User, id=id)
```

---

### 500 Internal Server Error

Server hatası

---

# 6. REST Nedir?

## Tanım

REST = Representational State Transfer

Web API tasarım standardıdır.

---

## Amaç

API’leri standart şekilde tasarlamak

---

## REST Mantığı

Her şey bir resource’dur.

Örnek:

- User
- Product
- Order

---

## REST Endpoint Örnekleri

```
GET /users
GET /users/1
POST /users
PUT /users/1
DELETE /users/1
```

---

## CRUD Mapping

| İşlem | HTTP Method |
|--------|--------------|
| Create | POST |
| Read | GET |
| Update | PUT / PATCH |
| Delete | DELETE |

---

## Django REST Framework Örneği

```python
class UserViewSet(ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

Otomatik üretir:

```
GET /users
GET /users/1
POST /users
PUT /users/1
DELETE /users/1
```

---

# 7. REST Prensipleri

## 1. Stateless

Server request’i hatırlamaz.

Her request auth içermeli:

```
Authorization: Bearer token
```

---

## 2. Resource Based

Endpoint resource’a göre olur.

Doğru:

```
/users
/products
```

Yanlış:

```
/getUsers
```

---

## 3. HTTP Methods Doğru Kullanılır

Yanlış:

```
POST /getUsers
```

Doğru:

```
GET /users
```

---

## 4. Standard Status Codes Kullanılır

- 200 OK
- 201 Created
- 404 Not Found

---

# 8. Gerçek Hayat Akışı (Tam Lifecycle)

Frontend:

```
GET /users/1
Authorization: Bearer token
```

↓

Django:

```python
def get_user(request, id):
    user = User.objects.get(id=id)

    return JsonResponse({
        "name": user.name
    })
```

↓

Response:

```
200 OK

{
  "name": "Ali"
}
```

---

# 9. HTTP Olmadan Django Çalışmaz

Django’nun temeli:

```
HTTP request → Django → HTTP response
```

Core flow:

```
Browser
 ↓
HTTP request
 ↓
Django URL dispatcher
 ↓
View
 ↓
Response
 ↓
Browser
```

---

# 10. Django'da Request-Response Cycle

```
Client
 ↓
Middleware
 ↓
URL
 ↓
View
 ↓
Response
 ↓
Middleware
 ↓
Client
```

---

# Senior Seviyede Bilmen Gereken Kritik Noktalar

## Bilmen Gereken HTTP Kavramları

- Request
- Response
- Headers
- Methods
- Status codes
- REST
- Stateless
- Authentication headers
- Content-Type

---

## Bilmen Gereken REST Kavramları

- Resource
- Endpoint
- CRUD mapping
- Stateless
- Proper status code

---

# Mülakat Soruları

## Soru

HTTP stateless ne demek?

### Cevap:

Server request’ler arasında state tutmaz. Her request bağımsızdır.

---

## Soru

GET vs POST farkı?

### Cevap:

GET → Veri alır  
POST → Veri oluşturur

---

## Soru

REST nedir?

### Cevap:

HTTP üzerine kurulu API tasarım standardıdır.

---

## Soru

404 vs 500 farkı?

### Cevap:

404 → Client hatası  
500 → Server hatası

---

# Özet

**HTTP:**  
Web iletişim protokolü

**Request:**  
Client → Server

**Response:**  
Server → Client

**Headers:**  
Metadata

**Status codes:**  
Sonuç

**REST:**  
API design standardı