# BÖLÜM 3.1 — OOP Temelleri (Object Oriented Programming)

Object Oriented Programming (OOP), yazılımı gerçek dünyadaki nesneler gibi modellemeye yarayan bir programlama paradigmasıdır.

Python tamamen OOP tabanlıdır. Python’da:

- `int` bir object’tir  
- `str` bir object’tir  
- `list` bir object’tir  
- `function` bile bir object’tir  

**Her şey object’tir.**

---

## 1. Class Nedir

### Tanım

Class, object oluşturmak için kullanılan bir şablondur (blueprint).

> Class, object’lerin yapısını ve davranışlarını tanımlar.

### Gerçek Dünya Örneği

Bir class düşünelim: **Car (Araba)**

Bir arabanın:

**Attributes (özellikleri):**
- color  
- brand  
- speed  

**Methods (davranışları):**
- start()  
- stop()  
- accelerate()  

### Python Örneği

```python
class Car:
    pass
```

Bu bir class tanımıdır. Henüz hiçbir özellik veya davranış içermez.

### Naming Convention

Python standardı: **PascalCase**

```python
class User:
    pass

class BankAccount:
    pass
```

### Memory’de Ne Oluşur?

```python
<class '__main__.Car'>
```

Class bile bir object’tir.

---

## 2. Object Nedir

### Tanım

Object, bir class’ın instance’ıdır.

> Instance = class’tan üretilen gerçek nesne

### Örnek

```python
class Car:
    pass

car1 = Car()
car2 = Car()
```

- `Car` → class  
- `car1` → object  
- `car2` → object  

### Memory Modeli

```
Car (class)
  |
  ├── car1
  └── car2
```

### Object Oluşturma Süreci

```python
car = Car()
```

Python:

1. Memory allocate eder  
2. Object oluşturur  
3. Reference döndürür  

```python
print(type(car))
```

Output:

```
<class '__main__.Car'>
```

---

## 3. Attribute Nedir

Attribute, object’in sahip olduğu veridir.

> Object’in değişkenleridir.

```python
class Car:

    def __init__(self):
        self.color = "red"
        self.speed = 0
```

### Erişim

```python
car = Car()

print(car.color)
print(car.speed)
```

Output:

```
red
0
```

### Memory

```
car
 ├── color → red
 └── speed → 0
```

### Dynamic Attribute

```python
car.brand = "BMW"
```

Production’da önerilmez.

---

## 4. Method Nedir

Method, class içinde tanımlanan function’dır.

```python
class Car:

    def start(self):
        print("Car started")

    def stop(self):
        print("Car stopped")
```

### Kullanım

```python
car = Car()

car.start()
car.stop()
```

Output:

```
Car started
Car stopped
```

### Internal

```python
car.start
```

```
<bound method Car.start of <Car object>>
```

Method = function + object reference

---

## 5. Constructor (`__init__`)

Object oluşturulduğunda otomatik çalışır.

```python
class Car:

    def __init__(self):
        print("Car created")
```

```python
car = Car()
```

Output:

```
Car created
```

### Parametreli Constructor

```python
class Car:

    def __init__(self, brand, color):
        self.brand = brand
        self.color = color
```

```python
car = Car("BMW", "Black")

print(car.brand)
print(car.color)
```

Output:

```
BMW
Black
```

---

## 6. self Nedir

`self` object’in kendisini temsil eder.

```python
class Car:

    def set_speed(self, speed):
        self.speed = speed
```

```python
car = Car()
car.set_speed(100)
```

Python aslında şunu yapar:

```python
Car.set_speed(car, 100)
```

`self` bir keyword değildir ama değiştirilmemelidir.

### Örnek

```python
car1 = Car()
car2 = Car()

car1.set_speed(100)
car2.set_speed(200)
```

```
car1.speed → 100
car2.speed → 200
```

---

## 7. Full Örnek

```python
class Car:

    def __init__(self, brand, color):
        self.brand = brand
        self.color = color
        self.speed = 0

    def start(self):
        print(f"{self.brand} started")

    def accelerate(self, amount):
        self.speed += amount

    def get_speed(self):
        return self.speed
```

```python
car = Car("BMW", "Black")

car.start()
car.accelerate(50)

print(car.get_speed())
```

Output:

```
BMW started
50
```

---

## 8. Internal Memory Model

```
Car (class)
 |
 ├── methods
 |     ├── __init__
 |     ├── start
 |     ├── accelerate
 |     └── get_speed
 |
 └── car (object)
       ├── brand → BMW
       ├── color → Black
       └── speed → 50
```

Methods class’ta tutulur.  
Attributes object’te tutulur.

---

## 9. Class vs Object

| Class | Object |
|-------|--------|
| Blueprint | Instance |
| Template | Real entity |
| Tek tane tanım | Birden fazla olabilir |
| Davranışı tanımlar | Veriyi tutar |

---

## 10. Backend Örneği

```python
class User:

    def __init__(self, username, email):
        self.username = username
        self.email = email

    def get_email(self):
        return self.email
```

```python
user = User("salih", "salih@email.com")
```

Bu mantık Django ORM’in temelidir.

---

## 11. Mülakat Soruları

**Class nedir?**  
Object üretmek için kullanılan blueprint’tir.

**Object nedir?**  
Class’ın instance’ıdır.

**self nedir?**  
Object’in referansıdır.

**__init__ nedir?**  
Constructor method’dur.

**Attribute nedir?**  
Object’in verisini tutan değişkendir.

**Method nedir?**  
Class içinde tanımlanan function’dır.

---

## 12. Production Best Practices

1. Her class tek sorumluluk taşımalı (SRP)
2. Constructor object state initialize etmeli
3. self her instance method’da kullanılmalı
4. Class ve object kavramı net anlaşılmalı

---

Bu konu OOP’un temelidir.

Inheritance, Django Models, DRF, Services, Repository Pattern gibi advanced konular bunun üzerine kurulur.