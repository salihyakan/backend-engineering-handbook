# 1.3 Fonksiyonlar (Functions)

Fonksiyonlar programlamanın temel yapı taşlarından biridir.  
İyi yazılmış fonksiyonlar:

- Tekrar eden kodu soyutlar  
- Mantığı izole eder  
- Kod tekrarını azaltır (DRY prensibi)  
- Test edilebilirliği artırır  
- Clean Architecture’ın temelini oluşturur  

Backend geliştirmede fonksiyon mantığını doğru kurmak, sürdürülebilir ve ölçeklenebilir kod yazmanın anahtarıdır.

---

## 1️⃣ Function Tanımı

Fonksiyon, belirli bir işi yapan ve çağrıldığında çalışan kod bloğudur.

### Temel Tanım

```python
def greet():
    print("Merhaba")
```

### Çağırma

```python
greet()
```

Çıktı:

```
Merhaba
```

---

## 2️⃣ return Nedir?

Fonksiyon bir değer döndürmek istiyorsa `return` kullanılır.

```python
def add(a, b):
    return a + b

result = add(3, 5)
print(result)
```

Çıktı:

```
8
```

### Önemli Noktalar

- `return` fonksiyonu **anında sonlandırır**.
- `return` yoksa fonksiyon varsayılan olarak `None` döner.

```python
def test():
    pass

print(test())  # None
```

---

## 3️⃣ Function Parameters

Fonksiyonun aldığı girdilere **parametre (parameter)** denir.

```python
def greet(name):
    print("Merhaba", name)
```

Burada `name` parametredir.

### Parametre Türleri

Python’da farklı parametre türleri vardır:

- Positional arguments
- Keyword arguments
- Default parameters
- Variable length arguments (`*args`, `**kwargs`)

---

## 4️⃣ Positional vs Keyword Arguments

### Positional Argument

Parametreler sıraya göre gönderilir.

```python
def divide(a, b):
    return a / b

divide(10, 2)
```

Burada:

- `a = 10`
- `b = 2`

---

### Keyword Argument

Parametre adı belirtilerek gönderilir.

```python
divide(a=10, b=2)
```

Avantajları:

- Okunabilirlik artar
- Parametre sırası önemli olmaz

---

### Karışık Kullanım

```python
divide(10, b=2)
```

Ancak şu kullanım hatalıdır:

```python
divide(a=10, 2)  # SyntaxError
```

Kural:  
**Keyword argument’tan sonra positional argument kullanılamaz.**

---

## 5️⃣ Default Parameters

Parametreye varsayılan değer verilebilir.

```python
def greet(name="Misafir"):
    print("Merhaba", name)

greet()
```

Çıktı:

```
Merhaba Misafir
```

---

## 6️⃣ Mutable Default Parameter Problemi (Mülakat Favorisi)

### Problemli Kod

```python
def add_item(item, my_list=[]):
    my_list.append(item)
    return my_list

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
[1, 2]
```

### Neden Böyle?

Python default parametreleri **fonksiyon tanımlanırken** oluşturur, her çağrıldığında değil.

Yani:

```python
my_list = []
```

sadece bir kez oluşturulur ve tüm çağrılar aynı listeyi kullanır.

---

### Doğru Kullanım

```python
def add_item(item, my_list=None):
    if my_list is None:
        my_list = []
    my_list.append(item)
    return my_list
```

Bu yöntem her çağrıda yeni liste oluşturur.

---

## 7️⃣ *args ve **kwargs

Fonksiyonun değişken sayıda parametre almasını sağlar.

---

### *args

Tuple olarak gelir.

```python
def sum_all(*args):
    return sum(args)

print(sum_all(1, 2, 3, 4))
```

`args` değeri:

```
(1, 2, 3, 4)
```

---

### **kwargs

Dictionary olarak gelir.

```python
def print_info(**kwargs):
    print(kwargs)

print_info(name="Ali", age=25)
```

`kwargs`:

```python
{"name": "Ali", "age": 25}
```

---

### Gerçek Backend Örneği

Django serializer kullanım mantığı:

```python
def create_user(**validated_data):
    return User.objects.create(**validated_data)
```

---

## 8️⃣ Recursion (Özyineleme)

Fonksiyonun kendini çağırmasıdır.

### Örnek: Faktöriyel

```python
def factorial(n):
    if n == 1:
        return 1
    return n * factorial(n - 1)
```

Çalışma akışı:

```
factorial(3)
→ 3 * factorial(2)
→ 3 * (2 * factorial(1))
→ 3 * (2 * 1)
→ 6
```

### Kritik Noktalar

- Mutlaka bir **base case** olmalıdır.
- Aksi halde sonsuz recursion oluşur.
- Python’da recursion derinlik limiti vardır (`RecursionError`).

### Backend Gerçek Kullanım

- Tree yapıları
- Nested yorum sistemi
- Dosya sistemi tarama
- Graph traversal

---

## 9️⃣ Pure Function

Pure function iki özelliğe sahiptir:

1. Aynı input → her zaman aynı output  
2. Dış dünyayı etkilemez (side effect yoktur)

### Pure Örnek

```python
def add(a, b):
    return a + b
```

---

### Pure Olmayan Örnek

```python
x = 10

def add(a):
    return a + x
```

Global değişken kullanıldığı için pure değildir.

---

## 🔟 Side Effects

Fonksiyonun dış dünyayı değiştirmesidir.

### Side Effect Örnekleri

- Global değişken değiştirmek
- Dosyaya yazmak
- Veritabanına kayıt atmak
- Email göndermek
- Cache yazmak
- Print etmek

```python
def add_item(lst):
    lst.append(5)
```

Bu pure değildir çünkü dış listeyi değiştirir.

Backend’te:

- DB update → side effect
- Email gönderme → side effect
- Log yazma → side effect

---

## 1️⃣1️⃣ First Class Functions

Python’da fonksiyonlar bir nesnedir.

Yani:

- Değişkene atanabilir
- Parametre olarak gönderilebilir
- Başka fonksiyondan döndürülebilir

---

### Fonksiyonu Değişkene Atama

```python
def greet():
    return "Merhaba"

hello = greet
print(hello())
```

---

### Fonksiyon Parametre Olarak

```python
def operate(func, a, b):
    return func(a, b)

def add(a, b):
    return a + b

print(operate(add, 3, 4))
```

---

### Fonksiyon Döndürme

```python
def outer():
    def inner():
        return "Hello"
    return inner
```

---

## Backend Açısından Önemi

Bu konuları bilmeden:

- Decorator anlayamazsın
- Django signal mantığını kavrayamazsın
- Middleware yazamazsın
- Clean architecture kuramazsın
- Test edilebilir kod yazamazsın

Fonksiyon tasarımı, backend geliştirmenin kalbidir.

---

## 🎯 Mülakat Seviyesi Sorular

- Mutable default parametre neden tehlikelidir?
- Pure function nedir?
- Recursion riskleri nelerdir?
- `*args` ve `**kwargs` farkı nedir?
- Function first class ne demektir?
- `return` ile `print` arasındaki fark nedir?
- Side effect nedir ve neden test edilebilirliği zorlaştırır?

---