# 2.9 Error Handling (Exception Management)

Backend geliştirmede hata yönetimi (error handling) kritik bir konudur.  
Production ortamında uygulamayı ayakta tutan şey çoğu zaman doğru exception yönetimidir.

Yanlış exception yönetimi:

- Uygulamanın çökmesine
- Hataların gizlenmesine
- Debug sürecinin zorlaşmasına
- Güvenlik açıklarına

neden olabilir.

---

## Bu Bölümde Öğreneceklerin

- Exception nedir?
- try / except yapısı
- finally ve else bloğu
- raise kullanımı
- Custom exception oluşturma
- Exception hierarchy
- Exception propagation
- Production best practices (mülakat seviyesi)

---

# 1. Exception Nedir?

Exception:

Program çalışırken oluşan hatadır.

Örnek:

```python
10 / 0
```

Output:

```
ZeroDivisionError
```

Python bu durumda programı durdurur.

Exception’lar kontrol edilmezse uygulama çöker.

---

# 2. try / except

Amaç:

Hata oluşursa programı durdurmak yerine kontrol etmektir.

---

## Temel Syntax

```python
try:
    riskli_kod
except HataTipi:
    hata_yonetimi
```

---

## Örnek

```python
try:
    x = 10 / 0
except ZeroDivisionError:
    print("Sıfıra bölme hatası")
```

Output:

```
Sıfıra bölme hatası
```

Program çökmez.

---

# 3. Birden Fazla Exception Yakalama

```python
try:
    x = int("abc")
except ValueError:
    print("Geçersiz sayı")
except TypeError:
    print("Tip hatası")
```

Spesifik exception yakalamak her zaman daha iyidir.

---

# 4. Genel Exception Yakalama

```python
try:
    ...
except Exception as e:
    print("Hata:", e)
```

⚠️ Production ortamında genelde `Exception` yakalanır ama mutlaka loglanır.

---

# 5. except Exception as e

```python
try:
    1 / 0
except Exception as e:
    print(type(e))
    print(e)
```

Output:

```
<class 'ZeroDivisionError'>
division by zero
```

Burada:

- e → exception objesidir
- type(e) → hata tipi
- e → hata mesajı

---

# 6. finally

finally:

Hata olsun ya da olmasın her zaman çalışır.

---

## Örnek

```python
try:
    f = open("test.txt")
except FileNotFoundError:
    print("Dosya yok")
finally:
    print("Temizlik yapıldı")
```

finally her durumda çalışır.

---

## Gerçek Kullanım

- Dosya kapatma
- Database bağlantı kapatma
- Network bağlantı kapatma
- Resource temizleme

---

# 7. else Bloğu

else:

Hata oluşmazsa çalışır.

```python
try:
    x = 10 / 2
except ZeroDivisionError:
    print("Hata")
else:
    print("Başarılı")
```

Output:

```
Başarılı
```

Kural:

- try → riskli kod
- except → hata varsa
- else → hata yoksa
- finally → her durumda

---

# 8. raise Kullanımı

Kendi exception’ını fırlatmak için kullanılır.

---

## Örnek

```python
def bol(a, b):
    if b == 0:
        raise ValueError("b sıfır olamaz")
    return a / b
```

---

## raise ile tekrar fırlatma

```python
try:
    ...
except Exception:
    raise
```

Hata yukarıya iletilir (propagate edilir).

---

# 9. Custom Exception

Kendi hata tipini oluşturabilirsin.

---

## Basit Örnek

```python
class YasHatasi(Exception):
    pass
```

Kullanım:

```python
def kontrol(yas):
    if yas < 18:
        raise YasHatasi("18 yaşından küçük")

kontrol(15)
```

---

## Daha Gelişmiş Örnek

```python
class APIError(Exception):

    def __init__(self, message, status_code):
        self.message = message
        self.status_code = status_code
        super().__init__(message)
```

Bu yapı genelde REST API’lerde kullanılır.

---

# 10. Exception Hierarchy

Python’daki tüm exception’lar şu hiyerarşidedir:

```
BaseException
    ├── SystemExit
    ├── KeyboardInterrupt
    └── Exception
         ├── ValueError
         ├── TypeError
         ├── ZeroDivisionError
         ├── FileNotFoundError
         └── ...
```

---

## Neden Önemli?

```python
except Exception:
```

- SystemExit yakalamaz
- KeyboardInterrupt yakalamaz

Ama:

```python
except BaseException:
```

Her şeyi yakalar (önerilmez).

---

# 11. Exception Propagation

Exception yakalanmazsa:

- Fonksiyon yukarı çıkar
- Call stack boyunca ilerler
- En üst seviyeye kadar gider
- Program çöker

---

## Örnek

```python
def a():
    b()

def b():
    c()

def c():
    1 / 0

a()
```

Exception yukarı doğru çıkar.

---

# 12. Multiple Exception Handling

```python
try:
    ...
except (ValueError, TypeError):
    print("Hata")
```

Tuple ile birden fazla exception yakalanabilir.

---

# 13. try İçinde return Varsa finally Çalışır mı?

Evet.

```python
def test():
    try:
        return 1
    finally:
        print("Finally çalıştı")

print(test())
```

Output:

```
Finally çalıştı
1
```

finally her zaman çalışır.

---

# 14. finally return Override Edebilir mi?

Evet.

```python
def test():
    try:
        return 1
    finally:
        return 2

print(test())
```

Output:

```
2
```

Bu tehlikelidir.

finally içindeki return, try’daki return’ü override eder.

---

# 15. Production Best Practices

❌ Bare except kullanma:

```python
except:
```

✔ Spesifik exception yakala:

```python
except ValueError:
```

✔ Exception logla:

```python
import logging
logging.error("Hata oluştu", exc_info=True)
```

✔ Sessizce hata yutma

❌

```python
except Exception:
    pass
```

Bu production’da büyük problemdir.

---

# 16. Django Context

Django’da sık kullanılan exception’lar:

- ValidationError
- Http404
- PermissionDenied

Django REST Framework’te:

- APIException
- AuthenticationFailed

Bunlar custom exception örnekleridir.

---

# 17. Mülakat Soruları

Soru: try / except nasıl çalışır?  
Cevap: Riskli kod try içine yazılır, hata oluşursa except bloğu çalışır.

Soru: finally ne zaman çalışır?  
Cevap: Her durumda çalışır (hata olsa da olmasa da).

Soru: Custom exception nasıl yazılır?  
Cevap: Exception class’ından miras alınarak.

Soru: Exception hierarchy neden önemli?  
Cevap: Genel ve spesifik hataları doğru seviyede yakalamak için.

Soru: raise ne işe yarar?  
Cevap: Exception fırlatır veya yeniden fırlatır.

---

# 18. Senior Level Kritik Bilgi

Backend’de:

- Hata saklanmaz
- Loglanır
- Gerekirse tekrar fırlatılır
- API response’a dönüştürülür
- Monitoring sistemine gönderilir (Sentry vb.)

Amaç:

Uygulamanın çökmesini engellemek  
ve hatayı görünür hale getirmek.

---

# 19. En Kritik Özet

- try → riskli kod
- except → hata yakala
- else → hata yoksa çalış
- finally → her durumda çalış
- raise → hata fırlat
- custom exception → kendi hata tipin
- exception hierarchy → hata ağacı

Backend geliştiricisi için doğru exception yönetimi, kod yazma bilgisinden daha kritik olabilir.