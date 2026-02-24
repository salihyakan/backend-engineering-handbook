# Process, Thread, Concurrency, Parallelism, GIL ve Async (Python & Django)

Bu doküman Python ve Django perspektifinden:

- Process
- Thread
- Concurrency
- Parallelism
- GIL
- threading / multiprocessing
- Async / Event Loop / asyncio
- Django async mimarisi

konularını net ve production odaklı şekilde açıklar.

---

# 1️⃣ Process Nedir?

## 📌 Tanım

**Process**, çalışan bir program örneğidir.

İşletim sistemi açısından:

- Her process’in kendi memory alanı vardır  
- Kendi heap ve stack yapısı vardır  
- Diğer process’lerden izoledir  

---

## 🔎 Örnek

Terminalde:

```bash
python app.py
```

Bu bir process başlatır.

Aynı anda:

```bash
python worker.py
```

Bu ikinci bir process’tir.

👉 İkisi birbirinin memory alanını göremez.

---

## ✅ Özellikler

- İzole memory
- Güçlü izolasyon
- Daha güvenli yapı
- IPC (Inter-Process Communication) gerekir

---

## 🔥 Production Örneği (Django + Gunicorn)

```bash
gunicorn app.wsgi:application --workers 4
```

Burada:

- 4 worker = 4 ayrı process
- Her biri ayrı memory alanında çalışır
- Bir worker çökse diğerleri etkilenmez

---

# 2️⃣ Thread Nedir?

## 📌 Tanım

**Thread**, bir process içindeki execution unit’tir.

Bir process birden fazla thread içerebilir.

⚠️ Kritik nokta:

Thread’ler aynı memory’yi paylaşır.

---

## Yapı

```
Process
├── Thread 1
├── Thread 2
├── Thread 3
```

Hepsi aynı heap’i paylaşır.

---

## ✅ Avantaj

- Hafif yapı
- Memory paylaşımı hızlı
- Context switch maliyeti düşüktür

## ❌ Risk

- Race condition
- Deadlock
- Shared state problemleri

---

## Python Örneği

```python
import threading

def task():
    print("Running")

t = threading.Thread(target=task)
t.start()
```

---

# 3️⃣ Concurrency Nedir?

## 📌 Tanım

**Concurrency**, aynı anda birden fazla işi yönetebilme yeteneğidir.

⚠️ Önemli:

Concurrency ≠ Gerçek paralel çalışma

Concurrency = Zaman dilimlerini paylaşma

---

## Örnek (Tek Çekirdek CPU)

2 task var:

- Task A
- Task B

CPU şunu yapar:

```
A → B → A → B → A → B
```

Hızlı geçiş yapar.

Sana aynı anda çalışıyormuş gibi görünür.

👉 Bu concurrency’dir.

---

## Nerede Etkilidir?

I/O bound sistemlerde çok etkilidir:

- HTTP request bekleme
- DB sorgusu bekleme
- Dosya okuma
- API çağrıları

---

# 4️⃣ Parallelism Nedir?

## 📌 Tanım

**Parallelism**, gerçek anlamda aynı anda çalışmadır.

Bunun için:

- Multi-core CPU gerekir

---

## Örnek (4 Core CPU)

```
Core 1 → Task A
Core 2 → Task B
Core 3 → Task C
Core 4 → Task D
```

Gerçek paralel çalışma gerçekleşir.

---

# 5️⃣ Concurrency vs Parallelism

| Özellik | Concurrency | Parallelism |
|----------|------------|-------------|
| Fiziksel aynı anda çalışma | ❌ | ✅ |
| Tek çekirdekte mümkün | ✅ | ❌ |
| I/O bound için iyi | ✅ | ⚠️ |
| CPU-bound için iyi | ❌ | ✅ |

---

# 6️⃣ GIL (Global Interpreter Lock)

## 📌 Tanım

**GIL**, CPython interpreter’da aynı anda yalnızca **1 thread’in bytecode çalıştırmasına izin veren kilittir**.

---

## ❓ Neden Var?

CPython memory management (reference counting) thread-safe değildir.

GIL:

- Memory corruption’ı önler
- Interpreter’ı güvenli tutar

---

## 🚨 Sonuç

Python’da:

> CPU-bound task’lar thread ile paralel çalışamaz.

---

## Örnek

```python
import threading

def cpu_task():
    while True:
        pass

for _ in range(4):
    t = threading.Thread(target=cpu_task)
    t.start()
```

4 thread başlatsan bile:

👉 Aynı anda yalnızca 1’i bytecode çalıştırır.

---

## GIL Ne Zaman Problem Değildir?

I/O-bound işler:

- DB
- HTTP
- File

Çünkü I/O sırasında GIL release edilir.

---

# 7️⃣ threading Module

Python’un built-in thread desteğidir.

---

## Kullanım

```python
import threading

def task():
    print("Working")

thread = threading.Thread(target=task)
thread.start()
thread.join()
```

---

## Ne Zaman Kullanılır?

- I/O bound işlemler
- Basit background işler
- Network çağrıları

---

## Riskler

- Race condition
- Shared state problemi

---

## Lock Örneği

```python
lock = threading.Lock()

with lock:
    shared_variable += 1
```

---

# 8️⃣ multiprocessing Module

GIL’i bypass etmek için kullanılır.

Her process ayrı memory alanına sahiptir.

Gerçek paralellik sağlar.

---

## Kullanım

```python
from multiprocessing import Process

def task():
    print("Working")

p = Process(target=task)
p.start()
p.join()
```

---

## ✅ Avantaj

- CPU-bound task’lar için ideal
- Gerçek paralellik

## ❌ Dezavantaj

- Memory pahalı
- IPC gerekir
- Process oluşturma maliyetlidir

---

## 🔥 Production Örneği

Gunicorn worker modeli:

- Her worker = ayrı process
- Bağımsız çalışır

---

# 9️⃣ Async Nedir?

## 📌 Tanım

**Async**, non-blocking execution modelidir.

⚠️ Önemli:

- Thread değildir
- Process değildir

Amaç:

> I/O beklerken CPU’yu boş bırakmamak

---

## ❌ Sync Kod

```python
import time

time.sleep(5)
```

Blocking’tir.

---

## ✅ Async Kod

```python
import asyncio

async def task():
    await asyncio.sleep(5)
```

Non-blocking’tir.

---

# 🔟 Event Loop Nedir?

Event loop, async task’ları yöneten merkezdir.

---

## Mantık

1. Task başlatılır
2. I/O bekler
3. Event loop başka task’a geçer
4. I/O hazır olduğunda geri döner

---

## Basit Akış

```
Task A → I/O bekliyor
Task B → çalışıyor
Task C → bekliyor
```

Event loop sürekli kontrol eder.

---

# 1️⃣1️⃣ asyncio Nedir?

Python’un async framework’üdür.

Event loop implementasyonu içerir.

---

## Basit Örnek

```python
import asyncio

async def say_hello():
    print("Hello")
    await asyncio.sleep(2)
    print("World")

asyncio.run(say_hello())
```

---

## Birden Fazla Task

```python
async def task1():
    await asyncio.sleep(2)
    print("Task1")

async def task2():
    await asyncio.sleep(1)
    print("Task2")

async def main():
    await asyncio.gather(task1(), task2())

asyncio.run(main())
```

Çıktı:

```
Task2
Task1
```

---

# Django ve Async

Django 3.1+ ile ASGI destekler.

---

## Sync View

```python
def view(request):
    pass
```

## Async View

```python
async def view(request):
    pass
```

---

# ASGI vs WSGI

| Özellik | WSGI | ASGI |
|----------|------|------|
| Sync destek | ✅ | ✅ |
| Async destek | ❌ | ✅ |
| WebSocket | ❌ | ✅ |

---

# 📌 Hangi Durumda Ne Kullanılır?

## CPU-Bound İşler

- Image processing
- ML
- Büyük hesaplama

👉 multiprocessing

---

## I/O-Bound İşler

- HTTP çağrıları
- DB işlemleri
- API
- Dosya okuma

👉 threading veya async

---

# 🔥 Production Mimari Örneği

```
Web Request → Django
CPU ağır iş → Celery worker (multiprocessing)
I/O yoğun iş → async HTTP client
Cache → Redis
```

---

# 🎯 Senior Seviyede Bilmen Gereken Kritik Gerçekler

- Django default olarak sync çalışır.
- Async view her zaman performans artırmaz.
- CPU-bound iş Django view içinde yapılmaz.
- Worker sayısı CPU core sayısına göre ayarlanır.
- GIL yüzünden Python thread ile paralel CPU işi yapılamaz.
- Async = daha fazla request handle etmek için kullanılır.

---

# 🎤 Mülakatlarda Çok Sorulan Sorular

- GIL nedir?
- Thread ile process farkı nedir?
- Concurrency vs parallelism farkı?
- Async nasıl çalışır?
- Event loop nedir?
- Django async nasıl çalışır?
- GIL varken neden multiprocessing kullanırız?

---

Bu doküman Python ve Django production ortamı perspektifiyle hazırlanmıştır.