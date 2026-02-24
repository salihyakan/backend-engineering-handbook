# Design Patterns Nedir?

**Design Pattern**, yazılım geliştirme sürecinde tekrar eden problemler için oluşturulmuş, test edilmiş ve kanıtlanmış **çözüm şablonlarıdır**.

> ⚠️ Önemli Nokta  
> Design pattern = hazır kod değildir  
> Design pattern = çözüm yaklaşımıdır  

---

## 🎯 Amaçları

- Yeniden kullanılabilirlik  
- Clean Architecture kurmak  
- Esneklik sağlamak  
- Maintainability (bakım kolaylığı)  
- Scalability (ölçeklenebilirlik)  

---

## Bu Dokümanda İşlenecek Patternler

- Singleton  
- Factory  
- Strategy  
- Observer  
- Adapter  
- Decorator  

---

# 1️⃣ Singleton Pattern

## 📌 Tanım

Bir class’tan yalnızca **tek bir instance** oluşturulmasını garanti eder.

Yani sistem boyunca sadece **1 obje** vardır.

---

## 🧠 Neden Gerekir?

Bazı sistem bileşenlerinin tek instance olması gerekir:

- Database connection manager  
- Cache manager  
- Logger  
- Config manager  

---

## ❌ Normal Class (Singleton Değil)

```python
class Database:

    def __init__(self):
        print("connection created")


db1 = Database()
db2 = Database()
```

👉 2 farklı instance oluşur.

---

## ✅ Singleton Implementation (Python)

```python
class Singleton:

    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance


s1 = Singleton()
s2 = Singleton()

print(s1 is s2)  # True
```

---

## 🔥 Django Gerçek Örnekleri

### Logger

```python
import logging

logger = logging.getLogger("myapp")
```

Aynı isimle çağrıldığında aynı logger döner → Singleton davranışı.

---

### settings

```python
from django.conf import settings
```

`settings` objesi uygulama boyunca tektir.

---

### Production Örneği: Redis Client

```python
import redis

class RedisClient:

    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = redis.Redis()
        return cls._instance
```

---

## ✅ Avantajları

- Memory tasarrufu  
- Global state yönetimi  

## ❌ Dezavantajları

- Test etmek zor olabilir  
- Global state risklidir  
- Coupling artırabilir  

---

# 2️⃣ Factory Pattern

## 📌 Tanım

Object oluşturma logic’ini merkezi bir yere taşır.

Amaç: Object creation sürecini kontrol altına almak.

---

## ❌ Problem (Factory Olmadan)

```python
if payment_type == "credit":
    payment = CreditCardPayment()

elif payment_type == "paypal":
    payment = PaypalPayment()
```

Bu kod birçok yerde tekrar eder.

---

## ✅ Factory Çözümü

```python
class PaymentFactory:

    @staticmethod
    def create_payment(payment_type):

        if payment_type == "credit":
            return CreditCardPayment()

        elif payment_type == "paypal":
            return PaypalPayment()
```

Kullanım:

```python
payment = PaymentFactory.create_payment("credit")
```

---

## 🔥 Django Gerçek Örnekleri

### Serializer Seçimi

```python
def get_serializer(format):

    if format == "json":
        return JsonSerializer()

    if format == "xml":
        return XmlSerializer()
```

---

### Cache Backend (Production)

```python
from django.core.cache import caches

cache = caches["default"]
```

Arka planda factory çalışır ve uygun backend (Redis, Memcached vb.) döndürülür.

---

## ✅ Avantajları

- Object creation merkezi olur  
- OCP uygulanabilir  
- Maintainability artar  

---

# 3️⃣ Strategy Pattern

## 📌 Tanım

Algoritmayı runtime’da değiştirmeyi sağlar.

---

## ❌ Problem

```python
def calculate_price(user, price):

    if user.type == "normal":
        return price

    elif user.type == "premium":
        return price * 0.8
```

Yeni kullanıcı tipi eklendikçe büyür → scalable değildir.

---

## ✅ Strategy Çözümü

### Strategy Interface

```python
class PricingStrategy:

    def calculate(self, price):
        pass
```

### Concrete Strategies

```python
class NormalPricing(PricingStrategy):

    def calculate(self, price):
        return price


class PremiumPricing(PricingStrategy):

    def calculate(self, price):
        return price * 0.8
```

### Context

```python
class PriceService:

    def __init__(self, strategy):
        self.strategy = strategy

    def get_price(self, price):
        return self.strategy.calculate(price)
```

Kullanım:

```python
strategy = PremiumPricing()
service = PriceService(strategy)

service.get_price(100)
```

---

## 🔥 Django Gerçek Örneği

### Authentication Backend

```python
AUTHENTICATION_BACKENDS = [
    "django.contrib.auth.backends.ModelBackend",
]
```

Backend değiştirilebilir → Strategy pattern.

---

# 4️⃣ Observer Pattern

## 📌 Tanım

Bir object değiştiğinde diğer object’leri otomatik bilgilendirir.

---

## 🌍 Gerçek Dünya Örneği

YouTube:

- Kanal video yükler  
- Subscriber’lara bildirim gider  

---

## Implementation

```python
class Observer:

    def update(self, data):
        pass


class Subject:

    def __init__(self):
        self.observers = []

    def subscribe(self, observer):
        self.observers.append(observer)

    def notify(self, data):
        for observer in self.observers:
            observer.update(data)
```

---

## 🔥 Django Gerçek Örneği: Signals

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User


@receiver(post_save, sender=User)
def user_created(sender, instance, created, **kwargs):

    if created:
        send_email(instance)
```

User oluşturulunca otomatik email gider → Observer pattern.

---

# 5️⃣ Adapter Pattern

## 📌 Tanım

Uyumsuz interface’leri uyumlu hale getirir.

---

## ❌ Problem

```python
class OldPayment:
    def make_payment(self):
        pass


class NewPayment:
    def pay(self):
        pass
```

Interface uyumsuz.

---

## ✅ Adapter Çözümü

```python
class PaymentAdapter:

    def __init__(self, payment):
        self.payment = payment

    def pay(self):
        return self.payment.make_payment()
```

---

## 🔥 Django Gerçek Örneği

### Storage Backend

`DEFAULT_FILE_STORAGE`

Local storage, S3 storage gibi farklı sistemler aynı interface’i kullanır → Adapter mantığı.

---

# 6️⃣ Decorator Pattern

## 📌 Tanım

Object’in davranışını değiştirmeden yeni özellik ekler.

Python decorator’ları Decorator pattern’ın doğrudan uygulamasıdır.

---

## Örnek

```python
def my_decorator(func):

    def wrapper():
        print("before")
        func()
        print("after")

    return wrapper


@my_decorator
def say_hello():
    print("hello")
```

---

## 🔥 Django Gerçek Örnekleri

### Login Required

```python
from django.contrib.auth.decorators import login_required


@login_required
def dashboard(request):
    pass
```

---

### Permission Decorator

```python
@permission_required("is_admin")
```

---

### Cache Decorator

```python
from django.views.decorators.cache import cache_page


@cache_page(60)
def view(request):
    pass
```

---

# 📌 Pattern Özet Tablosu

| Pattern   | Amaç |
|------------|--------|
| Singleton | Tek instance |
| Factory   | Object creation kontrolü |
| Strategy  | Algoritma değiştirme |
| Observer  | Event notification |
| Adapter   | Interface uyumu |
| Decorator | Davranış ekleme |

---

# 🔥 Django’da En Çok Kullanılan Patternler

En kritik olanlar:

- Factory  
- Strategy  
- Decorator  
- Observer  

---

# 🎯 Senior Seviyede Kritik Gerçek

Senior developer şunu bilir:

> Design pattern kullanma amacı pattern kullanmak değildir.  
> Amaç problemi doğru şekilde çözmektir.

---

## ❌ Yanlış Kullanım

- Pattern overengineering  
- Gereksiz abstraction  
- Basit problemi karmaşık hale getirmek  

## ✅ Doğru Kullanım

- Gerçek problem varsa pattern kullan  
- Ölçeklenebilirlik gerekiyorsa uygula  
- Kod karmaşıklaşmaya başladığında düşün  

---

# 🎤 Mülakatlarda En Çok Sorulan Sorular

Bu soruların gelme ihtimali çok yüksektir:

- Singleton nedir?  
- Factory nedir?  
- Strategy nedir?  
- Decorator nedir?  
- Django signals hangi pattern? (Observer)  
- Django decorators hangi pattern? (Decorator)  

---

Bu doküman mülakat seviyesinde hem teorik hem pratik anlayışı gösterecek şekilde hazırlanmıştır.