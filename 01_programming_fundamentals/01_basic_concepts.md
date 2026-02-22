# 1.1 Temel Programlama Kavramları

---

## 1️⃣ Programlama Nedir?

Programlama, bir bilgisayara belirli bir problemi çözmesi için adım adım talimatlar vermektir.

Bilgisayarlar yalnızca makine dili (binary: 0 ve 1) anlar. Biz insanlar ise Python, C, Java gibi yüksek seviyeli programlama dilleri kullanırız. Yazdığımız bu kodlar, makine diline çevrilerek çalıştırılır.

Programlama süreci üç temel aşamadan oluşur:

1. Problemi analiz etmek  
2. Çözüm algoritmasını tasarlamak  
3. Bu çözümü bir programlama dili ile ifade etmek  

> Programlama = Problem Çözme + Algoritma + Kod

---

## 2️⃣ Compiler vs Interpreter

Bir programlama diliyle yazılmış kodun çalışabilmesi için makine diline çevrilmesi gerekir.

### Compiler (Derleyici)

- Tüm kodu tek seferde makine diline çevirir.
- Çıktı olarak çalıştırılabilir dosya üretir.
- Örnek: C, C++

Çalışma modeli:

```
Source Code → Compiler → Executable File → Run
```

**Avantajı:**  
- Çalışma zamanı daha hızlıdır.

**Dezavantajı:**  
- Küçük bir hata bile olsa derleme tamamlanmaz.

---

### Interpreter (Yorumlayıcı)

- Kodu satır satır çalıştırır.
- Çalışma anında çeviri yapar.
- Örnek: Python

Çalışma modeli:

```
Source Code → Interpreter → Execute line by line
```

**Avantajı:**  
- Geliştirme süreci hızlıdır.
- Dinamik yapı sağlar.

> Not: Python tamamen interpreter değildir. CPython önce bytecode üretir, ardından Python Virtual Machine (PVM) bu bytecode’u çalıştırır.

---

## 3️⃣ Syntax vs Semantic

### Syntax (Sözdizimi)

Kodun yazım kurallarıdır.

Yanlış örnek:

```python
if x == 5
    print(x)
```

Doğru örnek:

```python
if x == 5:
    print(x)
```

Syntax hatası varsa program çalışmaz.

---

### Semantic (Anlam)

Kod yazım olarak doğru olabilir fakat mantıksal olarak hatalı olabilir.

```python
x = 5
y = 0
print(x / y)
```

Bu kod syntax olarak doğrudur ancak runtime sırasında `ZeroDivisionError` oluşur.

| Tür | Açıklama |
|------|----------|
| Syntax Error | Yazım kuralı hatası |
| Semantic Error | Mantık hatası |

---

## 4️⃣ Runtime vs Compile Time

### Compile Time

Kod derlenirken gerçekleşen aşamadır.

Örnek:
- Syntax hatası
- Tip uyumsuzluğu (statik dillerde)

---

### Runtime

Program çalışırken gerçekleşen aşamadır.

Örnek:
- Sıfıra bölme
- Dosya bulunamaması
- Index out of range

Python dinamik tipli olduğu için çoğu hata runtime sırasında ortaya çıkar.

---

## 5️⃣ Variable (Değişken) Nedir?

Değişken, bellekte bir nesneyi referans eden isimdir.

```python
x = 10
```

Burada:
- `10` bir nesnedir (object)
- `x` bu nesneyi işaret eden referanstır

> Python’da değişken bir "kutu" değildir. Bir etikettir (label).

---

## 6️⃣ Primitive vs Non-Primitive Types

Python’da teknik olarak her şey object’tir. Ancak genel programlama açısından bu ayrım önemlidir.

### Primitive Types
- Tek değer tutar
- Genellikle immutable’dır
- Örnek: `int`, `float`, `bool`

### Non-Primitive (Composite) Types
- Birden fazla değer tutabilir
- Veri yapısıdır
- Örnek: `list`, `dict`, `set`

---

## 7️⃣ Mutable vs Immutable

Mülakatların favori konularındandır.

### Immutable (Değiştirilemez)

Oluşturulduktan sonra değiştirilemez.  
Değişiklik yapılırsa yeni nesne oluşturulur.

Örnek:
- `int`
- `float`
- `str`
- `tuple`

```python
x = 5
x = x + 1
```

Burada `5` değişmez. Yeni `6` nesnesi oluşturulur.

---

### Mutable (Değiştirilebilir)

Aynı nesne üzerinde değişiklik yapılabilir.

Örnek:
- `list`
- `dict`
- `set`

```python
a = [1, 2]
a.append(3)
```

Yeni liste oluşmaz, mevcut liste değişir.

---

## 8️⃣ Stack vs Heap Memory

Bellek iki ana alana ayrılır:

### Stack
- Fonksiyon çağrıları
- Lokal değişken referansları
- LIFO (Last In First Out)
- Hızlıdır

### Heap
- Dinamik olarak oluşturulan nesneler
- Daha büyük bellek alanı
- Python nesneleri heap üzerinde tutulur

| Stack | Heap |
|--------|------|
| Fonksiyon çağrıları | Nesneler |
| Hızlı | Görece yavaş |
| Küçük | Büyük |

---

## 9️⃣ Reference vs Value

### Value Type
Değer doğrudan kopyalanır.

### Reference Type
Bellekteki adres kopyalanır.

Python’da değişkenler nesnelere referans tutar.

```python
a = [1, 2]
b = a
```

`b` yeni liste oluşturmaz.  
Aynı nesneyi referans eder.

---

## 🔟 Memory Allocation Temelleri

Python’da:

- Nesneler heap’te oluşturulur.
- Referans sayısı tutulur.
- Referans sayısı 0 olursa nesne silinir.

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

## 1️⃣1️⃣ Garbage Collection Nedir?

Kullanılmayan nesnelerin bellekten temizlenmesidir.

Python iki mekanizma kullanır:

1. Reference Counting
2. Cycle Detector (döngüsel referansları temizler)

---

## 1️⃣2️⃣ Memory Leak Nedir?

Kullanılmayan fakat referansı devam eden nesnelerin bellekte kalmasıdır.

Genellikle şu durumlarda oluşur:

- Global veri yapıları
- Circular reference
- Yanlış cache kullanımı
- Uzun yaşayan objeler

---

## 1️⃣3️⃣ Time Complexity (Big O Notation)

Algoritmanın veri büyüdükçe nasıl davrandığını ölçer.

| Notation | Açıklama |
|----------|----------|
| O(1) | Sabit zaman |
| O(n) | Doğrusal |
| O(n²) | Karesel |

Örnek:

```python
for i in range(n):
    print(i)
```

Bu algoritma O(n)’dir.

---

## 1️⃣4️⃣ Space Complexity

Algoritmanın kullandığı ek bellek miktarıdır.

```python
def foo(n):
    arr = []
    for i in range(n):
        arr.append(i)
```

Bu fonksiyon O(n) bellek kullanır.

---

## 1️⃣5️⃣ Algorithm Nedir?

Bir problemi çözmek için tanımlanmış adımlar bütünüdür.

Bir algoritma:

- Sonlu olmalıdır
- Deterministik olmalıdır
- Girdi almalıdır
- Çıktı üretmelidir

---

## 1️⃣6️⃣ Data Structure Nedir?

Veriyi organize etmek için kullanılan yapılardır.

Örnekler:

- Array
- List
- Stack
- Queue
- Hash Table
- Tree
- Graph

> Doğru veri yapısı seçimi performansı doğrudan etkiler.

---

## 📌 Bu Bölümün Önemi

Bu konular:

- GIL’i anlamanı sağlar
- Django ORM’i anlamanı sağlar
- QuerySet lazy evaluation mantığını kavratır
- Production memory problemlerini çözmene yardımcı olur
- Mülakatta temel eleme aşamasını geçmeni sağlar