# SOLID Principles (SOLID Prensipleri)

SOLID, nesne yönelimli yazılım tasarımında **esnek, sürdürülebilir, test edilebilir ve genişletilebilir** sistemler yazmak için kullanılan 5 temel prensiptir.

```
S → Single Responsibility Principle  
O → Open Closed Principle  
L → Liskov Substitution Principle  
I → Interface Segregation Principle  
D → Dependency Inversion Principle  
```

---

## 🎯 Amaçları

- Spaghetti code oluşmasını engellemek  
- Değişime dayanıklı sistemler kurmak  
- Test edilebilirliği artırmak  
- Maintainability (bakım kolaylığı) sağlamak  
- Extensibility (genişletilebilirlik) sağlamak  

---

# 1️⃣ Single Responsibility Principle (SRP)
## Tek Sorumluluk Prensibi

### 📌 Tanım

> Bir class’ın değişmesi için yalnızca **tek bir sebep** olmalıdır.  
> Yani bir class yalnızca **tek bir sorumluluk** taşımalıdır.

---

## ❌ Yanlış Örnek (SRP İhlali)

```python
class UserService:

    def create_user(self, username, password):
        pass

    def send_email(self, user):
        pass

    def log(self, message):
        pass
```

Bu class:

- User oluşturuyor  
- Email gönderiyor  
- Log yazıyor  

Bu nedenle:

- Email sistemi değişirse  
- Logging sistemi değişirse  
- User oluşturma mantığı değişirse  

class değişmek zorunda kalır.

👉 SRP ihlali.

---

## ✅ Doğru Tasarım

```python
class UserService:
    def create_user(self, username, password):
        pass


class EmailService:
    def send_email(self, user):
        pass


class LoggerService:
    def log(self, message):
        pass
```

Her class tek iş yapar.

---

## Django Gerçek Hayat Örneği

### ❌ Yanlış

```python
class OrderService:

    def create_order(self, data):
        order = Order.objects.create(**data)

        send_email(order.user)
        redis.delete("orders")
        logger.info("order created")

        return order
```

Bu class:

- Order oluşturuyor  
- Email gönderiyor  
- Cache temizliyor  
- Log yazıyor  

👉 Çoklu sorumluluk → SRP ihlali.

---

### ✅ Doğru

```python
class OrderService:
    def create_order(self, data):
        return Order.objects.create(**data)


class EmailService:
    def send_order_email(self, order):
        pass


class CacheService:
    def clear_orders(self):
        pass


class LoggerService:
    def log(self, message):
        pass
```

---

## SRP Avantajları

- Daha kolay test edilir  
- Daha az bağımlılık oluşur  
- Daha temiz mimari sağlar  
- Değişikliklerin etkisi minimize edilir  

---

# 2️⃣ Open Closed Principle (OCP)
## Açık / Kapalı Prensibi

### 📌 Tanım

> Yazılım varlıkları genişletmeye açık, değiştirmeye kapalı olmalıdır.

Yeni özellik eklerken mevcut kodu değiştirmemelisin.

---

## ❌ Yanlış Örnek

```python
class PaymentService:

    def pay(self, method):
        if method == "credit_card":
            print("credit card payment")
        elif method == "paypal":
            print("paypal payment")
```

Yeni ödeme yöntemi eklemek için class’ı değiştirmek gerekir.

👉 OCP ihlali.

---

## ✅ Doğru Tasarım

```python
from abc import ABC, abstractmethod

class PaymentMethod(ABC):

    @abstractmethod
    def pay(self):
        pass


class CreditCardPayment(PaymentMethod):
    def pay(self):
        print("credit card payment")


class PaypalPayment(PaymentMethod):
    def pay(self):
        print("paypal payment")


class PaymentService:
    def pay(self, payment_method: PaymentMethod):
        payment_method.pay()
```

Yeni yöntem eklemek:

```python
class BitcoinPayment(PaymentMethod):
    def pay(self):
        print("bitcoin payment")
```

👉 Mevcut kod değişmedi → OCP sağlandı.

---

# 3️⃣ Liskov Substitution Principle (LSP)

### 📌 Tanım

> Bir subclass, parent class’ın yerine geçebilmeli ve sistem davranışı bozulmamalıdır.

Child class:

- Parent’ın sözleşmesini bozmaz  
- Beklenen davranışı değiştirmez  
- Daha dar davranış sunmaz  

---

## ❌ Yanlış Örnek

```python
class Bird:
    def fly(self):
        print("flying")


class Penguin(Bird):
    def fly(self):
        raise Exception("penguins can't fly")
```

Penguin, Bird yerine kullanılamaz.

👉 LSP ihlali.

---

## ✅ Doğru Tasarım

```python
class Bird:
    pass


class FlyingBird(Bird):
    def fly(self):
        print("flying")


class Penguin(Bird):
    pass


class Eagle(FlyingBird):
    pass
```

Davranış ayrıştırıldı.

---

## Django Örneği (LSP İhlali)

```python
class FileStorage:
    def save(self, file):
        pass


class ReadOnlyStorage(FileStorage):
    def save(self, file):
        raise Exception("cannot save")
```

Parent save edebiliyor ama child edemiyor.

👉 LSP ihlali.

---

# 4️⃣ Interface Segregation Principle (ISP)

### 📌 Tanım

> Class’lar ihtiyaç duymadıkları method’lara bağımlı olmamalıdır.

Büyük interface’ler yerine küçük ve özelleşmiş interface’ler tercih edilmelidir.

---

## ❌ Yanlış Tasarım

```python
class Worker:
    def work(self):
        pass

    def eat(self):
        pass


class Robot(Worker):
    def eat(self):
        raise Exception("robots don't eat")
```

Robot gereksiz method’a bağımlı.

👉 ISP ihlali.

---

## ✅ Doğru Tasarım

```python
class Workable:
    def work(self):
        pass


class Eatable:
    def eat(self):
        pass


class Human(Workable, Eatable):
    pass


class Robot(Workable):
    pass
```

---

# 5️⃣ Dependency Inversion Principle (DIP)

## 📌 Tanım

> High-level modüller low-level modüllere bağımlı olmamalıdır.  
> İkisi de abstraction’a bağımlı olmalıdır.

---

## ❌ Yanlış Örnek

```python
class MySQLDatabase:
    def save(self, data):
        pass


class UserService:
    def __init__(self):
        self.db = MySQLDatabase()

    def create_user(self, data):
        self.db.save(data)
```

UserService doğrudan MySQL’e bağlıdır.

Database değişirse service değişir.

👉 DIP ihlali.

---

## ✅ Doğru Tasarım

```python
from abc import ABC, abstractmethod

class Database(ABC):

    @abstractmethod
    def save(self, data):
        pass


class MySQLDatabase(Database):
    def save(self, data):
        pass


class PostgreSQLDatabase(Database):
    def save(self, data):
        pass


class UserService:
    def __init__(self, db: Database):
        self.db = db

    def create_user(self, data):
        self.db.save(data)
```

Kullanım:

```python
db = MySQLDatabase()
service = UserService(db)
```

veya

```python
db = PostgreSQLDatabase()
service = UserService(db)
```

👉 UserService değişmedi → DIP sağlandı.

---

# 🔥 Django'da SOLID Nerelerde Var?

- Django ORM abstraction  
- Cache backend abstraction  
- Authentication backend sistemi  
- Storage backend sistemi  
- Middleware yapısı  
- Database backend mimarisi  

Django’nun mimarisi abstraction ve extensibility üzerine kuruludur.

---

# 📌 SOLID Özet Tablosu

| Principle | Amaç |
|------------|--------|
| SRP | Class tek iş yapmalı |
| OCP | Değiştirmeden genişletilebilir olmalı |
| LSP | Subclass parent yerine geçebilmeli |
| ISP | Gereksiz method bağımlılığı olmamalı |
| DIP | Somuta değil abstraction’a bağımlı ol |

---

# 🎯 Senior Seviyede Kritik Çıkarımlar

- Inheritance her zaman doğru değildir  
- Composition çoğu zaman daha güçlüdür  
- Abstraction kritik önemdedir  
- Dependency Injection bilinmelidir  
- Test yazılabiliyorsa mimari doğrudur  
- SOLID = Clean Architecture temelidir  

---

Bu doküman mülakat seviyesinde hem teorik hem pratik anlayışı gösterecek şekilde hazırlanmıştır.