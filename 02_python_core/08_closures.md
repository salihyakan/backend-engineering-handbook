# 2.8 Closures

Closure, Python’un en önemli ama genelde yanlış anlaşılan konularından biridir. Decorator, callback, functional programming ve birçok framework mekanizması closure üzerine kuruludur.

---

## Bu Bölümde Öğreneceklerin

- Closure nedir
- Lexical scoping nedir
- LEGB kuralı
- nonlocal keyword nedir
- Decorator ile ilişkisi
- Memory ve execution mantığı
- Closure vs Class farkı

---

# 1. Closure Nedir?

Closure:

Bir fonksiyonun, tanımlandığı scope’taki değişkenleri hatırlaması ve kullanmaya devam etmesidir.

Yani:

Fonksiyon çalışsa bile, dış scope’taki değişkenleri kaybetmez.

---

## Basit Örnek

```python
def dis():
    mesaj = "Merhaba"

    def ic():
        print(mesaj)

    return ic

f = dis()
f()
```

Output:

```
Merhaba
```

Burada ne oldu?

1. dis() çalıştı  
2. mesaj değişkeni oluşturuldu  
3. ic fonksiyonu oluşturuldu  
4. ic return edildi  
5. dis() bitti  

Normalde mesaj silinmeliydi.  
Ama silinmedi.  

Çünkü closure mesaj değişkenini korudu.

---

# 2. Closure’un Teknik Tanımı

Closure şu 3 şeyin birleşimidir:

1. Inner function  
2. Outer function variable  
3. Function reference  

Memory modeli:

```
outer function
   mesaj = "Merhaba"

inner function
   mesaj referansını saklar
```

---

# 3. Closure Neden Var?

Closure şu durumlar için kullanılır:

- Decorator  
- Callback  
- State saklama  
- Function factory  
- Functional programming  

---

# 4. Function Factory Örneği

```python
def carp(n):
    def ic(x):
        return x * n
    return ic

ikiyle_carp = carp(2)
print(ikiyle_carp(5))
```

Output:

```
10
```

Burada:

- n = 2  
- Closure tarafından saklandı  

Memory:

```
ikiyle_carp → ic function
             → n = 2 saklı
```

---

# 5. Closure Nasıl Çalışır (Step-by-Step)

```python
def dis():
    x = 10

    def ic():
        print(x)

    return ic
```

Adımlar:

1. dis çağrılır  
2. x oluşturulur  
3. ic oluşturulur  
4. ic, x referansını saklar  
5. dis biter  
6. ic hala x’i bilir  

---

# 6. Lexical Scoping Nedir?

Lexical scoping:

Değişkenler, tanımlandıkları scope’a göre bulunur.

Python scope sırası:

LEGB kuralı:

1. Local  
2. Enclosing  
3. Global  
4. Built-in  

---

## Örnek

```python
x = "global"

def dis():
    x = "enclosing"

    def ic():
        print(x)

    ic()

dis()
```

Output:

```
enclosing
```

Python önce enclosing scope’a bakar.

---

# 7. LEGB Kuralı (Local Önceliği)

```python
x = "global"

def dis():
    x = "enclosing"

    def ic():
        x = "local"
        print(x)

    ic()

dis()
```

Output:

```
local
```

---

# 8. nonlocal Keyword

nonlocal:

Enclosing scope’taki değişkeni değiştirmek için kullanılır.

---

## Problem

```python
def dis():
    x = 10

    def ic():
        x = x + 1

    ic()
```

Bu hata verir:

```
UnboundLocalError
```

Çünkü Python x’i local sanır.

---

# 9. Çözüm: nonlocal

```python
def dis():
    x = 10

    def ic():
        nonlocal x
        x = x + 1
        print(x)

    ic()

dis()
```

Output:

```
11
```

Memory:

```
ic → x değişkenini enclosing scope’ta değiştirir
```

---

# 10. Closure ile State Saklama

```python
def sayac():
    count = 0

    def arttir():
        nonlocal count
        count += 1
        return count

    return arttir

counter = sayac()

print(counter())
print(counter())
print(counter())
```

Output:

```
1
2
3
```

count closure içinde saklanır.

---

# 11. Closure vs Class

Closure:

```python
def counter():
    count = 0

    def inc():
        nonlocal count
        count += 1
        return count

    return inc
```

Class:

```python
class Counter:
    def __init__(self):
        self.count = 0

    def inc(self):
        self.count += 1
        return self.count
```

İkisi de state saklar.

Closure → Functional yaklaşım  
Class → OOP yaklaşım  

---

# 12. Decorator ve Closure İlişkisi

Decorator aslında closure kullanır.

```python
def deco(func):
    def wrapper():
        print("before")
        func()
        print("after")

    return wrapper
```

wrapper → func referansını saklar.

Bu bir closure’dır.

---

# 13. Closure Memory Yapısı

Closure şunu saklar:

- Function reference  
- Enclosing variables  

Closure inspect etme:

```python
print(f.__closure__)
```

Output:

```
(<cell at ...>,)
```

---

# 14. Gerçek Hayat Kullanımı

Django decorator örneği:

```python
@login_required
```

func referansı closure içinde saklanır.

---

# 15. Closure Neden Önemli?

Şu sistemlerin temelidir:

- Decorators  
- Callbacks  
- Functional programming  
- Django decorators  
- Middleware mantığı  

---

# 16. Closure Olmadan Decorator Olmaz

```python
def decorator(func):
```

func closure ile saklanır.

---

# 17. Senior Level Örnek

```python
def power(n):
    def calculate(x):
        return x ** n
    return calculate

square = power(2)
print(square(5))
```

Output:

```
25
```

n closure’da saklandı.

---

# 18. Closure vs Global

Closure:

- Güvenli  
- Encapsulated  
- State kontrollü  

Global:

- Riskli  
- Her yerden değiştirilebilir  
- Debug zor  

---

# 19. Mülakat Soruları

Soru: Closure nedir?  
Cevap: Inner function’un enclosing scope’taki değişkenleri hatırlamasıdır.

Soru: Lexical scoping nedir?  
Cevap: Variable lookup tanımlandığı scope’a göre yapılır (LEGB).

Soru: nonlocal ne yapar?  
Cevap: Enclosing scope’taki variable’ı değiştirir.

Soru: Decorator closure kullanır mı?  
Cevap: Evet.

---

# 20. En Kritik Özet

- Closure → Function state saklar  
- Enclosing variable saklar  
- Lexical scoping → Variable lookup kuralıdır  
- LEGB → Local → Enclosing → Global → Built-in  
- nonlocal → Enclosing variable değiştirmek için kullanılır  
- Decorator → Closure kullanır  

Closure kavramını anlamadan decorator, callback ve framework mimarilerini tam anlamak mümkün değildir.