# 1.2 Kontrol Akışı (Control Flow)

Kontrol akışı (control flow), bir programın hangi sırayla ve hangi koşullarda çalışacağını belirleyen yapılardır.

Varsayılan olarak programlar yukarıdan aşağıya doğru çalışır (sequential execution).  
Ancak gerçek uygulamalarda:

- Karar verme (if)
- Tekrar eden işlemler (loop)
- Döngüyü durdurma veya atlama (break, continue)
- Geçici olarak boş bırakma (pass)

gibi yapılara ihtiyaç duyulur.

Bu yapıların tamamına kontrol akışı denir.

---

# 1️⃣ if / elif / else Yapısı

`if`, belirli bir koşul doğruysa (True) bir kod bloğunu çalıştırır.

## Temel Yapı

```python
if condition:
    code
```

- `condition` → True ise çalışır
- False ise blok atlanır

---

## Basit Örnek

```python
age = 18

if age >= 18:
    print("Reşit")
```

---

## if - else Yapısı

```python
if condition:
    code_if_true
else:
    code_if_false
```

Örnek:

```python
age = 15

if age >= 18:
    print("Reşit")
else:
    print("Reşit değil")
```

---

## if - elif - else

Birden fazla koşul kontrol etmek için kullanılır.

```python
score = 75

if score >= 90:
    print("A")
elif score >= 70:
    print("B")
else:
    print("C")
```

Python koşulları yukarıdan aşağıya kontrol eder ve ilk True olan blokta durur.

---

## Backend Gerçek Hayat Örneği

```python
if user.is_authenticated:
    return dashboard
else:
    return login_page
```

Bu yapı Django view logic’inin temelidir.

---

# 2️⃣ Nested if (İç İçe if)

Bir `if` bloğu içinde başka bir `if` kullanılmasıdır.

```python
age = 20
has_id = True

if age >= 18:
    if has_id:
        print("Giriş yapabilir")
```

Çalışma sırası:

1. `age >= 18` kontrol edilir.
2. True ise ikinci if çalışır.
3. `has_id` True ise çıktı üretilir.

---

## Backend Örneği

```python
if user:
    if user.is_active:
        if user.is_admin:
            print("Admin paneli açıldı")
```

> Not: Gerçek projelerde bu yapı genelde tek koşula indirgenir:
>
> ```python
> if user and user.is_active and user.is_admin:
>     print("Admin paneli açıldı")
> ```

Bu daha temiz ve okunabilir bir yaklaşımdır.

---

# 3️⃣ Ternary Operator (Tek Satırlık if)

Kısa if-else yazımıdır.

## Syntax

```python
value_if_true if condition else value_if_false
```

## Örnek

```python
age = 20
result = "Reşit" if age >= 18 else "Reşit değil"
```

Normal hali:

```python
if age >= 18:
    result = "Reşit"
else:
    result = "Reşit değil"
```

---

## Django Kullanım Örneği

```python
status = "Online" if user.is_active else "Offline"
```

---

# 4️⃣ match-case (Python 3.10+)

`match-case`, Python’ın switch-case benzeri yapısıdır.  
Pattern matching desteği sunar.

## Temel Kullanım

```python
status = 404

match status:
    case 200:
        print("OK")
    case 404:
        print("Not Found")
    case 500:
        print("Server Error")
```

---

## Default Case

```python
match status:
    case 200:
        print("OK")
    case _:
        print("Unknown")
```

`_` → default anlamına gelir.

---

## Backend Örneği

```python
match request.method:
    case "GET":
        handle_get()
    case "POST":
        handle_post()
    case _:
        raise ValueError("Unsupported method")
```

---

# 5️⃣ for Loop

Tekrarlayan işlemler için kullanılır.

Python’da `for` loop iterator tabanlıdır.  
Yani iterable bir nesne üzerinde dolaşır.

## Temel Syntax

```python
for variable in iterable:
    code
```

---

## range Kullanımı

```python
for i in range(5):
    print(i)
```

Çıktı:

```
0
1
2
3
4
```

---

## Liste Üzerinde Kullanım

```python
users = ["Ali", "Ayşe", "Mehmet"]

for user in users:
    print(user)
```

---

## Django Gerçek Kullanım

```python
for user in User.objects.all():
    print(user.username)
```

Bu yapı QuerySet iteration mantığını gösterir.

---

# 6️⃣ while Loop

Koşul True olduğu sürece çalışır.

## Syntax

```python
while condition:
    code
```

## Örnek

```python
i = 0

while i < 5:
    print(i)
    i += 1
```

---

## Sonsuz Döngü

```python
while True:
    request = get_request()
    process(request)
```

Server’lar ve event loop yapıları bu mantıkla çalışır.

---

# 7️⃣ break ve continue

Döngü kontrol mekanizmalarıdır.

---

## break

Döngüyü tamamen durdurur.

```python
for i in range(10):
    if i == 5:
        break
    print(i)
```

Çıktı:

```
0
1
2
3
4
```

---

## continue

Bulunduğu iterasyonu atlar, döngü devam eder.

```python
for i in range(5):
    if i == 2:
        continue
    print(i)
```

Çıktı:

```
0
1
3
4
```

---

## Backend Örneği

```python
for user in users:
    if not user.is_active:
        continue
    send_email(user)
```

---

# 8️⃣ pass Keyword

Hiçbir şey yapmaz.  
Sadece sözdizimsel olarak blok gereksinimini karşılar.

## Örnek

```python
if True:
    pass
```

---

## Placeholder Kullanımı

```python
def foo():
    pass
```

```python
class User:
    pass
```

Development sürecinde geçici olarak kullanılır.

---

# 🔎 Control Flow Execution Mantığı

Python:

1. Yukarıdan aşağıya çalışır.
2. Koşul görürse karar verir.
3. Loop görürse tekrar eder.
4. break ile durur.
5. continue ile atlar.

---

# 🔥 Hepsi Birlikte Örnek

```python
users = ["Ali", "Ayşe", "Mehmet"]

for user in users:

    if user == "Ayşe":
        continue

    if user == "Mehmet":
        break

    print(user)
```

Çıktı:

```
Ali
```

---

# 📌 Backend ve Django Açısından Önemi

Bu konular şunların temelidir:

- Django QuerySet iteration
- Request handling
- API business logic
- Authentication kontrolü
- Middleware akışı
- Permission sistemi

---

# 🎯 Mülakat Önemi

Sık sorulan sorular:

- for loop nasıl çalışır?
- while ile for arasındaki fark nedir?
- break vs continue farkı nedir?
- ternary operator nedir?
- match-case ne zaman tercih edilir?
- Python’da truthy / falsy nedir?