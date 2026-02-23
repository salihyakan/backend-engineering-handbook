# 2.4 Python Collections Internal (Arka Plan Yapısı)

Bu bölüm Python’daki en kritik veri yapılarının **arka planda nasıl çalıştığını** açıklar.  
Özellikle teknik mülakatlarda çok sorulur çünkü:

- Performans analizi bununla ilgilidir  
- Big-O complexity bununla ilgilidir  
- Python’un neden hızlı olduğu bununla ilgilidir  

Bu bölümde şunları öğreneceksin:

- list internal yapısı  
- dict internal yapısı (hash table)  
- set internal yapısı  
- tuple internal yapısı  

---

# 1️⃣ Python `list` Internal Yapısı

Python `list` aslında:

> **Dynamic Array (Dinamik Dizi)**’dir.

C dilindeki array’e benzer ama **dinamik büyür**.

## 1.1 Memory Yapısı

```python
lst = [10, 20, 30]
```

Memory'de:

```
lst
 ↓
[ ptr ][ ptr ][ ptr ]
   ↓     ↓     ↓
  10    20    30
```

⚠️ List, değerleri direkt tutmaz.  
**Referansları (pointer)** tutar.

---

## 1.2 Dynamic Resizing Mantığı

List kapasitesi dolunca:

- Daha büyük bir array oluşturulur  
- Yeni memory allocate edilir  
- Eski elemanlar kopyalanır  

Örnek senaryo:

```
capacity = 4
size = 4
```

Yeni `append` yapılır → capacity genelde 2x büyür (örnek: 8)

---

## 1.3 `append()` Neden O(1)?

Çünkü:

- Çoğu zaman sadece sona ekler  
- Resize nadiren olur  

Bu yüzden:

> **Amortized O(1)**

---

## 1.4 `insert()` Neden O(n)?

```python
lst.insert(0, 100)
```

Çünkü tüm elemanlar kaydırılır:

```
[10, 20, 30]
→
[100, 10, 20, 30]
```

Bu işlem **O(n)**’dir.

---

## 1.5 list Operation Complexity

| Operation | Complexity |
|------------|------------|
| append     | O(1) |
| access     | O(1) |
| set        | O(1) |
| insert     | O(n) |
| delete     | O(n) |
| search     | O(n) |

---

## 1.6 Neden list Hızlı?

Çünkü:

- Contiguous memory kullanır  
- CPU cache friendly’dir  

```
[ ][ ][ ][ ]
```

---

# 2️⃣ Python `dict` Internal Yapısı (Hash Table)

`dict`, Python’un en güçlü veri yapısıdır.

> Hash Table kullanır.

## 2.1 dict Nasıl Çalışır?

```python
d = {
    "name": "Salih",
    "age": 25
}
```

Python şunu yapar:

```
hash("name") → index → value
hash("age")  → index → value
```

---

## 2.2 Hash Fonksiyonu

Hash:

> value → integer

```python
hash("name")
```

Örnek çıktı:

```
-23423423423
```

---

## 2.3 Internal Memory Yapısı

```
index    key      value
------------------------
0        None     None
1        age      25
2        None     None
3        name     Salih
```

---

## 2.4 Access Neden O(1)?

Çünkü:

```
hash(key) → direkt index
```

Loop yoktur.

---

## 2.5 Collision Problemi

İki key aynı hash index’e düşerse:

```
hash("a") → index 3
hash("b") → index 3
```

Collision oluşur.

Python çözümü:

> **Open Addressing**

Yeni boş slot aranır.

---

## 2.6 dict Complexity

| Operation | Complexity |
|------------|------------|
| access     | O(1) |
| insert     | O(1) |
| delete     | O(1) |
| search     | O(1) |

Worst case:

> O(n) (nadirdir)

---

## 2.7 dict Neden Çok Hızlı?

Çünkü:

- Hash table kullanır  
- Loop gerekmez  

---

# 3️⃣ Python `set` Internal Yapısı

`set` de hash table kullanır.

Mantık `dict` ile aynıdır.

Fark:

- `dict` → key → value  
- `set` → sadece key  

## Örnek

```python
s = {10, 20, 30}
```

Memory:

```
index   value
------------
0       None
1       20
2       None
3       10
4       30
```

## Complexity

| Operation | Complexity |
|------------|------------|
| add        | O(1) |
| remove     | O(1) |
| search     | O(1) |

## Neden set Hızlı?

```python
if 10 in s:
```

O(1)

Ama list:

```python
if 10 in lst:
```

O(n)

---

# 4️⃣ Python `tuple` Internal Yapısı

`tuple`:

> Immutable list gibidir.

## Memory Yapısı

```
tuple
 ↓
[ ptr ][ ptr ][ ptr ]
```

Ama immutable’dır.

## Avantajları

- Daha hızlı  
- Daha az memory  
- Thread-safe  

## tuple Neden Daha Hızlı?

Çünkü:

- Mutable değildir  
- Resize yoktur  
- Memory optimize edilmiştir  

## Complexity

| Operation | Complexity |
|------------|------------|
| access     | O(1) |
| search     | O(n) |

---

# 5️⃣ list vs tuple (Mülakat Sorusu)

| Feature | list | tuple |
|----------|-------|--------|
| mutable | yes | no |
| speed | slower | faster |
| memory | more | less |
| resize | yes | no |

---

# 6️⃣ dict vs list Performans Farkı

```python
lst = [1,2,3,4,5]
```

Search:

> O(n)

Ama:

```python
d = {1: True, 2: True, 3: True}
```

Search:

> O(1)

---

# 7️⃣ Gerçek Hayat Analojisi

**list** → Otel koridoru (oda1 oda2 oda3 oda4)

**dict** → İsim → oda numarası sistemi

**set** → Unique kimlik listesi

**tuple** → Sabit kayıt

---

# 8️⃣ dict ve set Neden Unordered Görünür?

Çünkü hash table kullanırlar.

⚠️ Python 3.7+ ile insertion order korunur.

---

# 9️⃣ dict Neden Sadece Immutable Key Kabul Eder?

Çünkü:

> Hash değeri değişmemelidir.

Bu yasak:

```python
d[[1,2]] = "value"
```

Ama bu olur:

```python
d[(1,2)] = "value"
```

---

# 🔟 Senior Level Kritik Bilgi

- dict ve set → hash table kullanır  
- list → dynamic array kullanır  
- tuple → immutable array kullanır  

---

# 1️⃣1️⃣ Mülakat Soruları

**Soru:** dict neden hızlıdır?  
**Cevap:** Hash table kullandığı için O(1) access sağlar.

**Soru:** list neden insert O(n)?  
**Cevap:** Elemanlar kaydırılır.

**Soru:** set neden hızlıdır?  
**Cevap:** Hash table kullanır.

**Soru:** tuple neden hızlıdır?  
**Cevap:** Immutable olduğu için optimize edilmiştir.

**Soru:** dict key neden immutable olmalı?  
**Cevap:** Hash değişmemelidir.

---

# 1️⃣2️⃣ Complexity Özeti

| Structure | Access | Insert | Search |
|------------|---------|---------|---------|
| list | O(1) | O(1)* | O(n) |
| dict | O(1) | O(1) | O(1) |
| set | O(1) | O(1) | O(1) |
| tuple | O(1) | ❌ | O(n) |

\* amortized

---

# 🔥 En Kritik Özet

```
list  → dynamic array
dict  → hash table
set   → hash table
tuple → immutable array
```