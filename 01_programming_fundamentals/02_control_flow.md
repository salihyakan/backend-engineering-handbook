# 1.2 Control Flow (Kontrol Akışı)

Bir program yazdığımızda kodlar varsayılan olarak **yukarıdan aşağıya doğru sırayla çalışır**.

Buna:

**Sequential Execution (Sıralı Çalışma)** denir.

```python
print("Program başladı")
print("Veri işleniyor")
print("Program bitti")
```

Çıktı:

```
Program başladı
Veri işleniyor
Program bitti
```

Ancak gerçek uygulamalarda programların:

- karar vermesi gerekir
- bazı işlemleri tekrar etmesi gerekir
- bazı durumlarda işlemi durdurması gerekir
- bazı durumları atlaması gerekir

İşte bu davranışları kontrol eden yapılara:

**Control Flow (Kontrol Akışı)** denir.

---

# Control Flow Neden Önemlidir?

Gerçek bir uygulama düşünelim.

Bir kullanıcı **login olmaya çalışıyor**.

Program şu soruların cevabını vermelidir:

- kullanıcı var mı?
- şifre doğru mu?
- hesap aktif mi?
- admin mi?

Bu kararlar **control flow** yapıları ile yönetilir.

---

# Python Control Flow Yapıları

Python'da kontrol akışı şu yapılardan oluşur:

| Yapı | Amaç |
|-----|------|
| if / elif / else | koşul kontrolü |
| match-case | çoklu durum kontrolü |
| for | iterable üzerinde döngü |
| while | koşula bağlı döngü |
| break | döngüyü durdurma |
| continue | iterasyonu atlama |
| pass | boş blok oluşturma |

---

# 1️⃣ if / elif / else

Programın **koşula göre karar vermesini sağlar**.

## Syntax

```python
if condition:
    code
```

Mantık:

```
condition True ise → kod çalışır
False ise → çalışmaz
```

---

## Basit Örnek

```python
age = 18

if age >= 18:
    print("Reşit")
```

Çalışma:

```
age >= 18 → True
kod çalışır
```

Çıktı:

```
Reşit
```

---

## Gerçek Hayat Analojisi

ATM makinesi düşün.

```
if kart_gecerli:
    para_cek
```

Kart geçerli değilse işlem yapılmaz.

---

# if - else

İki farklı durum varsa kullanılır.

```python
if condition:
    code_if_true
else:
    code_if_false
```

---

## Örnek

```python
age = 15

if age >= 18:
    print("Reşit")
else:
    print("Reşit değil")
```

Çıktı:

```
Reşit değil
```

---

## E-ticaret Örneği

```python
if stock > 0:
    print("Ürün satın alınabilir")
else:
    print("Stok yok")
```

---

# if - elif - else

Birden fazla durum varsa kullanılır.

```python
if condition1:
    code
elif condition2:
    code
else:
    code
```

Python koşulları **yukarıdan aşağıya kontrol eder**.

İlk True olan blok çalışır.

---

## Örnek

```python
score = 75

if score >= 90:
    print("A")
elif score >= 70:
    print("B")
else:
    print("C")
```

Çıktı:

```
B
```

---

# Backend Login Örneği

```python
if not user:
    print("Kullanıcı bulunamadı")

elif not user.is_active:
    print("Hesap pasif")

else:
    print("Giriş başarılı")
```

---

# 2️⃣ Nested if (İç İçe if)

Bir if bloğunun içinde başka bir if kullanılmasıdır.

```python
age = 20
has_id = True

if age >= 18:
    if has_id:
        print("Giriş yapabilir")
```

---

## Gerçek Hayat

Gece kulübü giriş kontrolü:

```
if yaş >= 18
    if kimlik_var
        içeri_al
```

---

## Daha Temiz Yazım

```python
if age >= 18 and has_id:
    print("Giriş yapabilir")
```

Bu yaklaşım **daha okunabilir ve profesyoneldir**.

---

# 3️⃣ Ternary Operator (Tek Satır if)

Kısa if-else yazımıdır.

```python
value_if_true if condition else value_if_false
```

---

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

## Backend Kullanımı

```python
status = "Online" if user.is_active else "Offline"
```

---

# 4️⃣ match-case (Python 3.10+)

Python’ın **switch-case benzeri yapısıdır**.

Ama Python’daki match-case daha güçlüdür çünkü **pattern matching** yapabilir.

---

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

## Backend Kullanımı

HTTP method kontrolü:

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

# match-case vs if

| match-case | if |
|---|---|
| çoklu sabit durum | genel koşullar |
| daha okunabilir | daha esnek |
| pattern matching | boolean logic |

---

# 5️⃣ for Loop

Tekrarlayan işlemler için kullanılır.

Python’daki for loop **iterator tabanlıdır**.

---

# Iterable Nedir?

Üzerinde dolaşılabilen veri yapılarıdır:

- list
- tuple
- set
- string
- dictionary

---

## Syntax

```python
for variable in iterable:
    code
```

---

## Örnek

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

## Django Örneği

```python
for user in User.objects.all():
    print(user.username)
```

---

# 6️⃣ while Loop

Koşul True olduğu sürece çalışır.

```python
while condition:
    code
```

---

## Örnek

```python
i = 0

while i < 5:
    print(i)
    i += 1
```

---

# while vs for

| for | while |
|----|----|
| iterable ile çalışır | koşul ile çalışır |
| daha güvenli | sonsuz döngü riski |
| pythonic | daha düşük seviyeli |

---

# Sonsuz Döngü

```python
while True:
    process_request()
```

Server'lar bu mantıkla çalışır.

---

# 7️⃣ break ve continue

Döngü kontrol araçlarıdır.

---

# break

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

# continue

O iterasyonu atlar.

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

# Backend Örneği

```python
for user in users:

    if not user.is_active:
        continue

    send_email(user)
```

---

# 8️⃣ pass Keyword

Hiçbir şey yapmaz.

Ama Python syntax gereği blok ister.

```python
if True:
    pass
```

---

# Placeholder Kullanımı

```python
def login():
    pass
```

```python
class User:
    pass
```

---

# Gerçek Hayat Analojisi

```
pass = "Bu kısmı sonra dolduracağım"
```

---

# Control Flow Nasıl Çalışır?

Python programı:

1️⃣ yukarıdan aşağıya çalışır  
2️⃣ if görünce karar verir  
3️⃣ loop görünce tekrar eder  
4️⃣ break görünce döngüyü durdurur  
5️⃣ continue görünce iterasyonu atlar  

---

# Birlikte Kullanım Örneği

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

# Backend Açısından Önemi

Control flow şu sistemlerin temelidir:

- authentication
- API logic
- permission kontrolü
- request handling
- validation logic
- middleware

---

# Mülakatlarda Sorulan Sorular

Sık sorulanlar:

- Python’da for nasıl çalışır?
- while ile for farkı nedir?
- break vs continue farkı nedir?
- ternary operator nedir?
- match-case ne zaman kullanılır?
- nested if neden kötü olabilir?

---

# Tek Cümlelik Mülakat Tanımı

Control flow, bir programın hangi koşullarda hangi kodların çalışacağını belirleyen yapılardır.