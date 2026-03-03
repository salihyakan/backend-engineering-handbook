# BÖLÜM 1.3 — FUNCTIONS  
## MÜLAKAT SORULARI VE DETAYLI CEVAPLAR

Bu doküman yalnızca fonksiyon konusuna ait mülakat sorularını içerir.  
Temel → ileri seviye sıralı şekilde hazırlanmıştır.

---

# 1️⃣ Function Temelleri

### Soru 1: Fonksiyon nedir?

**Cevap:**

Belirli bir işi yapan, çağrıldığında çalışan kod bloğudur.

Amaçları:

- Kod tekrarını azaltmak (DRY)
- Mantığı izole etmek
- Test edilebilirliği artırmak
- Okunabilirliği yükseltmek

---

### Soru 2: return ile print arasındaki fark nedir?

**Cevap:**

| return | print |
|--------|--------|
| Değer döndürür | Ekrana yazdırır |
| Fonksiyonu sonlandırır | Fonksiyonu sonlandırmaz |
| Test edilebilir | Genelde side effect üretir |

Örnek:

```python
def add(a, b):
    return a + b
```

Bu fonksiyon sonucu döndürür.  
print kullansaydı sonucu başka yerde kullanamazdık.

---

### Soru 3: return yazılmazsa fonksiyon ne döner?

**Cevap:**

Varsayılan olarak `None` döner.

```python
def test():
    pass

print(test())  # None
```

---

### Soru 4: return neden fonksiyonu sonlandırır?

**Cevap:**

return çalıştığı anda fonksiyon execution durur ve kontrol çağıran yere döner.

```python
def example():
    return 5
    print("Bu çalışmaz")
```

---

# 2️⃣ Parametre Türleri

### Soru 5: Parameter ile argument farkı nedir?

**Cevap:**

- Parameter → Fonksiyon tanımındaki isim
- Argument → Fonksiyon çağrılırken verilen gerçek değer

```python
def greet(name):  # name = parameter
    print(name)

greet("Ali")  # "Ali" = argument
```

---

### Soru 6: Positional argument nedir?

**Cevap:**

Sıraya göre eşleşen argümanlardır.

```python
def divide(a, b):
    return a / b

divide(10, 2)
```

---

### Soru 7: Keyword argument nedir?

**Cevap:**

Parametre adı belirtilerek gönderilir.

```python
divide(a=10, b=2)
```

Avantajı:

- Okunabilirlik artar
- Sıra önemli olmaz

---

### Soru 8: Bu kullanım neden hatalıdır?

```python
divide(a=10, 2)
```

**Cevap:**

Keyword argument’tan sonra positional argument kullanılamaz.  
Bu SyntaxError üretir.

---

### Soru 9: Default parameter nedir?

**Cevap:**

Parametreye varsayılan değer verilmesidir.

```python
def greet(name="Misafir"):
    print(name)
```

Eğer argument verilmezse varsayılan değer kullanılır.

---

# 3️⃣ Mutable Default Parameter Problemi

### Soru 10: Mutable default parameter neden tehlikelidir?

**Cevap:**

Çünkü default parametreler fonksiyon tanımlanırken oluşturulur, her çağrıda değil.

```python
def add_item(item, my_list=[]):
    my_list.append(item)
    return my_list
```

Aynı liste tüm çağrılarda kullanılır.

---

### Soru 11: Bu kodun çıktısı nedir?

```python
print(add_item(1))
print(add_item(2))
```

**Cevap:**

```
[1]
[1, 2]
```

---

### Soru 12: Doğru çözüm nedir?

**Cevap:**

None kontrolü yapılmalıdır.

```python
def add_item(item, my_list=None):
    if my_list is None:
        my_list = []
    my_list.append(item)
    return my_list
```

---

# 4️⃣ *args ve **kwargs

### Soru 13: *args nedir?

**Cevap:**

Değişken sayıda positional argument alır.  
Tuple olarak gelir.

```python
def sum_all(*args):
    return sum(args)
```

---

### Soru 14: **kwargs nedir?

**Cevap:**

Değişken sayıda keyword argument alır.  
Dictionary olarak gelir.

```python
def print_info(**kwargs):
    print(kwargs)
```

---

### Soru 15: *args ile **kwargs farkı nedir?

**Cevap:**

| *args | **kwargs |
|-------|----------|
| Positional alır | Keyword alır |
| Tuple döner | Dict döner |

---

### Soru 16: Backend’te **kwargs nerede kullanılır?

**Cevap:**

Serializer ve ORM create/update işlemlerinde:

```python
User.objects.create(**validated_data)
```

---

# 5️⃣ Recursion

### Soru 17: Recursion nedir?

**Cevap:**

Fonksiyonun kendini çağırmasıdır.

---

### Soru 18: Recursion’da base case neden gereklidir?

**Cevap:**

Base case yoksa sonsuz recursion oluşur ve stack overflow hatası alınır.

---

### Soru 19: Python recursion limiti nedir?

**Cevap:**

Varsayılan olarak yaklaşık 1000 çağrıdır.  
Aşılırsa `RecursionError` oluşur.

---

### Soru 20: Recursion nerede kullanılır?

**Cevap:**

- Tree traversal
- Graph traversal
- Nested yapı çözme
- Dosya sistemi gezme

---

# 6️⃣ Pure Function & Side Effect

### Soru 21: Pure function nedir?

**Cevap:**

- Aynı input → aynı output
- Side effect yok

```python
def add(a, b):
    return a + b
```

---

### Soru 22: Side effect nedir?

**Cevap:**

Fonksiyonun dış dünyayı değiştirmesidir.

Örnek:

- Global değişken değiştirmek
- DB update
- Dosya yazmak
- Email göndermek
- Print etmek

---

### Soru 23: Bu fonksiyon pure mudur?

```python
x = 10
def add(a):
    return a + x
```

**Cevap:**

Hayır. Global değişken kullanıyor.

---

### Soru 24: Pure function neden önemlidir?

**Cevap:**

- Test edilebilirlik artar
- Predictable davranış sağlar
- Debug kolaylaşır
- Functional programming temelidir

---

# 7️⃣ First Class Functions

### Soru 25: First class function ne demektir?

**Cevap:**

Fonksiyonların bir nesne gibi davranabilmesidir.

- Değişkene atanabilir
- Parametre olarak gönderilebilir
- Fonksiyondan döndürülebilir

---

### Soru 26: Bu kod ne yapar?

```python
def greet():
    return "Merhaba"

hello = greet
print(hello())
```

**Cevap:**

Fonksiyon referansı değişkene atanır ve çağrılır.

---

### Soru 27: Fonksiyon parametre olarak nasıl gönderilir?

```python
def operate(func, a, b):
    return func(a, b)
```

**Cevap:**

Fonksiyon bir argument olarak geçilir ve çağrılır.

---

### Soru 28: Fonksiyon döndüren fonksiyon ne işe yarar?

**Cevap:**

Closure ve decorator yapılarının temelidir.

---

# 8️⃣ İleri Seviye Düşünme Soruları

### Soru 29: Fonksiyon tasarımında en önemli prensip nedir?

**Cevap:**

Single Responsibility Principle.  
Fonksiyon tek bir iş yapmalıdır.

---

### Soru 30: Neden küçük ve saf fonksiyonlar tercih edilir?

**Cevap:**

- Test yazmak kolaylaşır
- Hata izolasyonu kolaylaşır
- Refactor güvenlidir
- Clean Architecture desteklenir

---

# 🎯 Bu Dosyanın Amacı

Bu sorular:

- Fonksiyon mantığını gerçekten anlayıp anlamadığını ölçer
- Backend geliştirme altyapısını güçlendirir
- Decorator ve middleware gibi ileri konulara zemin hazırlar
- Teknik mülakatta seni bir üst seviyeye taşır