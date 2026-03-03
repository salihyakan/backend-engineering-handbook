# BÖLÜM 1.2 — CONTROL FLOW  
## MÜLAKAT SORULARI VE DETAYLI CEVAPLAR

Bu doküman:

- Teknik mülakatlarda çıkabilecek soruları içerir
- Konu bazlı sistematik ilerler
- Her sorunun net ve anlaşılır cevabı vardır
- Junior → Mid seviye için uygundur

---

# 1️⃣ if / elif / else

### Soru 1: if yapısı nasıl çalışır?

**Cevap:**

if, bir koşulun True olup olmadığını kontrol eder.

Koşul True ise blok çalışır, False ise atlanır.

```python
if x > 10:
    print("Büyük")
```

Python koşulu boolean’a çevirerek değerlendirir.

---

### Soru 2: elif ne işe yarar?

**Cevap:**

Birden fazla koşul kontrol etmek için kullanılır.

Python yukarıdan aşağıya kontrol eder ve ilk True blokta durur.

```python
if score >= 90:
    print("A")
elif score >= 70:
    print("B")
else:
    print("C")
```

---

### Soru 3: Python’da truthy ve falsy nedir?

**Cevap:**

Boolean’a dönüştürüldüğünde False olan değerler falsy kabul edilir.

Falsy değerler:

- 0
- 0.0
- ""
- []
- {}
- None
- False

Diğer tüm değerler truthy kabul edilir.

---

### Soru 4: Bu kodun çıktısı nedir?

```python
if []:
    print("Hello")
else:
    print("World")
```

**Cevap:**

```
World
```

Çünkü boş liste falsy’dir.

---

# 2️⃣ Nested if

### Soru 5: Nested if nedir?

**Cevap:**

Bir if bloğu içinde başka bir if kullanılmasıdır.

```python
if user:
    if user.is_active:
        print("Active user")
```

Genellikle şu şekilde sadeleştirilir:

```python
if user and user.is_active:
    print("Active user")
```

---

### Soru 6: Nested if neden azaltılmalıdır?

**Cevap:**

- Okunabilirliği düşürür
- Karmaşıklığı artırır
- Maintainability’yi zorlaştırır

Profesyonel kodda sadeleştirme tercih edilir.

---

# 3️⃣ Ternary Operator

### Soru 7: Ternary operator nedir?

**Cevap:**

Tek satırlık if-else yazımıdır.

```python
result = "Pass" if score >= 50 else "Fail"
```

---

### Soru 8: Ternary operator ne zaman tercih edilir?

**Cevap:**

- Basit koşullarda
- Tek atama işlemlerinde
- Kod okunabilirliğini bozmayacak durumlarda

Karmaşık koşullarda önerilmez.

---

# 4️⃣ match-case

### Soru 9: match-case nedir?

**Cevap:**

Python 3.10+ ile gelen pattern matching yapısıdır.

Switch-case benzeri çalışır.

```python
match status:
    case 200:
        print("OK")
    case _:
        print("Error")
```

---

### Soru 10: match-case ile if-elif farkı nedir?

**Cevap:**

- match-case pattern matching yapabilir
- Daha temiz ve okunabilir olabilir
- Çoklu sabit değer kontrolünde avantajlıdır

---

# 5️⃣ for Loop

### Soru 11: Python’da for loop nasıl çalışır?

**Cevap:**

Iterator protokolünü kullanır.

Mantıksal olarak şu şekilde çalışır:

```python
iterator = iter(iterable)
while True:
    try:
        item = next(iterator)
    except StopIteration:
        break
```

---

### Soru 12: range(5) ne üretir?

**Cevap:**

0’dan başlar, 5’e kadar (5 hariç) üretir.

```
0 1 2 3 4
```

---

### Soru 13: for loop ile while arasındaki temel fark nedir?

**Cevap:**

| for | while |
|------|-------|
| Iterable üzerinde çalışır | Koşul tabanlıdır |
| Iterator kullanır | Manuel kontrol gerekir |
| Daha güvenlidir | Sonsuz döngü riski vardır |

---

### Soru 14: Bu kodun çıktısı nedir?

```python
for i in range(3):
    print(i)
```

**Cevap:**

```
0
1
2
```

---

# 6️⃣ while Loop

### Soru 15: while loop ne zaman kullanılır?

**Cevap:**

- İterasyon sayısı bilinmiyorsa
- Koşul bazlı çalışıyorsa
- Event loop / server mantığında

---

### Soru 16: Sonsuz döngü nasıl oluşur?

**Cevap:**

Koşul sürekli True kalırsa oluşur.

```python
while True:
    pass
```

---

# 7️⃣ break ve continue

### Soru 17: break ne yapar?

**Cevap:**

Döngüyü tamamen durdurur.

---

### Soru 18: continue ne yapar?

**Cevap:**

Mevcut iterasyonu atlar, döngü devam eder.

---

### Soru 19: Bu kodun çıktısı nedir?

```python
for i in range(5):
    if i == 3:
        break
    print(i)
```

**Cevap:**

```
0
1
2
```

---

### Soru 20: Bu kodun çıktısı nedir?

```python
for i in range(5):
    if i == 2:
        continue
    print(i)
```

**Cevap:**

```
0
1
3
4
```

---

# 8️⃣ pass

### Soru 21: pass keyword ne işe yarar?

**Cevap:**

Hiçbir şey yapmaz.  
Sadece boş blok tanımlamak için kullanılır.

```python
def foo():
    pass
```

---

### Soru 22: pass neden gereklidir?

**Cevap:**

Python’da blok boş bırakılamaz.  
Sözdizimsel gerekliliği karşılar.

---

# 9️⃣ Karma Mülakat Soruları

### Soru 23: Bu kodun çıktısı nedir?

```python
x = 0

if x:
    print("True")
else:
    print("False")
```

**Cevap:**

```
False
```

0 falsy’dir.

---

### Soru 24: Aşağıdaki kodda kaç kez "Hello" yazdırılır?

```python
for i in range(5):
    for j in range(3):
        print("Hello")
```

**Cevap:**

5 × 3 = 15 kez.

---

### Soru 25: Bu kodda hata var mı?

```python
i = 0
while i < 5:
    print(i)
```

**Cevap:**

Evet.  
i artırılmadığı için sonsuz döngü oluşur.

---

# 🎯 Bu Dosyanın Amacı

Bu sorular:

- Kontrol akışını gerçekten anlayıp anlamadığını ölçer
- Iterator mantığını kavratır
- Backend logic yazımının temelini oluşturur
- Teknik mülakatta eleme aşamasını geçmeni sağlar
- Kodun çalışma sırasını zihninde simüle etmeni öğretir