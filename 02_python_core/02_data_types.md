# 2.2 Python Data Types (Veri Tipleri)

Python’da **her şey bir object (nesne)**’dir.

Bir nesnenin:

- Bellekte nasıl saklanacağını  
- Hangi işlemleri desteklediğini  
- Mutable mı immutable mı olduğunu  
- Performans özelliklerini  

belirleyen şeye **data type (veri tipi)** denir.

---

## Python’daki Temel Built-in Veri Tipleri

| Tip       | Açıklama              | Mutable |
|------------|----------------------|----------|
| int        | Tam sayı              | ❌ Immutable |
| float      | Ondalıklı sayı        | ❌ Immutable |
| bool       | True / False          | ❌ Immutable |
| str        | Metin                 | ❌ Immutable |
| list       | Liste                 | ✅ Mutable |
| tuple      | Sabit liste           | ❌ Immutable |
| set        | Benzersiz elemanlar   | ✅ Mutable |
| dict       | Key-value veri        | ✅ Mutable |
| bytes      | Binary veri           | ❌ Immutable |
| NoneType   | Boş değer             | ❌ Immutable |

---

# 1️⃣ int (Integer)

Tam sayıları temsil eder.

```python
x = 10
y = -5
z = 0
```

### Özellikleri

- Immutable’dır  
- Keyfi büyüklükte olabilir (arbitrary precision)  
- Overflow problemi yoktur  

```python
x = 10
print(type(x))
```

Çıktı:

```
<class 'int'>
```

---

### Small Integer Caching

```python
a = 10
b = 10

print(a is b)
```

Çıktı:

```
True
```

CPython küçük sayıları cache’ler (genellikle -5 ile 256 arası).  
Bu bir optimizasyondur.

---

# 2️⃣ float

Ondalıklı sayıları temsil eder.

```python
x = 3.14
y = -0.5
```

### Özellikleri

- Immutable’dır  
- IEEE 754 standardını kullanır  
- Precision hataları olabilir  

```python
print(0.1 + 0.2)
```

Çıktı:

```
0.30000000000000004
```

Sebep: Binary floating point representation.

---

# 3️⃣ bool

Mantıksal veri tipidir.

Sadece iki değeri vardır:

```python
True
False
```

```python
is_active = True
```

### Önemli Bilgi

`bool`, `int`’in subclass’ıdır.

```python
print(True == 1)
```

Çıktı:

```
True
```

---

# 4️⃣ str (String)

Metin veri tipidir.

```python
name = "Ali"
```

### Özellikleri

- Immutable’dır  
- Unicode destekler  

```python
text = "Hello"
```

---

### Immutable Davranış

```python
text = "Hello"
text[0] = "h"
```

Hata:

```
TypeError
```

Yeni string oluşturulur:

```python
text = text.lower()
```

---

# 5️⃣ list

Birden fazla veri tutar.

```python
numbers = [1, 2, 3]
```

### Özellikleri

- Mutable’dır  
- Ordered’dır  
- Duplicate kabul eder  

```python
numbers.append(4)
```

---

### Referans Davranışı

```python
a = [1, 2]
b = a

b.append(3)

print(a)
```

Çıktı:

```
[1, 2, 3]
```

Çünkü aynı nesne referans edilir.

---

# 6️⃣ tuple

Liste gibidir ama immutable’dır.

```python
coordinates = (10, 20)
```

### Özellikleri

- Immutable  
- Ordered  
- List’ten genelde daha hızlı  

```python
point = (1, 2)
```

Değiştirilemez:

```python
point[0] = 5
```

Hata verir.

---

# 7️⃣ set

Benzersiz elemanlar tutar.

```python
numbers = {1, 2, 3}
```

### Özellikleri

- Mutable  
- Unordered  
- Duplicate kabul etmez  

```python
numbers.add(4)
numbers.add(4)
```

Set aynı kalır.

---

# 8️⃣ dict (Dictionary)

Key-value veri yapısıdır.

```python
user = {
    "name": "Ali",
    "age": 25
}
```

### Özellikleri

- Mutable  
- Ortalama O(1) lookup  
- Hash table kullanır  

```python
print(user["name"])
```

---

# 9️⃣ bytes

Binary veri tutar.

```python
data = b"hello"
```

### Özellikleri

- Immutable  
- Binary işlemler için kullanılır  

Kullanım alanları:

- Dosya okuma  
- Network işlemleri  
- Image / binary data  

---

# 🔟 NoneType

Hiçbir değer olmadığını temsil eder.

```python
x = None
```

```python
print(type(None))
```

Çıktı:

```
<class 'NoneType'>
```

---

### Neden Kullanılır?

Default value olarak:

```python
def foo(x=None):
    if x is None:
        x = []
```

Mutable default parametre probleminden kaçınmak için kullanılır.

---

# Mutable vs Immutable Özet

## Immutable

- int  
- float  
- bool  
- str  
- tuple  
- bytes  
- None  

## Mutable

- list  
- dict  
- set  

---

# Bellek Davranışı

## Immutable Örneği

```python
x = 10
y = x

y = 20
```

`x` değişmez. Yeni nesne oluşturulur.

---

## Mutable Örneği

```python
a = [1, 2]
b = a

b.append(3)
```

`a` değişir çünkü aynı nesne referans edilir.

---

# type() Fonksiyonu

```python
x = 10
print(type(x))
```

---

# isinstance() (Daha Doğru Yöntem)

```python
isinstance(x, int)
```

`isinstance`, inheritance durumlarında daha güvenlidir.

---

# Backend ve Django Açısından Önemi

Bu veri tipleri şunların temelidir:

- Django Models  
- QuerySet sonuçları  
- JSON serialization  
- Cache mekanizmaları  
- API response yapıları  
- Form validation  

Yanlış mutable kullanımı production’da ciddi bug’lara yol açabilir.

---

# Mülakat Soruları

- list vs tuple farkı nedir?  
- mutable vs immutable nedir?  
- dict neden hızlıdır?  
- set neden duplicate kabul etmez?  
- None nedir ve neden kullanılır?  
- bool neden int’in subclass’ıdır?  

---

# 🎯 Özet

Python’da her şey object’tir.

Veri tipleri iki ana kategoriye ayrılır:

**Immutable → değiştirilemez**  
**Mutable → değiştirilebilir**

Bu fark:

- Memory davranışını  
- Performansı  
- Referans mantığını  
- Bug oluşma ihtimalini  

doğrudan etkiler.

Python’u derinlemesine anlamak için veri tiplerini yüzeysel değil, bellek seviyesinde kavramak gerekir.