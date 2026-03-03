# BÖLÜM 1 — BASIC CONCEPTS (Temel Programlama ve Python Kavramları)

Bu bölüm:

- Programlama mantığını temellendirir
- Python’ın çalışma modelini açıklar
- Bellek yönetimini kavratır
- Algoritmik düşünceyi başlatır
- Junior–Mid seviye mülakatların büyük kısmını kapsar

---

# 1. Programlama Nedir?

Programlama, bir problemi çözmek için bilgisayara adım adım talimat vermektir.

Bilgisayarlar yalnızca **makine dili (binary: 0 ve 1)** anlar.  
Biz yüksek seviyeli diller kullanırız (Python, C, Java gibi).  

Bu kodlar makine diline çevrilerek çalıştırılır.

## Programlama Süreci

1. Problemi analiz etmek  
2. Algoritmayı tasarlamak  
3. Kodlamak  
4. Test etmek  

> Programlama = Problem Çözme + Algoritma + Uygulama

---

# 2. Compiler vs Interpreter

Kodun çalışabilmesi için makine diline çevrilmesi gerekir.

## Compiler (Derleyici)

- Tüm kodu tek seferde çevirir
- Çalıştırılabilir dosya üretir
- Örnek: C, C++

Çalışma modeli:

```
Source Code → Compiler → Executable → Run
```

Avantaj:
- Yüksek performans

Dezavantaj:
- Küçük hata bile derlemeyi durdurur

---

## Interpreter (Yorumlayıcı)

- Kodu satır satır çalıştırır
- Anlık çeviri yapar
- Örnek: Python

```
Source Code → Interpreter → Line-by-line execution
```

Avantaj:
- Geliştirme hızlıdır
- Esnektir

### Önemli Teknik Detay (Mülakat Sorusu Gelir)

Python tamamen interpreter değildir.

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

---

# 3. Syntax vs Semantic

## Syntax (Sözdizimi)

Yazım kurallarıdır.

Yanlış:

```python
if x == 5
    print(x)
```

Doğru:

```python
if x == 5:
    print(x)
```

Syntax hatası varsa program çalışmaz.

---

## Semantic (Mantıksal Anlam)

Kod yazım olarak doğru olabilir ama mantıksal olarak yanlış olabilir.

```python
x = 5
y = 0
print(x / y)
```

Bu runtime hatası üretir.

| Tür | Açıklama |
|------|----------|
| Syntax Error | Yazım hatası |
| Semantic Error | Mantık hatası |
| Runtime Error | Çalışma zamanı hatası |

---

# 4. Compile Time vs Runtime

## Compile Time
- Kod derlenirken oluşur
- Syntax hataları
- Statik tip hataları

## Runtime
- Program çalışırken oluşur
- ZeroDivisionError
- FileNotFoundError
- IndexError

Python dinamik tipli olduğu için çoğu hata runtime’da ortaya çıkar.

---

# 5. Variable (Değişken) Nedir?

Python’da değişken bir "kutu" değildir.

Değişken, bir nesneye referans tutan isimdir.

```python
x = 10
```

- `10` bir object’tir
- `x` bu object’e referanstır

> Python’da her şey object’tir.

---

# 6. Primitive vs Non-Primitive Types

Python’da teknik olarak her şey object’tir.

Ancak kavramsal ayrım önemlidir.

## Primitive (Basit Tipler)
- Tek değer
- Genellikle immutable
- `int`, `float`, `bool`, `str`

## Non-Primitive (Composite)
- Çoklu veri tutar
- Veri yapısıdır
- `list`, `dict`, `set`, `tuple`

---

# 7. Mutable vs Immutable

## Immutable

Oluşturulduktan sonra değiştirilemez.

Örnek:
- int
- float
- str
- tuple

```python
x = 5
x = x + 1
```

Yeni object oluşturulur.

---

## Mutable

Aynı nesne üzerinde değişiklik yapılabilir.

Örnek:
- list
- dict
- set

```python
a = [1, 2]
a.append(3)
```

Yeni liste oluşmaz. Aynı nesne değişir.

---

# 8. Stack vs Heap Memory

Python bellek modelini anlamak production için kritiktir.

## Stack

- Fonksiyon çağrıları
- Lokal referanslar
- LIFO mantığı
- Hızlıdır

## Heap

- Tüm Python nesneleri burada tutulur
- Dinamik bellek alanıdır
- Daha büyüktür

| Stack | Heap |
|--------|------|
| Referanslar | Nesneler |
| Küçük | Büyük |
| Hızlı | Görece yavaş |

---

# 9. Reference vs Value

Python’da değişkenler nesnelere referans tutar.

```python
a = [1, 2]
b = a
```

`b`, yeni liste oluşturmaz.  
Aynı nesneyi işaret eder.

```python
b.append(3)
```

Bu durumda `a` da değişir.

---

# 10. Memory Allocation

Python’da:

- Nesneler heap’te oluşturulur
- Referans sayısı tutulur
- Referans 0 olursa nesne silinir

```python
a = [1, 2]
b = a
```

Referans sayısı: 2

```python
del a
```

Referans sayısı: 1

---

# 11. Garbage Collection

Kullanılmayan nesnelerin bellekten temizlenmesidir.

Python iki mekanizma kullanır:

1. Reference Counting
2. Cycle Detector (circular reference temizler)

---

# 12. Memory Leak

Kullanılmayan ama referansı devam eden nesnelerin bellekte kalmasıdır.

Genellikle:

- Global değişkenler
- Circular reference
- Yanlış cache kullanımı
- Uzun yaşayan objeler

---

# 13. Time Complexity (Big-O)

Algoritmanın veri büyüdükçe nasıl davrandığını gösterir.

| Notation | Anlam |
|----------|--------|
| O(1) | Sabit |
| O(n) | Doğrusal |
| O(n²) | Karesel |
| O(log n) | Logaritmik |

```python
for i in range(n):
    print(i)
```

Bu algoritma O(n)’dir.

---

# 14. Space Complexity

Algoritmanın kullandığı ek bellek miktarıdır.

```python
def foo(n):
    arr = []
    for i in range(n):
        arr.append(i)
```

Bu fonksiyon O(n) bellek kullanır.

---

# 15. Algorithm Nedir?

Bir problemi çözmek için tanımlı adımlar bütünüdür.

Bir algoritma:

- Sonlu olmalı
- Deterministik olmalı
- Girdi almalı
- Çıktı üretmeli

---

# 16. Data Structure Nedir?

Veriyi organize etmek için kullanılan yapılardır.

Örnek:

- Array
- List
- Stack
- Queue
- Hash Table
- Tree
- Graph

> Doğru veri yapısı seçimi performansı doğrudan etkiler.

---

# 📌 Bu Bölüm Neden Kritik?

Bu konuları anlamadan:

- GIL’i anlayamazsın
- Django ORM davranışını kavrayamazsın
- Lazy evaluation mantığını çözemezsin
- Memory problemlerini debug edemezsin
- Teknik mülakatta ilk elemeden geçemezsin