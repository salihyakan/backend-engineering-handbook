# BÖLÜM 3.2 — OOP Concepts

Bu bölümün temel prensipleri:

- Encapsulation  
- Inheritance  
- Polymorphism  
- Abstraction  

Bu dört kavram birlikte OOP’un temelini oluşturur.

---

# 1. Encapsulation (Kapsülleme)

## Tanım

Encapsulation, veriyi ve o veriye erişimi tek bir yapı içinde toplama ve dışarıdan kontrolsüz erişimi engelleme prensibidir.

## Amaç

- Veri güvenliği  
- Kontrol  
- Modülerlik  

## Gerçek Dünya Örneği

Bir banka hesabı düşün:

Şunu yapamazsın:

```python
account.balance = -1000000
```

Bu tehlikelidir.

Onun yerine:

```python
account.withdraw(100)
```

gibi kontrollü bir method kullanılır.

---

## Encapsulation Olmadan

```python
class BankAccount:

    def __init__(self, balance):
        self.balance = balance
```

```python
account = BankAccount(1000)
account.balance = -5000  # Tehlikeli
```

---

## Encapsulation ile Çözüm

```python
class BankAccount:

    def __init__(self, balance):
        self.__balance = balance   # private attribute

    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount

    def withdraw(self, amount):
        if amount <= self.__balance:
            self.__balance -= amount

    def get_balance(self):
        return self.__balance
```

### Kullanım

```python
account = BankAccount(1000)

account.deposit(500)

print(account.get_balance())
```

Output:

```
1500
```

---

## Private Attribute Nedir?

```python
self.__balance
```

Double underscore → private

Python bunu şuna çevirir:

```
_BankAccount__balance
```

Buna **name mangling** denir.

Amaç: Direkt erişimi engellemek

```python
account.__balance  # ❌
```

---

## Mülakat Cevabı (Kısa)

Encapsulation, veri ve methodları tek yapı içinde toplayarak veri güvenliğini sağlamaktır.

---

# 2. Inheritance (Kalıtım)

## Tanım

Inheritance, bir class’ın başka bir class’ın özelliklerini miras almasıdır.

## Amaç

- Code reuse  
- Daha temiz mimari  

## Gerçek Dünya Örneği

```
Vehicle → Parent
Car     → Child
Bike    → Child
```

Hepsi:

- start()  
- stop()  

özelliklerini paylaşır.

---

## Python Örneği

### Parent Class

```python
class Animal:

    def speak(self):
        print("Animal speaks")
```

### Child Class

```python
class Dog(Animal):
    pass
```

### Kullanım

```python
dog = Dog()
dog.speak()
```

Output:

```
Animal speaks
```

Dog, speak method’unu miras aldı.

---

## Override (Method Ezme)

```python
class Dog(Animal):

    def speak(self):
        print("Dog barks")
```

```python
dog = Dog()
dog.speak()
```

Output:

```
Dog barks
```

---

## super() Kullanımı

Parent constructor çağırmak için:

```python
class Animal:

    def __init__(self, name):
        self.name = name


class Dog(Animal):

    def __init__(self, name):
        super().__init__(name)
```

---

## Mülakat Cevabı

Inheritance, bir class’ın başka bir class’ın özelliklerini miras almasıdır.

---

# 3. Polymorphism (Çok Biçimlilik)

## Tanım

Polymorphism, aynı method’un farklı davranışlar göstermesidir.

---

## Gerçek Dünya Örneği

`start()` method’u:

- Car → motor çalışır  
- Computer → sistem açılır  

Aynı isim, farklı davranış.

---

## Python Örneği

```python
class Dog:

    def speak(self):
        print("Bark")


class Cat:

    def speak(self):
        print("Meow")
```

```python
animals = [Dog(), Cat()]

for animal in animals:
    animal.speak()
```

Output:

```
Bark
Meow
```

Aynı method → farklı davranış.

---

## Backend Gerçek Örnek

```python
class Payment:

    def pay(self):
        pass


class CreditCard(Payment):

    def pay(self):
        print("Credit card payment")


class PayPal(Payment):

    def pay(self):
        print("PayPal payment")
```

```python
payments = [CreditCard(), PayPal()]

for p in payments:
    p.pay()
```

Production sistemlerde sürekli kullanılır.

---

## Mülakat Cevabı

Polymorphism, aynı interface’in farklı davranışlar göstermesidir.

---

# 4. Abstraction (Soyutlama)

## Tanım

Abstraction, gereksiz detayları gizleyip sadece gerekli kısmı göstermektir.

---

## Gerçek Dünya Örneği

Araba kullanırken motorun nasıl çalıştığını bilmezsin.

Sadece:

- start()  
- drive()  

kullanırsın.

Detay gizlidir.

---

## Python’da Abstraction

Abstract class kullanılır.

```python
from abc import ABC, abstractmethod


class Payment(ABC):

    @abstractmethod
    def pay(self):
        pass
```

### Child Class

```python
class CreditCard(Payment):

    def pay(self):
        print("Credit card payment")
```

### Kullanım

```python
p = CreditCard()
p.pay()
```

Abstract class’tan direkt object oluşturamazsın:

```python
p = Payment()  # ❌
```

Error verir.

---

## Amaç

- Interface zorunlu kılmak  
- Tutarlı mimari oluşturmak  

---

# 5. Tüm Kavramların Birlikte Kullanımı

```python
from abc import ABC, abstractmethod


class Animal(ABC):

    def __init__(self, name):
        self.__name = name  # encapsulation

    @abstractmethod
    def speak(self):
        pass


class Dog(Animal):

    def speak(self):
        return "Bark"


class Cat(Animal):

    def speak(self):
        return "Meow"
```

### Kullanım

```python
animals = [Dog("Karabas"), Cat("Minnak")]

for animal in animals:
    print(animal.speak())
```

Burada:

- Encapsulation → `__name`  
- Inheritance → `Dog(Animal)`  
- Polymorphism → `speak()` farklı davranır  
- Abstraction → `@abstractmethod`  

---

# 6. Mülakat Özeti (Ezberlenmesi Gereken)

**Encapsulation**  
→ Veri gizleme  

**Inheritance**  
→ Class mirası  

**Polymorphism**  
→ Aynı method farklı davranış  

**Abstraction**  
→ Detay gizleme, interface zorunluluğu  

---

# 7. Backend & Django’da Gerçek Kullanım

- Django Model → Inheritance kullanır  
- DRF Serializer → Abstraction kullanır  
- Payment system → Polymorphism kullanır  
- Private variables → Encapsulation kullanır  

Bu dört prensip, Django ORM, DRF, Service Layer, Repository Pattern ve Clean Architecture gibi ileri seviye mimarilerin temelini oluşturur.