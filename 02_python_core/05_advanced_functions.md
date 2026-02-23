# 2.5 Python Advanced Functions

Bu bölüm Python’un **functional programming (fonksiyonel programlama)** özelliklerini kapsar.

Bu kavramlar:

- Daha temiz ve okunabilir kod yazmanı sağlar  
- Daha performanslı çözümler üretmeni sağlar  
- Teknik mülakatlarda sık sorulur  
- Django, FastAPI, veri işleme (data processing) gibi alanlarda yoğun kullanılır  

---

## Bu Bölümde Öğreneceklerin

- lambda functions  
- map  
- filter  
- reduce  
- list comprehension  
- dict comprehension  
- set comprehension  
- generator expression  

---

# 1️⃣ Lambda Functions

## Tanım

Lambda function:

> İsimsiz (anonymous) ve kısa fonksiyondur.

Normal fonksiyon:

```python
def topla(a, b):
    return a + b
```

Lambda karşılığı:

```python
topla = lambda a, b: a + b
```

---

## Syntax

```python
lambda parametreler: expression
```

Örnek:

```python
kare = lambda x: x * x
print(kare(5))
```

Output:

```
25
```

---

## Lambda Neden Var?

- Kısa süreli
- Tek kullanımlık
- Basit işlemler için

Örnek:

```python
sayilar = [1, 2, 3, 4]
sonuc = list(map(lambda x: x * 2, sayilar))
```

---

## Ne Zaman Kullanılır?

- map  
- filter  
- sorted key  
- Kısa işlemler  

Örnek (`sorted` ile):

```python
kisiler = [
    ("Salih", 25),
    ("Ali", 20),
    ("Ayşe", 30)
]

kisiler.sort(key=lambda x: x[1])
```

Yaşa göre sıralar.

---

# 2️⃣ map()

## Tanım

`map`:

> Bir fonksiyonu iterable içindeki tüm elemanlara uygular.

## Syntax

```python
map(function, iterable)
```

## Örnek

```python
sayilar = [1, 2, 3, 4]

sonuc = map(lambda x: x * 2, sayilar)
print(list(sonuc))
```

Output:

```
[2, 4, 6, 8]
```

## Normal Yöntem

```python
sonuc = []

for x in sayilar:
    sonuc.append(x * 2)
```

`map` daha kısa ve fonksiyoneldir.

---

# 3️⃣ filter()

## Tanım

`filter`:

> Şarta uyan elemanları seçer.

## Syntax

```python
filter(function, iterable)
```

## Örnek

```python
sayilar = [1, 2, 3, 4, 5, 6]

cift = list(filter(lambda x: x % 2 == 0, sayilar))
print(cift)
```

Output:

```
[2, 4, 6]
```

Mantık:

- x = 1 → False  
- x = 2 → True  
- x = 3 → False  
- x = 4 → True  

---

# 4️⃣ reduce()

## Tanım

`reduce`:

> Iterable’ı tek bir değere indirger (aggregate eder).

## Import Gerekir

```python
from functools import reduce
```

## Örnek

```python
from functools import reduce

sayilar = [1, 2, 3, 4]

sonuc = reduce(lambda a, b: a + b, sayilar)
print(sonuc)
```

Output:

```
10
```

## Nasıl Çalışır?

```
1 + 2 = 3
3 + 3 = 6
6 + 4 = 10
```

## Gerçek Kullanım

- Toplam alma  
- Çarpım  
- Aggregate işlemler  

---

# 5️⃣ List Comprehension

En önemli konulardan biridir.  
Pythonic kodun temelidir.

## Normal Yöntem

```python
sonuc = []

for x in range(5):
    sonuc.append(x * 2)
```

## Comprehension

```python
sonuc = [x * 2 for x in range(5)]
```

Output:

```
[0, 2, 4, 6, 8]
```

## Şartlı Kullanım

```python
[x for x in range(10) if x % 2 == 0]
```

Output:

```
[0, 2, 4, 6, 8]
```

## Gerçek Kullanım

```python
users = ["salih", "ali"]
upper = [u.upper() for u in users]
```

---

# 6️⃣ Dict Comprehension

Dictionary üretir.

```python
d = {x: x * x for x in range(5)}
```

Output:

```
{
0:0,
1:1,
2:4,
3:9,
4:16
}
```

---

# 7️⃣ Set Comprehension

Set üretir.

```python
s = {x * 2 for x in range(5)}
```

Output:

```
{0, 2, 4, 6, 8}
```

---

# 8️⃣ Generator Expression

List comprehension gibidir ama liste oluşturmaz.

> Lazy evaluation (ihtiyaç olunca üretim) kullanır.

## List Comprehension

```python
lst = [x * 2 for x in range(1000000)]
```

Memory tüketimi yüksektir.

## Generator Expression

```python
gen = (x * 2 for x in range(1000000))
```

Memory tüketimi düşüktür.

## Kullanım

```python
for x in gen:
    print(x)
```

---

# 9️⃣ Memory Karşılaştırma

| Yapı | Memory Kullanımı |
|------|------------------|
| List Comprehension | Yüksek |
| Generator Expression | Düşük |

---

# 🔟 Gerçek Hayat Kullanımı

Büyük dosya okuma:

```python
lines = (line for line in file)
```

Tüm dosyayı memory’ye yüklemez.

---

# 1️⃣1️⃣ map vs Comprehension

map:

```python
map(lambda x: x * 2, lst)
```

Comprehension:

```python
[x * 2 for x in lst]
```

> Comprehension daha pythonic kabul edilir.

---

# 1️⃣2️⃣ filter vs Comprehension

filter:

```python
filter(lambda x: x > 10, lst)
```

Comprehension:

```python
[x for x in lst if x > 10]
```

---

# 1️⃣3️⃣ reduce vs sum

reduce:

```python
reduce(lambda a, b: a + b, lst)
```

Pythonic:

```python
sum(lst)
```

---

# 1️⃣4️⃣ Senior Level Kritik Bilgiler

- Comprehension → Loop’tan daha hızlıdır  
- Generator → Memory efficient’tir  
- Lambda → Anonymous function’tır  
- map / filter → Functional araçlardır  
- reduce → Aggregation için kullanılır  

---

# 1️⃣5️⃣ Performans Karşılaştırma (Genel)

Genellikle:

```
En hızlı → Comprehension
Sonra → map
Daha yavaş → klasik loop
```

(Not: Senaryoya göre değişebilir.)

---

# 1️⃣6️⃣ Mülakat Soruları

**Soru:** Lambda nedir?  
**Cevap:** Anonymous (isimsiz) kısa fonksiyondur.

**Soru:** Generator neden memory efficient’tir?  
**Cevap:** Lazy evaluation kullanır, tüm veriyi aynı anda oluşturmaz.

**Soru:** map ne yapar?  
**Cevap:** Bir fonksiyonu iterable içindeki tüm elemanlara uygular.

**Soru:** filter ne yapar?  
**Cevap:** Şarta uyan elemanları seçer.

**Soru:** reduce ne yapar?  
**Cevap:** Iterable’ı tek bir değere indirger (aggregate eder).

---

# 🔥 En Kritik Özet

```
lambda        → kısa, anonymous function
map           → function uygular
filter        → filtreler
reduce        → aggregate eder
comprehension → pythonic loop
generator     → memory efficient yapı
```