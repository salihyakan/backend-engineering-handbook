# BÖLÜM 2.10 — Modules & Packages (Python Modül Sistemi)

Python’un modül sistemi, büyük projeleri parçalara bölmek, yeniden kullanılabilir kod yazmak ve maintainable (bakımı kolay) uygulamalar geliştirmek için kritik öneme sahiptir.

Bu konu özellikle Django, Flask, FastAPI gibi framework’lerde ve production sistemlerde çok önemli bir rol oynar.

---

# 1. Module Nedir?

## Tanım

Module, Python’da:

> İçinde Python kodu bulunan bir `.py` dosyasıdır.

---

## Örnek

**math_utils.py**

```python
def add(a, b):
    return a + b

def multiply(a, b):
    return a * b
```

Bu dosya bir modüldür.

---

## Modül Kullanımı

```python
import math_utils

print(math_utils.add(2, 3))
```

Output:

```
5
```

---

## Alternatif Import

```python
from math_utils import add

print(add(5, 10))
```

---

## Neden Modül Kullanılır?

Amaç:

- Kod organizasyonu
- Kod tekrarını önlemek
- Maintainability
- Reusability
- Separation of Concerns

---

## Gerçek Proje Örneği

```
project/
    main.py
    database.py
    auth.py
    utils.py
```

Her dosya bir modüldür.

---

# 2. Package Nedir?

## Tanım

Package, modülleri organize eden klasördür.

```
project/
    utils/
        math.py
        string.py
```

- `utils` → package
- `math.py`, `string.py` → modül

---

## __init__.py Durumu

Eskiden zorunluydu:

```
utils/
    __init__.py
    math.py
```

Python 3.3+ ile opsiyonel hale geldi.

Ancak production’da kullanılması önerilir.

---

## Package Import Örneği

```python
from utils.math import add
```

---

## Nested Package Örneği

```
project/
    app/
        services/
            payment.py
```

Import:

```python
from app.services.payment import process_payment
```

---

# 3. __init__.py Nedir?

## Tanım

`__init__.py`, bir klasörün package olduğunu belirtir.

---

## Görevleri

### 1. Package Initialization

```python
print("Package loaded")
```

---

### 2. Package API Kontrolü

**utils/__init__.py**

```python
from .math import add
from .string import capitalize
```

Artık:

```python
from utils import add
```

çalışır.

---

### 3. Package Export Kontrolü

```python
__all__ = ["add"]
```

Wildcard import’ta hangi isimlerin dışa açılacağını belirler.

---

# 4. Import Sistemi Nasıl Çalışır?

Python bir modülü import ettiğinde şu adımları izler:

---

## Adım 1 — sys.modules Kontrol Edilir

Daha önce yüklenmiş mi?

```python
import sys
print(sys.modules)
```

Eğer varsa tekrar yüklenmez.

---

## Adım 2 — sys.path Kontrol Edilir

Python şu dizinlerde arama yapar:

```python
import sys
print(sys.path)
```

Örnek içerik:

```
[
 '',
 '/usr/lib/python3.11',
 '/project'
]
```

---

## Adım 3 — Module Yüklenir

- Parse edilir
- Bytecode oluşturulur
- Execute edilir

---

## Adım 4 — sys.modules İçine Cache Edilir

Bir modül yalnızca **1 kez load edilir.**

---

# 5. sys.path Nedir?

## Tanım

Python’un module aradığı dizin listesidir.

```python
import sys
print(sys.path)
```

---

## sys.path Arama Sırası

1. Current directory
2. Environment paths
3. Standard library
4. site-packages

---

## site-packages Nedir?

Yüklü paketler burada bulunur:

```bash
pip install django
```

→ site-packages içine kurulur.

---

## sys.path’e Manuel Ekleme

```python
import sys
sys.path.append("/my/custom/path")
```

Production’da önerilmez.

Doğru yöntem:

- PYTHONPATH environment variable kullanmak

---

# 6. Absolute Import vs Relative Import

## Absolute Import (Önerilen)

```python
from app.services.payment import process_payment
```

Avantaj:

- Daha okunabilir
- Daha güvenli
- Refactor sırasında daha stabil

---

## Relative Import

```python
from .payment import process_payment
```

- `.` → current package
- `..` → parent package

Örnek:

```python
from ..database import connect
```

---

## Best Practice

Her zaman **absolute import** kullan.

Django absolute import kullanır.

---

# 7. Import Türleri

### 1. import module

```python
import math
math.sqrt(4)
```

---

### 2. import specific function

```python
from math import sqrt
sqrt(4)
```

---

### 3. import as

```python
import numpy as np
```

---

### 4. import all (ÖNERİLMEZ)

```python
from math import *
```

Sebep:

- Namespace pollution
- Hangi fonksiyon nereden geldi belirsiz

---

# 8. Circular Import Problemi

Mülakatların favori sorusudur.

---

## Problem Nedir?

Module A → Module B’yi import eder  
Module B → Module A’yı import eder

Bu döngü oluşturur.

---

## Örnek

**module_a.py**

```python
from module_b import func_b

def func_a():
    func_b()
```

**module_b.py**

```python
from module_a import func_a

def func_b():
    func_a()
```

---

## Hata

```
ImportError: partially initialized module
```

---

## Sebep

Python modülü import ederken:

- Module başlatılır
- Henüz tamamlanmadan diğer modül onu import etmeye çalışır

---

## Çözümler

### 1. Import’u Function İçine Almak

```python
def func_a():
    from module_b import func_b
    func_b()
```

---

### 2. Ortak Modül Oluşturmak

```
common.py
```

---

### 3. Dependency Refactor Etmek (Best Practice)

Gerçek çözüm:

Architecture düzeltmektir.

---

# 9. Python’da Module Lifecycle

Import sırasında:

### Adım 1
Module bulunur

### Adım 2
Module object oluşturulur

### Adım 3
sys.modules içine eklenir

### Adım 4
Code execute edilir

---

## Önemli Bilgi

Module sadece **1 kez load edilir.**

---

## Örnek

**module.py**

```python
print("loaded")
```

**main.py**

```python
import module
import module
import module
```

Output:

```
loaded
```

Sadece 1 kez çalışır.

---

# 10. __name__ == "__main__"

Mülakatların klasik sorusudur.

---

## Amaç

Bir modülün:

- Direkt çalıştırıldığını mı
- Import edildiğini mi

ayırt etmek.

---

## Örnek

```python
def test():
    print("test")

if __name__ == "__main__":
    test()
```

Terminalden çalıştırırsan:

```
python file.py
```

→ test çalışır.

Başka yerden import edersen:

```python
import file
```

→ test çalışmaz.

---

# 11. Production Best Practices

### 1. Absolute import kullan

✔ DOĞRU

```python
from app.services.payment import process_payment
```

❌ YANLIŞ

```python
from ..payment import process_payment
```

---

### 2. Circular import oluşturma

Doğru architecture kullan.

---

### 3. Her module’un tek sorumluluğu olsun

SRP (Single Responsibility Principle)

---

### 4. Büyük projelerde package kullan

```
project/
    core/
    services/
    models/
    api/
```

---

### 5. Wildcard import kullanma

```python
from module import *
```

---

# 12. Django İçinden Gerçek Örnek

```
myproject/
    users/
        models.py
        views.py
        serializers.py
        services.py
```

Import:

```python
from users.models import User
```

---

# 13. Mülakat Soruları ve Cevapları

**Soru:** Module nedir?  
**Cevap:** Python’da module, Python kodu içeren bir `.py` dosyasıdır.

**Soru:** Package nedir?  
**Cevap:** Package, modülleri organize eden klasördür.

**Soru:** sys.path nedir?  
**Cevap:** Python’un module aradığı dizin listesidir.

**Soru:** Circular import nedir?  
**Cevap:** İki modülün birbirini import etmesidir.

**Soru:** __init__.py ne işe yarar?  
**Cevap:** Package initialization ve API yönetimi sağlar.

---

# 14. En Kritik Özet

Module  
→ Python dosyası

Package  
→ Module klasörü

sys.path  
→ Module arama yolu

sys.modules  
→ Import cache

Import  
→ Module yükler ve execute eder

Circular import  
→ Architecture problemidir