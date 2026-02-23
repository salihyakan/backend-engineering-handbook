# 2.6 Generator & Iterator

Bu konu Python’un **execution model’i** ve **memory verimliliği** açısından en kritik konularından biridir.

Özellikle:

- Django ORM’nin lazy çalışması  
- Büyük dosya işleme  
- Streaming sistemleri  
- Async yapıların mantığı  

Bu temele dayanır.

---

## Bu Bölümde Öğreneceklerin

- Iterator protocol  
- iter()  
- next()  
- Generator nedir  
- yield keyword  
- Lazy evaluation  
- Generator vs list memory farkı  

---

# 1️⃣ Iterator Nedir?

Iterator:

> Elemanları teker teker üretebilen ve sırayla ilerleyen objedir.

Amaç:

- Büyük veriyi tek seferde memory’ye almamak  
- Adım adım üretmek  

Örnek:

```python
lst = [1, 2, 3]
```

Liste **iterable’dır**, fakat **iterator değildir**.

Iterator almak için:

```python
it = iter(lst)
```

---

# 2️⃣ Iterator Protocol

Bir object’in iterator olması için iki method gerekir:

- `__iter__()`  
- `__next__()`  

Bu kurala:

> **Iterator Protocol** denir.

---

## Basit Custom Iterator Örneği

```python
class Counter:
    def __init__(self, max):
        self.max = max
        self.current = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.current < self.max:
            self.current += 1
            return self.current
        else:
            raise StopIteration
```

Kullanım:

```python
c = Counter(3)

for i in c:
    print(i)
```

Output:

```
1
2
3
```

---

## StopIteration Nedir?

Iterator bittiğinde:

```python
raise StopIteration
```

exception fırlatılır.

`for` loop bunu otomatik yakalar.

---

# 3️⃣ iter()

`iter()`:

> Bir iterable’dan iterator üretir.

Örnek:

```python
lst = [10, 20, 30]

it = iter(lst)

print(next(it))
print(next(it))
print(next(it))
```

Output:

```
10
20
30
```

---

# 4️⃣ next()

`next()`:

> Iterator’dan bir sonraki değeri alır.

Iterator biterse:

```
StopIteration
```

fırlatır.

Örnek:

```python
it = iter([1, 2])

print(next(it))  # 1
print(next(it))  # 2
print(next(it))  # StopIteration
```

---

# 5️⃣ Iterable vs Iterator (Mülakat Sorusu)

| Özellik | Iterable | Iterator |
|----------|----------|----------|
| for ile çalışır | ✅ | ✅ |
| iter() çağrılabilir | ✅ | ❌ |
| next() var mı | ❌ | ✅ |

Örnek:

```python
lst = [1, 2, 3]

iter(lst)  # çalışır
next(lst)  # hata
```

Ama:

```python
it = iter(lst)
next(it)  # çalışır
```

---

# 6️⃣ Generator Nedir?

Generator:

> Iterator üreten özel bir fonksiyondur.

Normal function:

```python
def say():
    return [1, 2, 3]
```

Generator:

```python
def say():
    yield 1
    yield 2
    yield 3
```

Kullanım:

```python
g = say()

print(next(g))
print(next(g))
```

---

# 7️⃣ yield Keyword

`yield`:

> Fonksiyonu durdurur ama state’i saklar.

Normal function:

```
return → fonksiyon biter
```

Generator:

```
yield → pause eder, devam edilebilir
```

Örnek:

```python
def say():
    print("başladı")
    yield 1
    print("devam")
    yield 2
```

Kullanım:

```python
g = say()

print(next(g))
print(next(g))
```

Output:

```
başladı
1
devam
2
```

Fonksiyon her seferinde kaldığı yerden devam eder.

---

# 8️⃣ Lazy Evaluation

Lazy evaluation:

> Değer ihtiyaç olduğunda üretilir.

Generator:

- Tüm veriyi baştan üretmez  
- İstenince üretir  

Örnek:

```python
def say():
    for i in range(1000000000):
        yield i
```

Bu memory doldurmaz.

Çünkü:

Her seferinde tek değer üretir.

---

# 9️⃣ Generator Expression

List comprehension:

```python
lst = [x * 2 for x in range(1000000)]
```

Memory doldurur.

Generator expression:

```python
gen = (x * 2 for x in range(1000000))
```

Memory doldurmaz.

---

# 🔟 Generator vs List Memory Farkı

## List

```python
lst = [x for x in range(1000000)]
```

- 1 milyon eleman memory’de tutulur  
- RAM kullanımı yüksektir  

## Generator

```python
gen = (x for x in range(1000000))
```

- Sadece 1 eleman memory’dedir  
- RAM kullanımı düşüktür  

---

## sys.getsizeof Karşılaştırma

```python
import sys

lst = [x for x in range(1000000)]
gen = (x for x in range(1000000))

print(sys.getsizeof(lst))
print(sys.getsizeof(gen))
```

Generator çok daha küçük çıkar.

---

# 1️⃣1️⃣ Django ORM Neden Lazy?

```python
users = User.objects.all()
```

Bu satırda query çalışmaz.

Ne zaman çalışır?

```python
for user in users:
    print(user)
```

Çünkü:

- QuerySet iterator gibi davranır  
- Lazy evaluation kullanır  

---

# 1️⃣2️⃣ for Loop Nasıl Çalışır?

Bu çok kritik bir bilgidir.

```python
for x in iterable:
    print(x)
```

Aslında arka planda:

```python
it = iter(iterable)

while True:
    try:
        x = next(it)
        print(x)
    except StopIteration:
        break
```

`for` loop → iterator protocol kullanır.

---

# 1️⃣3️⃣ Gerçek Hayat Analojisi

List:

> Tüm kitapları bir anda masaya koymak

Generator:

> Kitapları raftan tek tek almak

---

# 1️⃣4️⃣ Ne Zaman Generator Kullanmalı?

- Büyük dosya okuma  
- API stream  
- Pagination  
- Sonsuz veri üretme  
- Memory optimizasyonu  

---

# 1️⃣5️⃣ Senior Seviye Kritik Bilgi

Generator:

- Iterator’dır  
- `__iter__` ve `__next__` içerir  
- Lazy çalışır  
- State saklar  

---

# 1️⃣6️⃣ Mülakat Soruları

**Soru:** Iterator protocol nedir?  
**Cevap:** `__iter__` ve `__next__` metodlarını içeren yapıdır.

**Soru:** yield ne yapar?  
**Cevap:** Fonksiyonu durdurur, state’i saklar ve iterator üretir.

**Soru:** Generator neden memory efficient?  
**Cevap:** Lazy evaluation kullanır, tüm veriyi memory’de tutmaz.

**Soru:** Iterable ve Iterator farkı?  
**Cevap:**  
Iterable → `iter()` ile iterator üretir.  
Iterator → `next()` ile ilerler.

---

# 🔥 En Kritik Özet

```
Iterator  → next() ile ilerler
Iterable  → iter() ile iterator üretir
Generator → yield kullanan özel iterator
yield     → pause + state save
Lazy eval → ihtiyaç anında üretim
Generator → list’ten çok daha az memory kullanır
```