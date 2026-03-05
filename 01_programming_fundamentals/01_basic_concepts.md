# BÖLÜM 1 — BASIC CONCEPTS (Temel Programlama ve Python Kavramları)

Bu bölüm programlamanın temelini oluşturur.

Bu bölümde öğrenilecek konular:

- Programlama mantığı
- Python’ın çalışma modeli
- Bellek yönetimi
- Değişken ve referans sistemi
- Algoritmik düşünme
- Zaman ve bellek karmaşıklığı

Bu konular:

- Python mülakatlarının büyük kısmında sorulur
- Backend geliştirmenin temelini oluşturur
- Performans problemlerini anlamayı sağlar

---

# 1️⃣ Programlama Nedir?

Programlama, bir problemi çözmek için bilgisayara **adım adım talimatlar vermektir**.

Bilgisayarlar yalnızca **makine dili** anlar.

Makine dili:

```
010101010101
```

İnsanların bu şekilde yazması çok zor olduğu için **yüksek seviyeli diller** kullanılır.

Örnek:

- Python
- Java
- C
- C++
- Go
- JavaScript

Bu diller daha sonra **makine diline çevrilir**.

---

# Programlama Süreci

Bir yazılım geliştirme süreci genelde şu adımlardan oluşur:

1️⃣ Problemi anlamak  
2️⃣ Algoritma tasarlamak  
3️⃣ Kod yazmak  
4️⃣ Test etmek  
5️⃣ Hataları düzeltmek  

Basit formül:

```
Programlama = Problem çözme + Algoritma + Kod
```

---

# Gerçek Hayat Analojisi

Bir yemek tarifi düşün.

```
1. Tavayı ısıt
2. Yağ ekle
3. Yumurtayı kır
4. Karıştır
```

Bu aslında bir **algoritmadır**.

Bilgisayar programları da aynı mantıkla çalışır.

---

# 2️⃣ Compiler vs Interpreter

Kodun çalışabilmesi için **makine diline çevrilmesi gerekir**.

Bu çeviriyi yapan iki sistem vardır.

---

# Compiler (Derleyici)

Compiler tüm programı **tek seferde çevirir**.

Çalışma modeli:

```
Source Code → Compiler → Executable → Run
```

Örnek diller:

- C
- C++
- Rust
- Go

Avantajları:

- Çok hızlı çalışır
- Optimize edilmiş kod üretir

Dezavantajları:

- Küçük hata bile tüm derlemeyi durdurur

---

# Interpreter (Yorumlayıcı)

Interpreter kodu **satır satır çalıştırır**.

Çalışma modeli:

```
Source Code → Interpreter → Execute
```

Örnek:

- Python
- JavaScript
- Ruby

Avantajları:

- Geliştirme hızlıdır
- Test etmek kolaydır

Dezavantajları:

- Genelde daha yavaştır

---

# Python Gerçekte Nasıl Çalışır?

Python tamamen interpreter değildir.

CPython çalışma modeli:

```
Python Source Code (.py)
↓
Bytecode (.pyc)
↓
Python Virtual Machine (PVM)
↓
Execution
```

Yani:

```
Python = Bytecode + Virtual Machine
```

---

# 3️⃣ Syntax vs Semantic

Programlama dillerinde iki tür hata vardır.

---

# Syntax (Sözdizimi)

Dil kurallarına uymayan yazım hatalarıdır.

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

Syntax hatası varsa program **çalışmaz**.

---

# Semantic (Mantık Hatası)

Kod yazım olarak doğru olabilir ama mantık yanlış olabilir.

```python
price = 100
discount = 200

final = price - discount
```

Kod çalışır ama sonuç mantıksızdır.

---

# Runtime Error

Program çalışırken oluşan hatalardır.

```python
x = 10
y = 0

print(x / y)
```

Hata:

```
ZeroDivisionError
```

---

# Hata Türleri

| Tür | Açıklama |
|----|----|
| Syntax Error | Yazım hatası |
| Semantic Error | Mantık hatası |
| Runtime Error | Çalışma zamanı hatası |

---

# 4️⃣ Compile Time vs Runtime

---

# Compile Time

Kod derlenirken oluşan hatalardır.

Örnek:

- Syntax hataları
- Tip hataları (statik dillerde)

---

# Runtime

Program çalışırken oluşan hatalardır.

Örnek:

- ZeroDivisionError
- FileNotFoundError
- IndexError

Python dinamik tipli olduğu için çoğu hata **runtime’da ortaya çıkar**.

---

# 5️⃣ Variable (Değişken) Nedir?

Çoğu kişi değişkeni **kutu** gibi düşünür.

Ama Python'da durum farklıdır.

Python'da değişken:

```
Bir nesneye referans tutan isimdir
```

Örnek:

```python
x = 10
```

Burada:

```
10 → object
x → referans
```

---

# Python'da Her Şey Object'tir

Python'da şu değerlerin hepsi object'tir:

- int
- float
- string
- list
- dict
- function
- class

---

# 6️⃣ Primitive vs Non-Primitive Types

Python’da teknik olarak her şey object olsa da kavramsal ayrım yapılır.

---

# Primitive Types

Tek bir değer tutar.

Örnek:

- int
- float
- bool
- str

---

# Non-Primitive Types

Birden fazla veri tutar.

Örnek:

- list
- dict
- set
- tuple

---

# 7️⃣ Mutable vs Immutable

Bu Python'daki en kritik kavramlardan biridir.

---

# Immutable

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

Burada yeni object oluşur.

---

# Mutable

Aynı nesne değiştirilebilir.

Örnek:

- list
- dict
- set

```python
a = [1,2]
a.append(3)
```

Aynı liste değişir.

---

# Mutable vs Immutable Farkı

| Tür | Özellik |
|----|----|
| Immutable | değiştirilemez |
| Mutable | değiştirilebilir |

---

# 8️⃣ Stack vs Heap Memory

Python'da bellek iki ana bölümden oluşur.

---

# Stack

Stack şu şeyleri tutar:

- fonksiyon çağrıları
- lokal değişken referansları

Özellikleri:

- küçük
- hızlı
- LIFO (Last In First Out)

---

# Heap

Heap şu şeyleri tutar:

- tüm Python nesneleri

Özellikleri:

- büyük
- dinamik
- daha yavaş

---

# Stack vs Heap

| Stack | Heap |
|------|------|
| Referanslar | Nesneler |
| Küçük | Büyük |
| Hızlı | Daha yavaş |

---

# 9️⃣ Reference vs Value

Python referans tabanlı çalışır.

Örnek:

```python
a = [1,2]
b = a
```

Bellekte:

```
a → list
b → aynı list
```

---

```python
b.append(3)
```

Sonuç:

```
a = [1,2,3]
b = [1,2,3]
```

Çünkü aynı nesneyi gösterirler.

---

# 10️⃣ Memory Allocation

Python'da nesneler **heap memory'de oluşturulur**.

Referans sayısı tutulur.

Örnek:

```python
a = [1,2]
```

Referans sayısı:

```
1
```

---

```python
b = a
```

Referans sayısı:

```
2
```

---

```python
del a
```

Referans sayısı:

```
1
```

Referans sayısı **0 olursa nesne silinir**.

---

# 11️⃣ Garbage Collection

Garbage collection kullanılmayan nesneleri temizler.

Python iki sistem kullanır.

---

# Reference Counting

Her nesnenin referans sayısı tutulur.

Referans sayısı 0 olunca:

```
object silinir
```

---

# Cycle Detector

Bazı durumlarda referans sayısı 0 olmaz.

Örnek:

```
A → B
B → A
```

Bu circular reference oluşturur.

Python'un cycle detector sistemi bunu temizler.

---

# 12️⃣ Memory Leak

Kullanılmayan ama referansı devam eden nesnelerin bellekte kalmasıdır.

Sebep olabilecek durumlar:

- global değişkenler
- circular references
- yanlış cache kullanımı
- büyük veri yapıları

---

# 13️⃣ Time Complexity (Big-O)

Bir algoritmanın veri büyüdükçe nasıl davrandığını gösterir.

---

# O(1)

Sabit zaman.

```python
arr[0]
```

---

# O(n)

Doğrusal zaman.

```python
for i in arr:
    print(i)
```

---

# O(n²)

Karesel zaman.

```python
for i in arr:
    for j in arr:
        print(i,j)
```

---

# O(log n)

Logaritmik zaman.

Genellikle binary search algoritmasında görülür.

---

# Complexity Tablosu

| Complexity | Anlam |
|----|----|
| O(1) | sabit |
| O(log n) | logaritmik |
| O(n) | doğrusal |
| O(n²) | karesel |

---

# 14️⃣ Space Complexity

Bir algoritmanın kullandığı **ek bellek miktarıdır**.

```python
def foo(n):

    arr = []

    for i in range(n):
        arr.append(i)
```

Bellek kullanımı:

```
O(n)
```

---

# 15️⃣ Algorithm Nedir?

Bir problemi çözmek için tanımlı adımlar bütünüdür.

Bir algoritma:

- sonlu olmalıdır
- belirli olmalıdır
- girdi almalıdır
- çıktı üretmelidir

---

# Basit Algoritma Örneği

Bir sayının çift olup olmadığını kontrol et.

```
1. sayıyı al
2. 2'ye böl
3. kalan 0 ise çift
```

Kod:

```python
if n % 2 == 0:
    print("Çift")
```

---

# 16️⃣ Data Structure Nedir?

Verilerin bellekte organize edilme biçimidir.

Örnek veri yapıları:

- Array
- List
- Stack
- Queue
- Hash Table
- Tree
- Graph

Doğru veri yapısı seçimi performansı ciddi şekilde etkiler.

---

# Bu Bölüm Neden Kritik?

Bu konular anlaşılmadan:

- Python memory modeli anlaşılmaz
- Django ORM davranışı anlaşılmaz
- performans problemleri çözülemez
- teknik mülakatlarda zorlanılır