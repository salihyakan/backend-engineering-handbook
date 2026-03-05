# 1.3 Functions (Fonksiyonlar)

Fonksiyonlar programlamanın **en önemli soyutlama araçlarından biridir**.

İyi yazılmış fonksiyonlar:

- Tekrar eden kodu soyutlar
- Mantığı izole eder
- Kod tekrarını azaltır (**DRY – Don't Repeat Yourself**)
- Test edilebilirliği artırır
- Büyük projelerde modüler yapı oluşturur
- Clean Architecture’ın temelini oluşturur

Backend geliştirmede fonksiyon tasarımı doğru yapılmazsa:

- kod karmaşıklaşır
- test yazmak zorlaşır
- hatalar artar
- kod bakımı zorlaşır

---

# 1️⃣ Function Nedir?

Fonksiyon, belirli bir işi yapan ve **çağrıldığında çalışan kod bloğudur**.

Basitçe:

```
Fonksiyon = tekrar kullanılabilir kod bloğu
```

---

# Basit Örnek

```python
def greet():
    print("Merhaba")
```

Fonksiyon çağrısı:

```python
greet()
```

Çıktı:

```
Merhaba
```

---

# Fonksiyon Neden Kullanılır?

Fonksiyonlar şu problemleri çözer:

### 1️⃣ Kod tekrarını azaltır

Kötü kod:

```python
print("Hoşgeldiniz Ali")
print("Hoşgeldiniz Ayşe")
print("Hoşgeldiniz Mehmet")
```

Fonksiyon ile:

```python
def greet(name):
    print("Hoşgeldiniz", name)

greet("Ali")
greet("Ayşe")
greet("Mehmet")
```

---

### 2️⃣ Kod okunabilirliğini artırır

```python
process_payment()
send_email()
create_invoice()
```

Bu fonksiyon isimleri kodun ne yaptığını açıklar.

---

# 2️⃣ return Nedir?

Fonksiyon bir değer üretmek istiyorsa **return kullanılır**.

```python
def add(a, b):
    return a + b
```

Fonksiyon çağrısı:

```python
result = add(3, 5)
print(result)
```

Çıktı:

```
8
```

---

# return Nasıl Çalışır?

`return` iki şey yapar:

1️⃣ Fonksiyonu **hemen sonlandırır**  
2️⃣ Bir değer döndürür

Örnek:

```python
def test():

    print("Başladı")

    return 5

    print("Burası çalışmaz")
```

Çıktı:

```
Başladı
```

---

# return Olmazsa Ne Olur?

Python varsayılan olarak `None` döndürür.

```python
def test():
    pass

print(test())
```

Çıktı:

```
None
```

---

# return vs print

Yeni başlayanların sık yaptığı hata.

### print

Sadece ekrana yazar.

```python
def add(a, b):
    print(a + b)
```

### return

Değer üretir.

```python
def add(a, b):
    return a + b
```

Return kullanmazsak:

```python
result = add(3,5)
```

result:

```
None
```

---

# 3️⃣ Parameters ve Arguments

Bu iki kavram genelde karıştırılır.

### Parameter

Fonksiyon tanımındaki değişken.

```python
def greet(name):
```

`name` → parameter

---

### Argument

Fonksiyon çağrılırken verilen değer.

```python
greet("Ali")
```

`"Ali"` → argument

---

# 4️⃣ Positional vs Keyword Arguments

## Positional Argument

Sırasına göre eşleşir.

```python
def divide(a, b):
    return a / b

divide(10, 2)
```

Eşleşme:

```
a = 10
b = 2
```

---

## Keyword Argument

Parametre adı belirtilir.

```python
divide(a=10, b=2)
```

Avantajları:

- daha okunabilir
- parametre sırası önemli değildir

---

## Karışık Kullanım

```python
divide(10, b=2)
```

Ama şu kullanım hatalıdır:

```python
divide(a=10, 2)
```

Sebep:

```
Keyword argument'tan sonra positional argument kullanılamaz
```

---

# 5️⃣ Default Parameters

Parametrelere varsayılan değer verilebilir.

```python
def greet(name="Misafir"):
    print("Merhaba", name)
```

Çağrı:

```python
greet()
```

Çıktı:

```
Merhaba Misafir
```

---

# 6️⃣ Mutable Default Parameter Problemi

Python mülakatlarının klasik sorusudur.

---

## Problem

```python
def add_item(item, my_list=[]):

    my_list.append(item)

    return my_list
```

Çağrı:

```python
print(add_item(1))
print(add_item(2))
```

Beklenen:

```
[1]
[2]
```

Gerçek:

```
[1]
[1,2]
```

---

# Neden Böyle Olur?

Python default parametreleri:

```
fonksiyon tanımlanırken oluşturur
```

yani:

```
my_list = []
```

sadece **bir kez oluşturulur**.

---

# Doğru Çözüm

```python
def add_item(item, my_list=None):

    if my_list is None:
        my_list = []

    my_list.append(item)

    return my_list
```

Bu yaklaşım **her çağrıda yeni liste oluşturur**.

---

# 7️⃣ *args ve **kwargs

Fonksiyonun **değişken sayıda parametre almasını sağlar**.

---

# *args

Tuple olarak gelir.

```python
def sum_all(*args):

    return sum(args)
```

Çağrı:

```python
sum_all(1,2,3,4)
```

args değeri:

```
(1,2,3,4)
```

---

# **kwargs

Dictionary olarak gelir.

```python
def print_info(**kwargs):

    print(kwargs)
```

Çağrı:

```python
print_info(name="Ali", age=25)
```

kwargs:

```
{"name": "Ali", "age": 25}
```

---

# Backend Gerçek Kullanım

Django ORM:

```python
User.objects.create(**data)
```

data:

```
{
"name":"Ali",
"age":25
}
```

---

# 8️⃣ Recursion (Özyineleme)

Fonksiyonun **kendini çağırmasıdır**.

---

# Faktöriyel Örneği

```python
def factorial(n):

    if n == 1:
        return 1

    return n * factorial(n-1)
```

Çalışma:

```
factorial(3)

3 * factorial(2)

3 * (2 * factorial(1))

3 * (2 * 1)

6
```

---

# Recursion İçin 2 Kural

1️⃣ Base case olmalı  
2️⃣ Problem küçülmeli

---

# Recursion Nerelerde Kullanılır?

- Tree traversal
- Graph traversal
- Nested yorum sistemi
- Dosya sistemi tarama

---

# Recursion Riskleri

- Stack overflow
- Recursion depth limit

Python default limit:

```
~1000
```

---

# 9️⃣ Pure Function

Pure function iki özelliğe sahiptir:

1️⃣ Aynı input → aynı output  
2️⃣ Side effect yoktur

---

# Pure Function Örneği

```python
def add(a,b):
    return a+b
```

---

# Pure Olmayan Fonksiyon

```python
x = 10

def add(a):
    return a + x
```

Global değişken kullanıldığı için pure değildir.

---

# 🔟 Side Effects

Fonksiyonun dış dünyayı değiştirmesidir.

Örnekler:

- dosya yazma
- database kayıt
- email gönderme
- global değişken değiştirme
- print

```python
def add_item(lst):

    lst.append(5)
```

Bu fonksiyon dış listeyi değiştirir.

---

# Backend Side Effect Örnekleri

- DB update
- Email gönderme
- Cache yazma
- Log yazma

---

# 1️⃣1️⃣ First Class Functions

Python'da fonksiyonlar **nesne gibi davranır**.

Yani:

- değişkene atanabilir
- parametre olabilir
- başka fonksiyondan dönebilir

---

# Fonksiyon Atama

```python
def greet():
    return "Merhaba"

hello = greet

print(hello())
```

---

# Fonksiyon Parametre Olarak

```python
def operate(func, a, b):

    return func(a,b)
```

---

```python
def add(a,b):

    return a+b
```

---

```python
operate(add,3,4)
```

---

# Fonksiyon Döndürme

```python
def outer():

    def inner():
        return "Hello"

    return inner
```

---

# Backend Açısından Önemi

Fonksiyon mantığını anlamadan şu konular zor anlaşılır:

- Decorators
- Django middleware
- Signals
- Dependency injection
- Clean architecture

Fonksiyon tasarımı backend geliştirmenin **temelidir**.

---

# 🎯 Mülakat Soruları

Sık sorulanlar:

- Mutable default parametre neden tehlikelidir?
- `return` ile `print` farkı nedir?
- Pure function nedir?
- Recursion riskleri nelerdir?
- `*args` ve `**kwargs` farkı nedir?
- First class function nedir?
- Side effect nedir?

---

# Tek Cümlelik Mülakat Tanımı

Fonksiyon, belirli bir işi yapan ve çağrıldığında çalışan tekrar kullanılabilir kod bloğudur.