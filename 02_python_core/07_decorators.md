# 2.7 Decorators

Decorator, Python’un en güçlü ve en önemli özelliklerinden biridir.  
Django, Flask, FastAPI gibi framework’lerin temelinde decorator mantığı vardır.

Örnek:

```python
@login_required
def profile():
    ...
```

Bu bir decorator’dır.

---

## Bu Bölümde Öğreneceklerin

- Decorator nedir  
- Function decorator  
- Parametre alan decorator  
- Class decorator  
- Decorator chaining  
- functools.wraps  

---

# 1️⃣ Decorator Nedir?

Decorator:

> Bir fonksiyonu değiştirmeden, davranışını genişleten yapıdır.

Başka bir deyişle:

> Bir fonksiyonu saran (wrap eden) fonksiyondur.

---

## Temel Mantık

Decorator:

- Bir fonksiyon alır  
- Yeni bir fonksiyon döndürür  

---

## Basit Örnek

```python
def decorator(func):
    def wrapper():
        print("Önce çalışır")
        func()
        print("Sonra çalışır")
    return wrapper
```

Kullanım:

```python
def selam():
    print("Merhaba")

selam = decorator(selam)
selam()
```

Output:

```
Önce çalışır
Merhaba
Sonra çalışır
```

---

# 2️⃣ Decorator Syntax ( @ )

Python, decorator kullanımını kolaylaştırmak için `@` syntax’ını sunar.

```python
def decorator(func):
    def wrapper():
        print("Önce")
        func()
        print("Sonra")
    return wrapper

@decorator
def selam():
    print("Merhaba")

selam()
```

Bu aslında şuna eşittir:

```python
selam = decorator(selam)
```

---

# 3️⃣ Function Decorator

En yaygın decorator türüdür.  
Fonksiyonları sarar.

## Örnek: Log Decorator

```python
def log_decorator(func):
    def wrapper():
        print("Fonksiyon çalışıyor:", func.__name__)
        func()
    return wrapper

@log_decorator
def test():
    print("Test çalıştı")

test()
```

Output:

```
Fonksiyon çalışıyor: test
Test çalıştı
```

---

# 4️⃣ Parametre Alan Decorator

Fonksiyon parametre alıyorsa `wrapper` da almalıdır.

```python
def log(func):
    def wrapper(*args, **kwargs):
        print("Çalışıyor")
        return func(*args, **kwargs)
    return wrapper

@log
def topla(a, b):
    return a + b

print(topla(2, 3))
```

Burada:

- `*args` → positional parametreleri  
- `**kwargs` → keyword parametreleri  

aktarır.

---

# 5️⃣ Gerçek Hayat Örneği (Authentication)

```python
def login_required(func):
    def wrapper(user):
        if not user["logged"]:
            print("Giriş gerekli")
            return
        return func(user)
    return wrapper

@login_required
def profile(user):
    print("Profile erişildi")

user = {"logged": True}
profile(user)
```

---

# 6️⃣ Decorator Nasıl Çalışır? (Step by Step)

```python
@decorator
def test():
    pass
```

Python bunu şöyle çalıştırır:

```python
test = decorator(test)
```

---

# 7️⃣ Class Decorator

Decorator class ile de yazılabilir.

```python
class MyDecorator:

    def __init__(self, func):
        self.func = func

    def __call__(self):
        print("Önce")
        self.func()
        print("Sonra")

@MyDecorator
def test():
    print("Test")

test()
```

`__call__` metodu sayesinde object fonksiyon gibi çağrılabilir.

---

# 8️⃣ Decorator Chaining

Birden fazla decorator kullanılabilir.

```python
def deco1(func):
    def wrapper():
        print("Deco1 önce")
        func()
        print("Deco1 sonra")
    return wrapper

def deco2(func):
    def wrapper():
        print("Deco2 önce")
        func()
        print("Deco2 sonra")
    return wrapper

@deco1
@deco2
def test():
    print("Test")

test()
```

Output:

```
Deco1 önce
Deco2 önce
Test
Deco2 sonra
Deco1 sonra
```

Execution sırası:

```python
test = deco1(deco2(test))
```

Decorator’lar **içten dışa sarılır**.

---

# 9️⃣ functools.wraps (ÇOK ÖNEMLİ)

Decorator kullanıldığında orijinal fonksiyonun bilgileri kaybolur.

## Problem

```python
def deco(func):
    def wrapper():
        return func()
    return wrapper

@deco
def test():
    pass

print(test.__name__)
```

Output:

```
wrapper
```

Ama biz:

```
test
```

bekleriz.

---

# 🔟 Çözüm: functools.wraps

```python
from functools import wraps

def deco(func):

    @wraps(func)
    def wrapper():
        return func()

    return wrapper
```

Artık:

```python
print(test.__name__)
```

Output:

```
test
```

---

## wraps Ne Yapar?

Şunları korur:

- Function name  
- Docstring  
- Annotations  
- Metadata  

---

# 1️⃣1️⃣ Timing Decorator Örneği

```python
import time
from functools import wraps

def timer(func):

    @wraps(func)
    def wrapper(*args, **kwargs):

        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()

        print("Süre:", end - start)

        return result

    return wrapper
```

---

# 1️⃣2️⃣ Decorator Neden Kullanılır?

Şunlar için kullanılır:

- Logging  
- Authentication  
- Authorization  
- Caching  
- Validation  
- Performance measurement  

---

# 1️⃣3️⃣ Decorator Nasıl Çalışır? (Memory Mantığı)

```python
@decorator
def test():
```

Memory akışı:

```
test → decorator → wrapper → original function
```

---

# 1️⃣4️⃣ Decorator ve Closure İlişkisi

Decorator aslında bir **closure**’dır.

Wrapper function:

- Outer function değişkenlerini hatırlar  
- State’i saklar  

---

# 1️⃣5️⃣ Senior Level Bilgi

Decorator:

- Higher-order function’dır  
- Function alır, function döndürür  
- Closure kullanır  

---

# 1️⃣6️⃣ Django’da Decorator Kullanımı

Örnekler:

- `login_required`  
- `permission_required`  
- `csrf_exempt`  
- `api_view`  

Bu decorator’lar:

- Request kontrolü yapar  
- Yetki kontrol eder  
- Güvenlik sağlar  
- API davranışını değiştirir  

---

# 1️⃣7️⃣ Mülakat Soruları

**Soru:** Decorator nedir?  
**Cevap:** Fonksiyonun davranışını değiştiren wrapper function’dır.

**Soru:** @ syntax ne yapar?  
**Cevap:** `function = decorator(function)`

**Soru:** wraps neden gerekir?  
**Cevap:** Orijinal function metadata’sını korur.

**Soru:** Decorator chaining nasıl çalışır?  
**Cevap:** İçten dışa sarılır.

---

# 🔥 En Kritik Özet

```
Decorator → function saran function’dır
Behavior  → genişletir
Closure   → kullanır
wraps     → metadata korur
Chaining  → decorator(decorator(function))
```