# BÖLÜM 3.4 — Instance Attribute vs Class Attribute

## Tanım

Python’da attribute’ler ikiye ayrılır:

- **Instance Attribute**
- **Class Attribute**

Bu ayrım Python OOP’un en kritik konularından biridir ve production’da ciddi bug’lara sebep olabilir.

---

# 1️⃣ Instance Attribute

## Tanım

Her object’e özel olan attribute’tür.

- Her instance kendi kopyasına sahiptir.
- Genellikle `__init__` içinde tanımlanır.
- Object’in state’ini temsil eder.

---

## Örnek

```python
class User:

    def __init__(self, name):
        self.name = name
```

Kullanım:

```python
u1 = User("Salih")
u2 = User("Ahmet")

print(u1.name)
print(u2.name)
```

Output:

```
Salih
Ahmet
```

---

## Memory Modeli

```
u1 → name: Salih
u2 → name: Ahmet
```

Her object kendi verisini tutar.

---

## Ne Zaman Kullanılır?

- Object’e özel veri
- Mutable veri (list, dict, set)
- Kullanıcıya özel bilgiler
- Runtime’da değişecek state

---

# 2️⃣ Class Attribute

## Tanım

Class attribute, tüm object’ler tarafından paylaşılan attribute’tür.

- Class seviyesinde tanımlanır.
- Tek bir kopyası vardır.
- Tüm instance’lar referans eder.

---

## Örnek

```python
class User:

    platform = "Instagram"
```

Kullanım:

```python
u1 = User()
u2 = User()

print(u1.platform)
print(u2.platform)
```

Output:

```
Instagram
Instagram
```

---

## Memory Modeli

```
User class
   |
   └── platform: Instagram

u1 → reference → User.platform
u2 → reference → User.platform
```

Tek bir kopya vardır.

---

# 3️⃣ Instance vs Class Attribute Farkı (Kritik Örnek)

```python
class User:

    platform = "Instagram"

    def __init__(self, name):
        self.name = name
```

```python
u1 = User("Salih")
u2 = User("Ahmet")

u1.platform = "Twitter"

print(u1.platform)
print(u2.platform)
```

Output:

```
Twitter
Instagram
```

---

## Neden Böyle Oldu?

Şu satır:

```python
u1.platform = "Twitter"
```

Class attribute’ü değiştirmez.

Bunun yerine u1 üzerinde **yeni bir instance attribute oluşturur**.

---

## Memory Modeli

```
User class
   └── platform: Instagram

u1
   ├── name: Salih
   └── platform: Twitter   ← instance attribute

u2
   └── name: Ahmet
```

u1 artık kendi `platform` attribute’una sahiptir.

---

# 4️⃣ Attribute Lookup Sırası (ÇOK KRİTİK)

Python bir attribute ararken şu sırayı izler:

1. Instance attribute
2. Class attribute
3. Parent class attribute (MRO sırasına göre)

---

## Örnek

```python
class User:
    role = "user"

u = User()
print(u.role)
```

Instance’ta yok → class’ta arar → bulur.

---

## Teknik Olarak

Python şu işlemi yapar:

```python
u.__dict__          # instance attribute'ler
User.__dict__       # class attribute'ler
```

Lookup sırası:

```
instance → class → parent classes → object
```

---

# 5️⃣ Class Attribute Ne Zaman Kullanılır?

### Kullanım Alanları

- Sabit değerler (constants)
- Config değerleri
- Shared state
- Enum benzeri yapılar

---

## Production Örnek

```python
class Database:
    connection = None
```

Tüm instance’lar aynı connection’ı paylaşabilir.

---

# 6️⃣ Django Gerçek Örnek

```python
class User(models.Model):

    ROLE_CHOICES = [
        ("admin", "Admin"),
        ("user", "User")
    ]
```

`ROLE_CHOICES` bir **class attribute**’dür.

Sebep:

- Sabit
- Paylaşılan
- Immutable

---

# 7️⃣ ⚠ Production’da En Büyük Hata

## Yanlış Kullanım

```python
class User:
    permissions = []
```

```python
u1 = User()
u2 = User()

u1.permissions.append("admin")

print(u2.permissions)
```

Output:

```
['admin']
```

---

## Neden?

`permissions` class attribute’tür.  
Tüm instance’lar aynı listeyi paylaşır.

Bu production’da ciddi bug üretir.

---

## Doğru Kullanım

```python
class User:

    def __init__(self):
        self.permissions = []
```

Artık her instance kendi listesini tutar.

---

# 8️⃣ Mülakat Sorusu

### Soru:
Instance attribute ve class attribute farkı nedir?

### Cevap:
Instance attribute her object’e özeldir.  
Class attribute tüm object’ler tarafından paylaşılır.

---

# 9️⃣ Class Attribute Ne Zaman Kullanılır?

Kullan:

- Constants
- Config
- Shared state
- Immutable değerler (str, int, tuple)

Kullanma:

- Mutable data (list, dict, set)
- Kullanıcıya özel veri

---

# 🔟 Best Practice

✅ Immutable ise → class attribute kullan  
✅ Mutable ise → instance attribute kullan  

---

# 1️⃣1️⃣ Senior Level Kritik Bilgi

Class attribute mutable ise:

- Thread safety problemi oluşabilir
- Beklenmeyen side effect oluşur
- Debug etmesi zordur

Bu yüzden:

> Production’da mutable class attribute kullanımı neredeyse her zaman hatadır.

---

# 📌 Özet

| Özellik | Instance Attribute | Class Attribute |
|----------|-------------------|-----------------|
| Kopya sayısı | Her object’te ayrı | Tek kopya |
| Tanımlandığı yer | `__init__` içinde | Class seviyesinde |
| Paylaşım | Paylaşılmaz | Tüm instance’lar paylaşır |
| Mutable kullanım | Güvenli | Tehlikeli |
| Lookup sırası | 1. sırada | 2. sırada |

---

## Final Kural

Instance → object’e özel  
Class → paylaşılan  

Production’da en çok bug çıkaran OOP konularından biridir.