# Backend Engineering Handbook

Backend Engineering Handbook; Python ve Django ekosistemi merkezli olarak backend geliştirme disiplinini **temelden production seviyesine**, oradan da **senior mühendislik perspektifine** kadar sistematik biçimde ele alan kapsamlı bir kaynaktır.

Bu repository yalnızca framework kullanımını değil; arka plandaki **mimari prensipleri, performans kararlarını, production gerçeklerini, scalability stratejilerini ve güvenlik yaklaşımlarını** öğretmeyi amaçlar.

Bu doküman bir tutorial değil, bir **engineering handbook**’tur.

---

# 🎯 Amaç

Bu repository'nin temel amacı:

> Mid-level bir geliştiriciyi sistematik şekilde Senior Backend Engineer seviyesine taşımak.

Bu hedef doğrultusunda aşağıdaki alanlar derinlemesine ele alınır:

- Python internal yapısı
- OOP ve abstraction
- Database internals ve performans
- Django & DRF production kullanımı
- Infrastructure ve deployment
- System design ve scalability
- Security ve testing
- Production engineering yaklaşımı

---

# 🧠 Bu Repository Nasıl Kullanılmalı?

Dokümanlar **sıralı öğrenme mantığıyla** hazırlanmıştır.

Önerilen öğrenme sırası:


Programming Fundamentals
→ Python Core
→ OOP
→ Software Design
→ Database
→ Django
→ Infrastructure
→ System Design
→ Security & Testing
→ Senior Topics


Her bölüm bir sonrakinin temelini oluşturur. Atlamadan ilerlemeniz önerilir.

---

# 📚 İçerik Planı

1. Programming Fundamentals  
2. Python Core  
3. OOP  
4. Software Design  
5. Concurrency & Parallelism  
6. Database  
7. Django  
8. Infrastructure  
9. System Design  
10. Security, Testing & Clean Code  
11. Senior Backend Topics  

---

# 📂 Repository Yapısı


backend-engineering-handbook
│
├─ 01_programming_fundamentals
│ ├─ 01_basic_concepts.md
│ ├─ 02_control_flow.md
│ └─ 03_functions.md
│
├─ 02_python_core
│ ├─ 01_python_basics.md
│ ├─ 02_data_types.md
│ ├─ 03_memory_model.md
│ ├─ 04_collections_internal.md
│ ├─ 05_advanced_functions.md
│ ├─ 06_generators_iterators.md
│ ├─ 07_decorators.md
│ ├─ 08_closures.md
│ ├─ 09_error_handling.md
│ └─ 10_modules_packages.md
│
├─ 03_oop
│ ├─ 01_oop_basics.md
│ ├─ 02_oop_principles.md
│ ├─ 03_advanced_oop.md
│ ├─ 04_class_types.md
│ └─ 05_advanced_oop-2.md
│
├─ 04_software_design
│ ├─ design_patterns.md
│ └─ solid_principles.md
│
├─ 05_concurrency_parallelism
│ └─ concurrency.md
│
├─ 06_database
│ ├─ 01_database_basics.md
│ ├─ 02_sql_basics.md
│ └─ 03_database_advanced.md
│
├─ 07_django
│ ├─ 01_django_core.md
│ ├─ 02_django_orm.md
│ ├─ 03_django_models.md
│ ├─ 04_django_views.md
│ ├─ 05_drf.md
│ ├─ 06_authentication.md
│ └─ 07_django_deployment.md
│
├─ 08_infrastructure
│ ├─ docker.md
│ ├─ git.md
│ ├─ http_web.md
│ ├─ production.md
│ └─ redis.md
│
├─ 09_system_design
│ ├─ 01_system_design_fundamentals.md
│ ├─ 02_scalability_and_performance.md
│ ├─ 03_distributed_systems.md
│ └─ 04_real_world_architecture_and_patterns.md
│
├─ 10_security_testing_clean_code
│ ├─ clean_code.md
│ ├─ security.md
│ └─ testing.md
│
├─ 11_senior_topics
│ └─ advanced_backend_concepts.md
│
└─ README.md


---

# 🏗 Bu Repo’da Öğreneceğiniz Kritik Engineering Alanları

## 1. Programming & Python Internals

- Python memory model
- Mutable vs immutable
- Stack vs heap
- Garbage collection
- Iterators & generators
- Decorators & closures
- Exception handling internals

Amaç: Python’ı sadece kullanmak değil, anlamak.

---

## 2. Object Oriented Programming & Design

- Encapsulation
- Abstraction
- Inheritance
- Polymorphism
- SOLID principles
- Design patterns

Amaç: maintainable ve scalable code yazabilmek.

---

## 3. Database Engineering

- SQL fundamentals
- Index internals
- Query optimization
- Transactions
- Isolation levels
- ACID properties
- Database performance tuning

Senior backend engineer için kritik bir alandır.

---

## 4. Django & API Engineering

- Django architecture
- ORM internals
- Query optimization
- Django Rest Framework
- Authentication systems
- Session vs Token vs JWT
- Production deployment

Amaç: Production seviyesinde Django bilgisi.

---

## 5. Infrastructure & Production Engineering

- HTTP internals
- Reverse proxy
- NGINX
- Gunicorn
- WSGI / ASGI
- Linux fundamentals
- Docker & Docker Compose
- Production deployment flow

Amaç: uygulamayı sadece yazmak değil, çalıştırabilmek.

---

## 6. System Design & Scalability

- Scalability principles
- Vertical scaling
- Horizontal scaling
- Load balancing
- Caching
- Distributed systems fundamentals
- Real-world architectures

Amaç: yüksek trafikli sistemleri anlayabilmek.

---

## 7. Security & Testing

- Backend security fundamentals
- Authentication security
- Secure architecture
- Testing strategies
- Clean code principles

Amaç: güvenli ve sürdürülebilir sistemler yazmak.

---

## 8. Senior Backend Engineering Perspective

Senior seviyede fark yaratan konular:

- Trade-off analysis
- Architecture decisions
- Performance reasoning
- Production debugging
- Observability mindset
- Scalability planning

---

# 🧩 Bu Repository’nin Farkı

Bu repository:

- Tutorial değildir
- Framework rehberi değildir

Bu repository:

Bir **Backend Engineering Knowledge Base**’dir.

Odak noktası:

- Nasıl yapılır → evet  
- Ama daha önemlisi → neden böyle yapılır  

---

# 🎯 Hedeflenen Seviye

Bu repository tamamlandığında aşağıdaki konularda güçlü bir anlayış kazanırsınız:

- Production backend systems
- Django production deployment
- Database optimization
- Scalable system design
- Infrastructure fundamentals
- Senior-level engineering thinking

---

# 👨‍💻 Kimler İçin Uygun?

Bu repository özellikle şu geliştiriciler için uygundur:

- Backend öğrenmek isteyenler
- Django developer’lar
- Mid-level engineer’lar
- Senior seviyeye geçmek isteyenler
- System design öğrenmek isteyenler
- Mülakatlara hazırlananlar

---

# 🚀 Uzun Vadeli Plan

Bu repository zamanla şu içeriklerle genişletilecektir:

- Production case studies
- Real-world architecture examples
- Deployment blueprints
- Scaling scenarios
- Interview preparation content

---

# 📌 Engineering Felsefesi

Backend engineering:

- Framework bilmek değildir  
- Syntax bilmek değildir  

Backend engineering:

- Sistem düşünme becerisidir  
- Trade-off yapabilme becerisidir  
- Production gerçeklerini anlayabilme becerisidir  

---

# 📄 Lisans

Bu repository eğitim ve referans amaçlı oluşturulmuştur.

---

# Final Not

Amaç sadece kod yazan değil,

**Production seviyesinde sistem tasarlayabilen ve inşa edebilen bir backend engineer yetiştirmektir.**