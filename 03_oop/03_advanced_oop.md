# BÖLÜM 3.3 — Python OOP Advanced

Bu bölümde Python’un OOP tarafındaki ileri seviye (advanced) konularını inceleyeceğiz:

- Dunder (Double Under) Methods nedir?
- `__str__`
- `__repr__`
- `__eq__`
- `__hash__`
- `__eq__` ve `__hash__` ilişkisi (kritik production bilgisi)

Bu konular özellikle backend geliştirmede, Django ORM, caching, dict/set kullanımı ve production sistemlerde çok önemlidir.

---

# 1. Dunder Methods (Double Under Methods)

## Tanım

Dunder methods (double underscore methods), Python’un object’lerle nasıl etkileşime gireceğini belirleyen özel method’lardır.

Dunder = **Double underscore**

Format:

```python
__method_name__
```

Örnekler:

- `__init__`
- `__str__`
- `__repr__`
- `__eq__`
- `__hash__`
- `__len__`

## Amaç

Python’un built-in fonksiyonlarının senin object’inle çalışmasını sağlamak.

Örneğin:

```python
print(obj)
```

Python aslında şunu çağırır:

```python
obj.__str__()
```

---

## Basit Örnek

```python
class User:
    pass

user = User()

print(user)
```

Output:

```
<__main__.User object at 0x000001>
```

Bu teknik ve okunması zor bir çıktıdır.

Bunu iyileştirmek için `__str__` ve `__repr__` kullanırız.

---

# 2. `__str__` Method

## Tanım

`__str__`, object’in **kullanıcı dostu (human-readable)** string temsilini döndürür.

`print()` tarafından kullanılır.

## Örnek

```python
class User:

    def __init__(self, name):
        self.name = name

    def __str__(self):
        return f"User name is {self.name}"
```

Kullanım:

```python
user = User("Salih")
print(user)
```

Output:

```
User name is Salih
```

## Python Internal Çalışma

```python
print(user)
```

Python şunu yapar:

```python
print(user.__str__())
```

## Amaç

- Kullanıcı dostu çıktı üretmek
- Loglama ve UI tarafında okunabilirlik sağlamak

---

# 3. `__repr__` Method

## Tanım

`__repr__`, object’in **developer dostu, teknik temsilini** döndürür.

Debugging için kullanılır.

## Örnek

```python
class User:

    def __init__(self, name):
        self.name = name

    def __repr__(self):
        return f"User(name='{self.name}')"
```

Kullanım:

```python
user = User("Salih")
print(repr(user))
```

Output:

```
User(name='Salih')
```

---

## `__str__` vs `__repr__`

| Method  | Amaç                  |
|----------|-----------------------|
| `__str__`  | Kullanıcı dostu çıktı |
| `__repr__` | Developer dostu çıktı |

## Python Fallback Mekanizması

- Eğer `__str__` varsa → `print()` onu kullanır.
- Eğer `__str__` yoksa → `__repr__` kullanılır.

## Best Practice

Production’da genellikle:

- En azından `__repr__` implement edilir.
- `__repr__` mümkünse object’i yeniden oluşturabilecek bir formatta yazılır.

---

# 4. `__eq__` Method

## Tanım

`__eq__`, object’lerin eşitlik karşılaştırmasını kontrol eder.

`==` operatörü tarafından çağrılır.

## Default Davranış

```python
class User:

    def __init__(self, name):
        self.name = name


u1 = User("Salih")
u2 = User("Salih")

print(u1 == u2)
```

Output:

```
False
```

Çünkü default olarak Python:

- Memory adreslerini karşılaştırır.

---

## `__eq__` ile Çözüm

```python
class User:

    def __init__(self, name):
        self.name = name

    def __eq__(self, other):
        if not isinstance(other, User):
            return NotImplemented
        return self.name == other.name
```

Kullanım:

```python
u1 = User("Salih")
u2 = User("Salih")

print(u1 == u2)
```

Output:

```
True
```

## Python Internal Çalışma

```python
u1 == u2
```

Python bunu çağırır:

```python
u1.__eq__(u2)
```

---

# 5. `__hash__` Method

Bu konu mülakatlarda en kritik konulardan biridir.

## Tanım

`__hash__`, object’in hash değerini üretir.

Bu değer:

- `set`
- `dict`

içinde kullanılabilmesini sağlar.

## Hash Nedir?

Hash = integer bir değer

Örnek:

```python
print(hash("hello"))
```

## Neden Gerekli?

`dict` ve `set` hash tabanlı veri yapılarıdır.

```python
my_dict = {}
my_dict["key"] = "value"
```

Python şunu yapar:

```python
hash("key")
```

---

## Önemli Nokta (Python 3 Davranışı)

Eğer bir class içinde `__eq__` override edilirse ve `__hash__` tanımlanmazsa:

- Python otomatik olarak `__hash__ = None` yapar.
- Object hash edilemez hale gelir.
- `set` ve `dict` içinde kullanılamaz.

---

## Doğru Kullanım

```python
class User:

    def __init__(self, name):
        self.name = name

    def __eq__(self, other):
        if not isinstance(other, User):
            return NotImplemented
        return self.name == other.name

    def __hash__(self):
        return hash(self.name)
```

Kullanım:

```python
u1 = User("Salih")
u2 = User("Salih")

my_set = {u1, u2}

print(len(my_set))
```

Output:

```
1
```

Çünkü artık:

- `u1 == u2` → True
- `hash(u1) == hash(u2)` → True

---

# 6. `__eq__` ve `__hash__` İlişkisi (KRİTİK)

Temel kural:

```
if a == b:
    hash(a) == hash(b)
```

olmalıdır.

Aksi halde:

- dict/set bozuk çalışır
- lookup hataları oluşur
- production bug’ları çıkar

Bu yüzden:

- `__eq__` yazıyorsan
- `__hash__`’i de düşünmelisin

---

# 7. Python dict Internal Çalışma Modeli

```python
my_dict[user] = "value"
```

Python şu adımları izler:

1. `hash(user)` çağrılır
2. Hash’e göre bucket bulunur
3. Aynı bucket içindeki object’ler `__eq__` ile kontrol edilir
4. Doğru key eşleşirse değer yazılır

Bu yüzden hem `hash` hem `eq` kritiktir.

---

# 8. Tüm Dunder Method Örneği

```python
class User:

    def __init__(self, name):
        self.name = name

    def __str__(self):
        return f"User: {self.name}"

    def __repr__(self):
        return f"User(name='{self.name}')"

    def __eq__(self, other):
        if not isinstance(other, User):
            return NotImplemented
        return self.name == other.name

    def __hash__(self):
        return hash(self.name)
```

---

# 9. Internal Çalışma Özeti

| Kod                  | Çağrılan Method |
|----------------------|-----------------|
| `print(user)`        | `__str__`       |
| `repr(user)`         | `__repr__`      |
| `user1 == user2`     | `__eq__`        |
| `hash(user)`         | `__hash__`      |

---

# 10. Backend Production Örnekleri

## Cache Sistemi

```python
cache[user] = data
```

## Set Kullanımı

```python
visited_users.add(user)
```

## Permission Mapping

```python
permissions[user] = role
```

Bunların tamamı:

- `hash`
- `eq`

üzerinden çalışır.

---

# 11. Mülakat Soruları

### Soru: `__str__` ve `__repr__` farkı nedir?

Cevap:  
`__str__` kullanıcı dostu çıktı üretir.  
`__repr__` developer dostu teknik temsil üretir.

---

### Soru: `==` nasıl çalışır?

Cevap:  
`__eq__` method’unu çağırır.

---

### Soru: dict neden hash kullanır?

Cevap:  
O(1) lookup performansı için hash tabanlı yapı kullanır.

---

### Soru: `__hash__` neden gereklidir?

Cevap:  
Object’in dict ve set içinde kullanılabilmesi için gereklidir.

---

# 12. En Kritik Production Bilgisi

Python’da:

- `dict`
- `set`

tamamen hash tabanlıdır.

Bu yüzden:

- `__eq__`
- `__hash__`

backend geliştirmede son derece kritiktir.

Yanlış implement edilen bir `__eq__` / `__hash__`:

- Cache bug’ı
- Permission bug’ı
- Data duplication problemi
- Memory leak benzeri davranışlar

üretebilir.

Bu konu, özellikle Django Model, caching sistemleri ve büyük ölçekli backend mimarilerinde mutlaka doğru anlaşılmalıdır.