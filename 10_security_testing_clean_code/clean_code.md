# BÖLÜM 3 — CLEAN CODE PRINCIPLES

Bu bölümde şunları öğreneceksin:

- Clean Code nedir?
- Neden kritiktir?
- Clean code prensipleri nelerdir?
- Bad code vs Clean code farkı
- Production seviyesinde clean architecture mantığı

Clean Code yaklaşımı büyük ölçüde :contentReference[oaicite:0]{index=0} (Uncle Bob) tarafından standartlaştırılmıştır.

Bu bölümün amacı: Sadece çalışan kod değil, **okunabilir, sürdürülebilir ve production-ready kod** yazmanı sağlamaktır.

---

# 1️⃣ Clean Code Nedir?

**Clean Code**, okunması, anlaşılması ve değiştirilmesi kolay koddur.

Clean code:

✔ Anlaşılır  
✔ Sürdürülebilir (maintainable)  
✔ Scalable  
✔ Test edilebilir  

## Clean Code’un Temel Felsefesi

> Kod bilgisayar için değil, insanlar için yazılır.

Çünkü:

- Kodun %90’ı okunur
- %10’u yazılır
- Kodu genellikle başkaları maintain eder
- 6 ay sonra sen de “başkası” olursun

---

## ❌ Bad Code Örneği

```python
def f(x, y):
    if x == 1:
        return y * 0.18
```

Bu kod:

- Anlaşılmaz
- Context yok
- Magic number içeriyor
- Maintain edilemez

---

## ✅ Clean Code Versiyonu

```python
TAX_RATE = 0.18

def calculate_tax(amount: float) -> float:
    return amount * TAX_RATE
```

Artık:

- Okunabilir
- Anlaşılır
- Test edilebilir
- Değiştirilebilir

---

# 2️⃣ Meaningful Names (Anlamlı İsimlendirme)

İsimler kodun yarısıdır.

## ❌ Kötü

```python
x = 5
```

## ✅ İyi

```python
user_count = 5
```

---

## ❌ Kötü

```python
def calc(x):
```

## ✅ İyi

```python
def calculate_total_price(price: float) -> float:
```

İsim:

- Amacı açıklamalı
- Context vermeli
- Kısaltma içermemeli (gereksiz yere)

---

# 3️⃣ Functions Small and Focused Olmalı

Bir function tek iş yapmalı.

## ❌ Kötü

```python
def process_user(user):
    validate(user)
    save(user)
    send_email(user)
    log(user)
```

Bu function:

- Çok fazla sorumluluk içeriyor
- Test edilmesi zor
- Değişiklik riski yüksek

---

## ✅ Daha Clean

```python
def process_user(user):
    validate_user(user)
    save_user(user)
    send_welcome_email(user)
```

Her function:

- Küçük olmalı
- Tek iş yapmalı
- Açıklama gerektirmemeli

Bu yaklaşım aynı zamanda **Single Responsibility Principle (SRP)** ile uyumludur.

---

# 4️⃣ DRY Principle (Don't Repeat Yourself)

Tekrar eden kod risklidir.

## ❌ Kötü

```python
total = price + price * 0.18
```

Başka yerde tekrar:

```python
total = amount + amount * 0.18
```

---

## ✅ Clean

```python
def calculate_tax(amount: float) -> float:
    return amount * TAX_RATE
```

Tek bir kaynak, tek bir değişiklik noktası.

---

# 5️⃣ Avoid Magic Numbers

Magic number = anlamı belli olmayan sabit değer.

## ❌ Kötü

```python
if age > 18:
```

## ✅ Clean

```python
LEGAL_AGE = 18

if age > LEGAL_AGE:
```

Kod artık kendini açıklar.

---

# 6️⃣ Single Responsibility Principle (Clean Code Perspektifi)

Her class tek bir sorumluluk taşımalı.

## ❌ Kötü

```python
class UserService:

    def save_user(self):
        pass

    def send_email(self):
        pass
```

Bu class:

- Hem veri katmanı
- Hem iletişim katmanı

sorumluluğu taşıyor.

---

## ✅ Clean

```python
class UserRepository:
    def save(self):
        pass


class EmailService:
    def send(self):
        pass
```

Sorumluluklar ayrıldı.

---

# 7️⃣ Avoid Deep Nesting

Derin if blokları okunabilirliği düşürür.

## ❌ Kötü

```python
if user:
    if user.is_active:
        if user.is_admin:
            return True
```

---

## ✅ Clean

```python
if not user:
    return False

if not user.is_active:
    return False

return user.is_admin
```

Early return yaklaşımı okunabilirliği artırır.

---

# 8️⃣ Write Self-Documenting Code

Kod açıklama gerektirmemeli.

## ❌ Kötü

```python
def p(u):
```

## ✅ Clean

```python
def process_user_registration(user):
```

Artık comment yazmaya gerek yok.

---

# 9️⃣ Avoid Large Classes

Büyük class = yüksek karmaşıklık.

## ❌ Kötü

```python
class UserManager:
    def save(self): pass
    def delete(self): pass
    def send_email(self): pass
    def validate(self): pass
    def calculate(self): pass
```

---

## ✅ Clean

- UserRepository
- UserValidator
- EmailService

Her class tek iş yapmalı.

---

# 🔟 Separation of Concerns

Katmanları ayır.

Örnek Django clean architecture:

```
View → Service → Repository → Database
```

---

## ❌ Bad (Fat View)

```python
def create_user(request):
    user = User.objects.create(...)
    send_email(user)
    log(user)
```

---

## ✅ Clean

```python
def create_user(request):
    user = user_service.create_user(request.data)
    return Response(user)
```

### Service Layer

```python
def create_user(data):
    user = user_repository.create(data)
    email_service.send_welcome_email(user)
    return user
```

Katmanlar ayrıldı:

- View → HTTP
- Service → Business logic
- Repository → Database

---

# 11️⃣ Avoid God Object

God object = her şeyi yapan class.

## ❌ Kötü

```python
class AppManager:
    pass
```

---

## ✅ Clean

- UserService
- OrderService
- AuthService

Her servis domain odaklı olmalı.

---

# 12️⃣ Write Testable Code

Dependency injection kullan.

## ❌ Kötü

```python
def create_user():
    db.save()
```

Test edilemez.

---

## ✅ Clean

```python
def create_user(repository):
    repository.save()
```

Artık mock edilebilir → test edilebilir.

---

# 13️⃣ Consistent Code Style

Python standardı:

:contentReference[oaicite:1]{index=1}

## Doğru

```python
user_name = "Salih"
```

## Yanlış

```python
UserName = "Salih"
```

Tutarlılık profesyonellik göstergesidir.

---

# 14️⃣ Clean Django Örneği

## ❌ Bad View

```python
def create_user(request):
    user = User.objects.create(...)
    send_email(user)
    return user
```

---

## ✅ Clean

```python
def create_user(request):
    user = user_service.create_user(request.data)
    return Response(user)
```

### Service Layer

```python
def create_user(data):
    user = User.objects.create(**data)
    email_service.send_welcome_email(user)
    return user
```

Business logic view içinde olmamalı.

---

# 15️⃣ Clean Code vs Bad Code

## Bad Code

- Anlaşılmaz
- Karmaşık
- Test edilemez
- Tekrar içerir
- Büyük class’lar barındırır

## Clean Code

- Readable
- Maintainable
- Scalable
- Testable
- Modüler

---

# Senior Engineer Mentality

Senior engineer şunu sorar:

> “Bu kod 2 yıl sonra da anlaşılabilir mi?”

Kod yazarken:

- Geleceği düşün
- Takım arkadaşlarını düşün
- Değişiklik ihtimalini düşün

---

# Production Seviyesi Clean Architecture

Örnek yapı:

```
project/
│
├── views/
├── services/
├── repositories/
├── models/
├── tests/
```

Bu yapı:

- Modüler
- Test edilebilir
- Scalable
- Büyük projelere uygun

---

# Clean Code Checklist

Kod yazarken kendine sor:

- İsimler anlamlı mı?
- Function küçük mü?
- Tekrar var mı?
- Magic number var mı?
- Test edilebilir mi?
- Katmanlar ayrılmış mı?
- Okunabilir mi?

---

# Bu Bölümün Özeti

Artık biliyorsun:

- Clean code prensipleri
- Readable code yazma teknikleri
- Maintainable architecture kurma
- Django’da clean katman ayrımı yapma
- Production seviyesinde code organization mantığı

Çalışan kod yazmak kolaydır.  
Temiz kod yazmak mühendisliktir.