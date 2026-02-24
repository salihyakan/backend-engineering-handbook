# BÖLÜM 3.5 — Advanced OOP

## İçerik

- Multiple Inheritance
- MRO (Method Resolution Order)
- Mixins
- Abstract Base Classes (Production Bakış Açısıyla)

---

# 1️⃣ Multiple Inheritance (Çoklu Kalıtım)

## Tanım

Bir class’ın birden fazla parent class’tan miras almasıdır.

```python
class A:
    def method_a(self):
        print("A")


class B:
    def method_b(self):
        print("B")


class C(A, B):
    pass
```

Kullanım:

```python
obj = C()
obj.method_a()
obj.method_b()
```

C hem A hem B’nin özelliklerini aldı.

---

## Neden Kullanılır?

- Code reuse
- Behavior eklemek
- Mixins kullanmak
- Cross-cutting concern eklemek (logging, permission vb.)

---

## ⚠ Diamond Problem

```python
class A:
    def speak(self):
        print("A")


class B(A):
    pass


class C(A):
    pass


class D(B, C):
    pass
```

```python
d = D()
d.speak()
```

Python hangi `A`'yı çağırır?  
Cevap: MRO belirler.

---

# 2️⃣ MRO (Method Resolution Order)

## Tanım

Bir method çağrıldığında Python’un hangi class’ta arama yapacağını belirleyen sıradır.

---

## MRO Görüntüleme

```python
print(D.__mro__)
print(D.mro())
```

---

## Örnek

```python
class A:
    pass

class B(A):
    pass

class C(A):
    pass

class D(B, C):
    pass
```

MRO:

D → B → C → A → object

---

## Kullanılan Algoritma

Python **C3 Linearization** algoritmasını kullanır.

Özellikleri:

- Deterministik
- Tutarlı
- Diamond problem’i çözer

---

## Kritik Örnek

```python
class A:
    def speak(self):
        print("A")


class B(A):
    def speak(self):
        print("B")


class C(A):
    def speak(self):
        print("C")


class D(B, C):
    pass
```

```python
d = D()
d.speak()
```

Output:

B

Sebep:

D → B → C → A → object

---

## super() Neden Önemli?

❌ Yanlış:

```python
A.__init__(self)
```

✅ Doğru:

```python
super().__init__()
```

`super()` MRO sırasına göre çalışır.

---

# 3️⃣ Mixins

## Tanım

Tek başına kullanılmaz.  
Başka class’lara davranış eklemek için yazılır.

---

## Özellikleri

- Küçük olmalı
- Single Responsibility
- Constructor içermemeli
- Genelde state tutmamalı
- İsmi genelde `Mixin` ile biter

---

## Örnek

```python
class LoggerMixin:
    def log(self, message):
        print(f"[LOG]: {message}")
```

```python
class User(LoggerMixin):
    pass

u = User()
u.log("User created")
```

---

## Django Gerçek Hayat

- LoginRequiredMixin
- PermissionRequiredMixin

Mixin → davranış ekler.

---

## Mixin vs Inheritance

Mixin:

- is-a değildir
- has behavior’dır

---

# 4️⃣ Abstract Base Classes (ABC)

## Tanım

Interface zorunluluğu sağlayan base class’tır.

---

## Örnek

```python
from abc import ABC, abstractmethod


class Payment(ABC):

    @abstractmethod
    def pay(self, amount):
        pass
```

```python
class CreditCard(Payment):

    def pay(self, amount):
        print(f"Paid {amount} via Credit Card")
```

Şu çalışmaz:

```python
p = Payment()
```

TypeError verir.

---

## Production Kullanım

- Payment systems
- Notification systems
- Repository pattern
- Service layer

---

## Backend Örnek

```python
class NotificationService(ABC):

    @abstractmethod
    def send(self, message):
        pass


class EmailService(NotificationService):
    def send(self, message):
        print("Email sent")


class SMSService(NotificationService):
    def send(self, message):
        print("SMS sent")
```

---

# 5️⃣ Kombinasyon Örneği

```python
from abc import ABC, abstractmethod


class LoggerMixin:
    def log(self, msg):
        print(msg)


class Service(ABC):

    @abstractmethod
    def execute(self):
        pass


class OrderService(LoggerMixin, Service):

    def execute(self):
        self.log("Order executed")
```

Bu yapı:

- Multiple inheritance
- MRO
- Mixin
- ABC
- Polymorphism

---

# 6️⃣ Mülakat Soruları

**Multiple inheritance nedir?**  
Bir class’ın birden fazla parent’tan miras almasıdır.

**MRO nedir?**  
Python’un method arama sırasıdır. C3 linearization kullanılır.

**Mixin nedir?**  
Başka class’lara davranış eklemek için yazılan küçük yardımcı class’tır.

**Abstract Base Class nedir?**  
Interface zorunluluğu sağlayan base class’tır.

---

# 7️⃣ Production Uyarıları

- Multiple inheritance dikkatli kullanılmalı
- Diamond problem bilinmeli
- super() mutlaka kullanılmalı
- Mixin’ler küçük ve stateless olmalı
- ABC’ler interface gibi tasarlanmalı
- Composition çoğu zaman inheritance’tan daha sağlıklıdır