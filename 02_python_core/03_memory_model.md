# 2.3 Python Memory Model

Python’da **memory model**, değişkenlerin nasıl saklandığını, referansların nasıl çalıştığını ve belleğin nasıl yönetildiğini açıklar.

Bu konu mülakatlarda çok sık sorulur çünkü:

- Python’un referans mantığını anlayıp anlamadığını gösterir  
- Mutable / immutable davranışını açıklayabilmeni sağlar  
- Memory leak ve performans konularına hakimiyetini gösterir  

---

# Bu Bölümde Öğreneceklerin

- Object identity  
- `is` vs `==` farkı  
- `id()` fonksiyonu  
- Reference counting  
- Garbage collector  
- Small integer caching  
- String interning  

---

# 1️⃣ Python’da Her Şey Object’tir

Python’da teknik olarak primitive / non-primitive ayrımı yoktur.

Her şey object’tir:

```python
x = 10
y = "hello"
z = [1, 2, 3]
```

Her object’in 3 temel özelliği vardır:

- **identity** → benzersiz kimlik  
- **type** → veri tipi  
- **value** → değer  

---

# 2️⃣ Object Identity

Identity = bir object’in benzersiz kimliğidir.

CPython’da genellikle memory adresidir.

```python
x = 10
y = 10

print(id(x))
print(id(y))
```

Çoğu durumda aynı çıkar (small integer caching nedeniyle).

---

# 3️⃣ `id()` Fonksiyonu

Bir object’in identity değerini döndürür.

```python
x = "hello"
print(id(x))
```

CPython’da bu değer genellikle memory adresidir.

---

# 4️⃣ `is` vs `==` (ÇOK ÖNEMLİ)

Bu klasik mülakat sorusudur.

| Operatör | Karşılaştırma Türü |
|-----------|--------------------|
| `==`      | Value equality     |
| `is`      | Object identity    |

---

## Örnek 1

```python
a = [1, 2]
b = [1, 2]

print(a == b)  # True
print(a is b)  # False
```

- Değerleri aynı  
- Ama memory adresleri farklı  

---

## Örnek 2

```python
a = 10
b = 10

print(a == b)  # True
print(a is b)  # True (small integer cache nedeniyle)
```

---

## Örnek 3

```python
a = None
b = None

print(a is b)  # True
```

`None` bir **singleton**’dır.

---

### Mülakat Cevabı

- `==` → value equality kontrol eder  
- `is` → object identity kontrol eder  

---

# 5️⃣ Reference (Referans) Mantığı

Python’da değişkenler value tutmaz.

Object’e referans tutar.

```python
x = [1, 2, 3]
y = x
```

Memory mantığı:

```
x ─┐
   ├──> [1,2,3]
y ─┘
```

```python
y.append(4)
print(x)
```

Çıktı:

```
[1, 2, 3, 4]
```

Çünkü aynı object’i gösteriyorlar.

---

# 6️⃣ Reference Counting

Python bellek yönetimini **reference counting** ile yapar.

Her object kaç referans tarafından kullanılıyor sayılır.

```python
import sys

x = []
print(sys.getrefcount(x))
```

Reference count 0 olunca:

```
object → refcount = 0 → memory free
```

---

## Örnek

```python
x = [1, 2, 3]

y = x
z = x

del y
del z
del x
```

Artık referans kalmaz → object silinir.

---

# 7️⃣ Garbage Collector (GC)

Reference counting tek başına yeterli değildir.

Çünkü circular reference olabilir.

---

## Circular Reference Örneği

```python
a = []
b = []

a.append(b)
b.append(a)
```

Memory:

```
a → b
b → a
```

Refcount hiçbir zaman 0 olmaz.

Bu durumda memory leak oluşur.

---

## Python GC Ne Yapar?

- Circular reference’ları tespit eder  
- Temizler  

Manuel tetikleme:

```python
import gc
gc.collect()
```

---

# 8️⃣ Small Integer Caching

Python performans için küçük integer’ları cache’ler.

Genellikle aralık:

```
-5 ile 256
```

```python
a = 100
b = 100

print(a is b)  # True
```

Ama:

```python
a = 1000
b = 1000

print(a is b)  # False olabilir
```

Amaç: Performance optimization.

---

# 9️⃣ String Interning

Python bazı string’leri cache’ler.

```python
a = "hello"
b = "hello"

print(a is b)  # True olabilir
```

Ama:

```python
a = "hello world"
b = "hello world"

print(a is b)  # False olabilir
```

---

## Manuel Interning

```python
import sys

a = sys.intern("hello world")
b = sys.intern("hello world")

print(a is b)  # True
```

---

# 🔟 Mutable vs Immutable Memory Davranışı

## Immutable Örnek

```python
x = 10
y = x

x = 20
```

Yeni object oluşturulur.

```
y → 10
x → 20
```

---

## Mutable Örnek

```python
x = [1, 2]
y = x

x.append(3)
```

```
x,y → [1,2,3]
```

Aynı object değişir.

---

# 1️⃣1️⃣ Python’da Variable Gerçekten Nedir?

Python’da variable:

- value değil  
- object reference tutar  

```python
x = 10
```

Bu şu anlama gelir:

- 10 object oluştur  
- x → o object’e referans ver  

---

# 1️⃣2️⃣ Memory Model Diyagramı

```
Variable → Reference → Object → Value
```

Örnek:

```
x → memory address → int object → value=10
```

---

# 1️⃣3️⃣ Senior Seviye Kritik Soru

```python
a = [1, 2, 3]
b = a
b = [4, 5, 6]

print(a)
```

Çıktı:

```
[1, 2, 3]
```

Çünkü `b` yeni object’e bağlandı.

Ama:

```python
b = a
b.append(4)
```

Çıktı:

```
[1, 2, 3, 4]
```

---

# 1️⃣4️⃣ Mülakat Soruları

**Soru:** `is` ve `==` farkı nedir?  
**Cevap:** `==` value equality, `is` object identity kontrol eder.

**Soru:** Python memory management nasıl çalışır?  
**Cevap:** Reference counting + garbage collector kullanır.

**Soru:** Garbage collector neden gerekir?  
**Cevap:** Circular reference’ları temizlemek için.

**Soru:** Python’da variable ne tutar?  
**Cevap:** Object reference tutar.

**Soru:** Small integer caching nedir?  
**Cevap:** -5 ile 256 arası integer’ların cache edilmesidir.

---

# 🎯 En Kritik Özet

Python’da:

- Variables value tutmaz  
- Object reference tutar  
- Reference counting memory yönetir  
- Garbage collector circular reference temizler  
- `is` identity karşılaştırır  
- `==` value karşılaştırır  
- Küçük integer’lar ve bazı string’ler cache edilir  

Python’un memory model’ini anlamak, senior backend seviyesine geçişte kritik eşiktir.