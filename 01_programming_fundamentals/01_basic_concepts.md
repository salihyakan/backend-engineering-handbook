# 16️⃣ Data Structure (Veri Yapısı) Nedir?

Data Structure (Veri Yapısı), verilerin bilgisayar belleğinde **nasıl organize edildiğini, nasıl saklandığını ve nasıl erişildiğini** tanımlayan yapılardır.

Bir program yazarken çoğu zaman problem şu olur:

- veriyi nereye koyacağız?
- veriye nasıl ulaşacağız?
- veriyi nasıl değiştireceğiz?

İşte bu soruların cevabını **veri yapıları** verir.

---

# Basit Tanım

Data Structure =

```
Verinin düzeni + veriye erişim yöntemi
```

---

# Veri Yapısı Neden Önemlidir?

Aynı problemi farklı veri yapıları ile çözmek **performansı dramatik şekilde değiştirebilir**.

Örneğin:

100 milyon elemanlı veri düşün.

| Yapı | Arama Süresi |
|-----|--------------|
| List | çok yavaş |
| Set | çok hızlı |

Sebebi veri yapılarının **farklı algoritmalar kullanmasıdır**.

---

# Gerçek Hayat Analojisi

Bir kütüphane düşün.

## Kötü organizasyon

Kitaplar rastgele yere konulmuş.

Bir kitabı bulmak için:

```
1. raf
2. raf
3. raf
...
```

Hepsini aramak gerekir.

Bu:

```
O(n)
```

yani **linear search**.

---

## İyi organizasyon

Kitaplar kategorilere ayrılmış.

```
Tarih
Bilim
Roman
```

Bir kitabı çok hızlı bulabilirsin.

Bu da **iyi veri yapısıdır**.

---

# Veri Yapılarının Temel Amacı

3 şey:

```
1️⃣ Veriyi saklamak
2️⃣ Veriye hızlı erişmek
3️⃣ Veriyi verimli değiştirmek
```

---

# Python'daki Temel Veri Yapıları

Python’da en sık kullanılan veri yapıları:

| Veri Yapısı | Açıklama |
|---|---|
| List | sıralı veri koleksiyonu |
| Tuple | değiştirilemez liste |
| Set | tekrarsız veri |
| Dict | anahtar-değer veri |

---

# 1️⃣ List

List, Python’daki en yaygın veri yapısıdır.

Tanım:

> Sıralı ve değiştirilebilir veri koleksiyonu.

---

## Örnek

```python
numbers = [10, 20, 30, 40]
```

Bellekte:

```
index   value
0       10
1       20
2       30
3       40
```

---

# Index ile Erişim

```python
numbers[0]
```

Sonuç:

```
10
```

---

# Eleman Ekleme

```python
numbers.append(50)
```

Sonuç:

```
[10,20,30,40,50]
```

---

# Eleman Silme

```python
numbers.pop()
```

---

# List Özellikleri

| Özellik | Açıklama |
|---|---|
| sıralıdır | index vardır |
| değiştirilebilir | mutable |
| duplicate olabilir | aynı değer tekrar edebilir |

---

# List Complexity

| İşlem | Complexity |
|---|---|
| index erişim | O(1) |
| append | O(1) amortized |
| arama | O(n) |

---

# Neden Arama O(n)?

Çünkü Python tek tek kontrol eder.

```
10
20
30
40
```

Aranan değer:

```
40
```

Python sırasıyla bakar.

---

# List Internal Yapısı

Python list aslında:

```
dynamic array
```

yani boyutu otomatik büyüyen bir dizidir.

Örneğin:

```
capacity = 4
```

Liste dolunca:

```
capacity = 8
```

yapılır.

Bu yüzden append genelde hızlıdır.

---

# 2️⃣ Tuple

Tuple, listeye benzer ama **immutable'dır**.

Tanım:

> Değiştirilemeyen sıralı veri yapısı.

---

# Örnek

```python
point = (10, 20)
```

---

# Erişim

```python
point[0]
```

Sonuç:

```
10
```

---

# Tuple Değiştirilebilir mi?

Hayır.

```python
point[0] = 30
```

Hata verir.

---

# Tuple Avantajları

- daha hızlı
- daha az bellek kullanır
- immutable olduğu için güvenlidir

---

# Tuple Ne Zaman Kullanılır?

Eğer veri **değişmeyecekse**.

Örnek:

```
koordinatlar
RGB renk
database record
```

---

# Tuple Hashable Olabilir

Bu yüzden dictionary key olabilir.

```python
d = {(1,2): "point"}
```

---

# 3️⃣ Set

Set, tekrarsız veri yapısıdır.

Tanım:

> Unique elemanlardan oluşan koleksiyon.

---

# Örnek

```python
nums = {1,2,3,4}
```

---

# Duplicate Eklenirse

```python
nums = {1,2,2,3}
```

Sonuç:

```
{1,2,3}
```

---

# Set Özellikleri

| Özellik | Açıklama |
|---|---|
| duplicate yok | unique |
| sıralı değil | unordered |
| çok hızlı arama | hash table |

---

# Eleman Ekleme

```python
nums.add(5)
```

---

# Set Complexity

| İşlem | Complexity |
|---|---|
| arama | O(1) |
| ekleme | O(1) |
| silme | O(1) |

---

# Set Neden Bu Kadar Hızlı?

Çünkü **hash table kullanır**.

---

# Hash Table Basit Mantık

Bir değer alınır:

```
"Ali"
```

hash fonksiyonu çalışır:

```
hash("Ali") → 72391
```

Bu sayı bir index'e dönüştürülür.

Bu yüzden arama çok hızlıdır.

---

# 4️⃣ Dictionary

Dictionary Python’un en güçlü veri yapılarından biridir.

Tanım:

> Anahtar-değer (key-value) veri yapısı.

---

# Örnek

```python
user = {
    "name": "Ali",
    "age": 25
}
```

---

# Erişim

```python
user["name"]
```

Sonuç:

```
Ali
```

---

# Yeni Veri Eklemek

```python
user["city"] = "Istanbul"
```

---

# Dictionary Özellikleri

| Özellik | Açıklama |
|---|---|
| key-value | veri eşlemesi |
| hızlı erişim | O(1) |
| hash table kullanır | |

---

# Dictionary Complexity

| İşlem | Complexity |
|---|---|
| erişim | O(1) |
| ekleme | O(1) |
| silme | O(1) |

---

# List vs Set Arama Karşılaştırması

---

## List Arama

```python
if x in my_list:
```

Python tek tek bakar.

```
O(n)
```

---

## Set Arama

```python
if x in my_set:
```

Hash table kullanır.

```
O(1)
```

---

# Büyük Veri Örneği

10 milyon veri düşün.

| Yapı | Arama |
|---|---|
| List | 10 milyon kontrol |
| Set | 1 kontrol |

Bu yüzden veri yapısı seçimi kritiktir.

---

# Python Built-in Dışındaki Veri Yapıları

---

# Stack

Stack:

```
LIFO
Last In First Out
```

Gerçek hayat:

```
tabak yığını
```

Son koyduğun tabak ilk çıkar.

---

# Queue

Queue:

```
FIFO
First In First Out
```

Gerçek hayat:

```
banka kuyruğu
```

---

# Linked List

Linked list:

```
node → node → node
```

Her eleman bir sonrakini gösterir.

Avantaj:

```
ekleme hızlı
```

---

# Tree

Tree hiyerarşik veri yapısıdır.

```
        root
       /    \
     node   node
```

Kullanım:

```
file system
database index
```

---

# Graph

Graph bağlantılı veri yapısıdır.

```
A ---- B
 \    /
   C
```

Kullanım:

```
social network
navigation
```

---

# Heap

Heap özel bir ağaç yapısıdır.

Kullanım:

```
priority queue
task scheduler
```

---

# En Önemli Mülakat Sorusu

Bir developer’dan beklenen şey:

> Doğru veri yapısını seçebilmek.

---

# Örnek

Problem:

```
hızlı arama
```

Çözüm:

```
set
```

---

Problem:

```
key-value mapping
```

Çözüm:

```
dict
```

---

Problem:

```
sıralı veri
```

Çözüm:

```
list
```

---

Problem:

```
unique veri
```

Çözüm:

```
set
```

---

# Tek Cümlelik Mülakat Tanımı

Data structure, verilerin bellekte organize edilmesini ve veriye verimli erişilmesini sağlayan yapılardır.