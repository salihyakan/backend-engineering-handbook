# BÖLÜM 1 — BASIC CONCEPTS  
## MÜLAKAT SORULARI VE DETAYLI CEVAPLAR

Bu doküman:

- Teknik mülakatlarda çıkabilecek soruları içerir
- Konu bazlı ilerler
- Her sorunun net ve anlaşılır cevabı vardır
- Junior → Mid seviye için uygundur

---

# 1️⃣ PROGRAMLAMA TEMELLERİ

### Soru 1: Programlama nedir?

**Cevap:**

Programlama, bir problemi çözmek için bilgisayara adım adım talimat vermektir.

Temel süreç:

1. Problemi analiz etmek  
2. Algoritma tasarlamak  
3. Kod yazmak  
4. Test etmek  

Programlama özünde problem çözme disiplinidir.

---

### Soru 2: Algoritma nedir?

**Cevap:**

Bir problemi çözmek için tanımlanmış sonlu ve deterministik adımlar bütünüdür.

Bir algoritma:

- Sonlu olmalıdır  
- Deterministik olmalıdır  
- Girdi almalıdır  
- Çıktı üretmelidir  

---

# 2️⃣ COMPILER vs INTERPRETER

### Soru 3: Compiler ve Interpreter arasındaki fark nedir?

**Cevap:**

| Compiler | Interpreter |
|-----------|------------|
| Tüm kodu bir kerede çevirir | Satır satır çalıştırır |
| Executable üretir | Anlık çalıştırır |
| Hızlıdır | Daha esnektir |
| Hata varsa derleme durur | Hata satırında durur |

---

### Soru 4: Python interpreter mıdır compiler mı?

**Cevap:**

Python klasik anlamda interpreter değildir.

CPython çalışma modeli:

```
Source Code
↓
Bytecode (.pyc)
↓
Python Virtual Machine (PVM)
↓
Execution
```

Önce bytecode üretir, sonra PVM çalıştırır.

---

# 3️⃣ SYNTAX – SEMANTIC – RUNTIME

### Soru 5: Syntax error nedir?

**Cevap:**

Kod yazım kurallarının ihlal edilmesidir.

Program çalışmadan hata verir.

---

### Soru 6: Semantic error nedir?

**Cevap:**

Kod yazım olarak doğrudur fakat mantıksal olarak yanlıştır.

---

### Soru 7: Runtime error nedir?

**Cevap:**

Program çalışırken oluşan hatadır.

Örnek:

- ZeroDivisionError  
- IndexError  
- FileNotFoundError  

---

# 4️⃣ VARIABLE & MEMORY

### Soru 8: Python’da değişken nedir?

**Cevap:**

Değişken bir nesneye referans tutan isimdir.

Python’da değişken bir “kutu” değildir.

```
x = 10
```

`x` → 10 object’ine referans tutar.

---

### Soru 9: Python’da her şey object midir?

**Cevap:**

Evet. Python tamamen object-oriented bir dildir.  
int, str, list dahil her şey object’tir.

---

### Soru 10: Stack ve Heap arasındaki fark nedir?

**Cevap:**

| Stack | Heap |
|--------|------|
| Fonksiyon çağrıları | Nesneler |
| Lokal referanslar | Dinamik bellek |
| Hızlı | Görece yavaş |

Python’daki tüm nesneler heap’te tutulur.

---

# 5️⃣ MUTABLE vs IMMUTABLE

### Soru 11: Mutable nedir?

**Cevap:**

Oluşturulduktan sonra değiştirilebilen nesnelerdir.

Örnek:

- list  
- dict  
- set  

---

### Soru 12: Immutable nedir?

**Cevap:**

Oluşturulduktan sonra değiştirilemeyen nesnelerdir.

Örnek:

- int  
- float  
- str  
- tuple  

---

### Soru 13: Bu kodun çıktısı nedir?

```
a = [1, 2]
b = a
b.append(3)
print(a)
```

**Cevap:**

```
[1, 2, 3]
```

Çünkü `b`, `a` ile aynı nesneyi referans eder.

---

### Soru 14: Bu kodda ne olur?

```
x = 5
y = x
x = 10
print(y)
```

**Cevap:**

```
5
```

Çünkü int immutable’dır.  
`x = 10` yeni object oluşturur.

---

# 6️⃣ MEMORY MANAGEMENT

### Soru 15: Python’da garbage collection nasıl çalışır?

**Cevap:**

İki mekanizma vardır:

1. Reference Counting  
2. Cycle Detector  

Referans sayısı 0 olursa nesne silinir.

---

### Soru 16: Memory leak nedir?

**Cevap:**

Kullanılmayan fakat referansı devam eden nesnelerin bellekte kalmasıdır.

Genellikle:

- Global değişkenler  
- Circular reference  
- Yanlış cache kullanımı  

---

# 7️⃣ TIME & SPACE COMPLEXITY

### Soru 17: Big-O nedir?

**Cevap:**

Algoritmanın veri büyüdükçe zaman maliyetinin nasıl arttığını gösterir.

---

### Soru 18: O(1) nedir?

**Cevap:**

Sabit zaman karmaşıklığıdır.  
Veri büyüklüğünden bağımsızdır.

---

### Soru 19: Bu kodun time complexity’si nedir?

```
for i in range(n):
    print(i)
```

**Cevap:**

O(n)

---

### Soru 20: Nested loop’un complexity’si nedir?

```
for i in range(n):
    for j in range(n):
        print(i, j)
```

**Cevap:**

O(n²)

---

### Soru 21: Space complexity nedir?

**Cevap:**

Algoritmanın kullandığı ek bellek miktarıdır.

---

### Soru 22: Bu fonksiyonun space complexity’si nedir?

```
def foo(n):
    arr = []
    for i in range(n):
        arr.append(i)
```

**Cevap:**

O(n)

---

# 8️⃣ KARIŞIK MÜLAKAT SORULARI

### Soru 23: Python dinamik tipli ne demektir?

**Cevap:**

Değişken tipi runtime sırasında belirlenir.

```
x = 5
x = "hello"
```

Aynı değişken farklı tip tutabilir.

---

### Soru 24: Python’da reference vs value kavramı nasıl işler?

**Cevap:**

Python’da değişkenler nesnelere referans tutar.

Mutable nesnelerde değişiklik referans üzerinden yansır.  
Immutable nesnelerde yeni object oluşturulur.

---

### Soru 25: Performans açısından neden doğru veri yapısı seçimi önemlidir?

**Cevap:**

Yanlış veri yapısı:

- Zaman karmaşıklığını artırır  
- Bellek kullanımını artırır  
- Production performansını düşürür  

Örneğin:

- Arama için list → O(n)  
- Arama için dict → O(1)  

---

# 🎯 BU DOSYANIN AMACI

Bu sorular:

- Teknik eleme aşamasını geçmeni sağlar  
- Temel kavramlarda sağlam durmanı sağlar  
- Memory ve complexity konularında bilinç kazandırır  
- Daha ileri konular (GIL, Django ORM, async) için altyapı oluşturur  