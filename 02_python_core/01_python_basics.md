# 2.1 Python Temelleri – Interpreter, Bytecode ve Execution Model

Python’u gerçekten anlamak için şu sorunun cevabını bilmek gerekir:

> Yazdığımız Python kodu bilgisayar tarafından nasıl çalıştırılıyor?

Bu süreç birkaç teknik aşamadan oluşur. Bu bölümü anlamak; performance, memory management, concurrency ve production debugging açısından kritiktir.

---

## 1️⃣ Python Interpreter Nasıl Çalışır?

Python interpreter, yazdığın Python kodunu çalıştıran programdır.

CPU yalnızca **makine dili (binary)** anlar. Python kodu doğrudan CPU tarafından çalıştırılamaz.

Python’un çalışma akışı şu şekildedir:

```
Python Code (.py)
        ↓
Compile to Bytecode
        ↓
Python Virtual Machine (PVM)
        ↓
CPU Execution
```

### Önemli Nokta

Python:

- Doğrudan makine koduna çevrilmez
- Önce **bytecode**’a çevrilir
- Bytecode, **Python Virtual Machine (PVM)** tarafından çalıştırılır

---

### Örnek

Python dosyası:

```python
print("Hello World")
```

Interpreter’ın yaptığı işlemler:

1. Source code’u alır  
2. Bytecode’a çevirir  
3. Python Virtual Machine çalıştırır  
4. Output üretir  

---

### Python Compiled mı Interpreted mı?

Python teknik olarak **hem compiled hem interpreted** bir dildir.

- Compiled → Bytecode’a çevrilir  
- Interpreted → Bytecode interpreter tarafından çalıştırılır  

Bu yüzden “sadece interpreted” demek eksik bir tanımdır.

---

## 2️⃣ CPython Nedir?

CPython, Python’un en yaygın kullanılan resmi implementasyonudur.

Python aslında bir dil spesifikasyonudur. Farklı implementasyonları vardır:

| Implementation | Açıklama |
|---------------|----------|
| CPython | En yaygın, C dili ile yazılmıştır |
| PyPy | JIT compiler kullanır, genelde daha hızlıdır |
| Jython | Java Virtual Machine üzerinde çalışır |
| IronPython | .NET üzerinde çalışır |

---

### CPython Neden Önemli?

- GIL (Global Interpreter Lock) CPython’a özeldir  
- Memory management CPython’a özeldir  
- Production sistemlerin büyük çoğunluğu CPython kullanır  

---

### CPython Ne Yapar?

- Python kodunu bytecode’a çevirir  
- Python Virtual Machine’i çalıştırır  
- Memory yönetimini yapar  
- Garbage Collection gerçekleştirir  

---

## 3️⃣ Bytecode Nedir?

Bytecode, Python kodunun düşük seviyeli ara temsilidir.

- Makine kodu değildir  
- CPU doğrudan anlayamaz  
- Python Virtual Machine tarafından çalıştırılır  

---

### Çalışma Süreci

```
Python code → Bytecode → Python Virtual Machine → CPU
```

---

### Bytecode Nerede Saklanır?

`__pycache__` klasöründe:

```
__pycache__/
    file.cpython-311.pyc
```

`.pyc` dosyası bytecode içerir.

---

### Bytecode Avantajları

- Tekrar compile edilmez  
- Daha hızlı başlangıç sağlar  
- Performans iyileştirmesi sağlar  

---

### Bytecode Görmek

```python
import dis

def add(a, b):
    return a + b

dis.dis(add)
```

Örnek çıktı:

```
LOAD_FAST
LOAD_FAST
BINARY_ADD
RETURN_VALUE
```

Bunlar bytecode instruction’lardır.

---

## 4️⃣ Python Execution Model

Python’un kodu çalıştırma sürecidir.

### Adım 1: Source Code

```python
x = 10
print(x)
```

---

### Adım 2: Compile

Kod bytecode’a çevrilir:

```
LOAD_CONST
STORE_NAME
LOAD_NAME
CALL_FUNCTION
```

---

### Adım 3: Python Virtual Machine (PVM)

- Bytecode’u okur  
- Instruction’ları tek tek işler  
- Stack tabanlı çalışır  

---

### Adım 4: Execution

CPU, interpreter üzerinden komutları çalıştırır.

---

### Özet Akış

```
.py file
   ↓
Compiler
   ↓
Bytecode (.pyc)
   ↓
Python Virtual Machine
   ↓
Execution
```

---

## 5️⃣ Python Virtual Machine (PVM) Nedir?

Python’un çalıştırma motorudur.

Görevleri:

- Bytecode okumak  
- Execute etmek  
- Stack yönetmek  
- Memory yönetmek  

PVM:

- Gerçek CPU değildir  
- Sanal bir makinedir  
- Stack tabanlıdır  

---

### Stack Tabanlı Çalışma Örneği

```python
a = 2
b = 3
c = a + b
```

Stack işlemleri:

```
push 2
push 3
add
pop result
```

---

## 6️⃣ Python REPL Nedir?

REPL = **Read Evaluate Print Loop**

Python’un interaktif çalışma ortamıdır.

Terminalde çalıştırmak için:

```bash
python
```

Karşına gelir:

```
>>>
```

---

### REPL Ne Yapar?

- Read → kodu okur  
- Evaluate → çalıştırır  
- Print → sonucu gösterir  
- Loop → tekrar bekler  

---

### Örnek

```
>>> 2 + 2
4
```

---

### REPL Kullanım Alanı

- Hızlı test  
- Debug  
- Öğrenme  
- Deneme  

---

## 7️⃣ Python Interpreter Gerçekte Nasıl Çalışır? (Deep Level)

Gerçek teknik akış:

```
Source Code
      ↓
Lexical Analysis
      ↓
Parsing
      ↓
AST (Abstract Syntax Tree)
      ↓
Compile
      ↓
Bytecode
      ↓
Python Virtual Machine
      ↓
Execution
```

---

### AST (Abstract Syntax Tree) Nedir?

Kodun ağaç yapısındaki temsilidir.

Örnek:

```python
a = 1 + 2
```

AST yapısı:

```
=
├── a
└── +
    ├── 1
    └── 2
```

Interpreter önce AST oluşturur, sonra bytecode üretir.

---

## 8️⃣ Bu Konular Neden Kritik?

Bu konuları bilmeden:

- GIL neden var anlayamazsın  
- Thread neden paralel çalışmıyor kavrayamazsın  
- Memory nasıl yönetiliyor bilemezsin  
- Performance nasıl optimize edilir çözemezsin  
- Django’nun lazy evaluation mantığını anlayamazsın  

---

## 9️⃣ Production Açısından Önemi

Bu bilgiler şunları çözmeni sağlar:

- Memory leak debugging  
- Performance bottleneck analizi  
- Concurrency problemleri  
- CPU-bound vs IO-bound ayrımı  
- Profiling ve optimizasyon  

---

## 🔟 Mülakat Soruları

Sık sorulan sorular:

- Python interpreted mi compiled mı?  
- Bytecode nedir?  
- CPython nedir?  
- Python execution model nedir?  
- REPL nedir?  
- Python Virtual Machine nedir?  
- AST nedir ve neden önemlidir?  

---

## 🎯 Özet (Çok Kritik)

Python şu şekilde çalışır:

```
Python Code
      ↓
Compile
      ↓
Bytecode
      ↓
Python Virtual Machine
      ↓
Execution
```

Bu modeli anlamak, seni “Python bilen biri” seviyesinden  
“Python’u anlayan biri” seviyesine taşır.

---