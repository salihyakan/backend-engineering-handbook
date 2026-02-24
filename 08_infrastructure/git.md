# 1. Git Nedir?

## Tanım

Git, bir **version control system**’dir (versiyon kontrol sistemi).

**Basit tanım:**

> Git, kodun zaman içindeki değişimlerini takip eder.

---

## Git Olmadan Problem

Kod yazıyorsun:

`user.py`

Bugün:

```python
print("v1")
```

Yarın:

```python
print("v2")
```

Bir bug çıktı → eski versiyona dönmek istiyorsun.

Ama eski kod yok.

---

## Git Çözümü

Git şunu yapar:

```
v1 → v2 → v3 → v4
```

İstediğin versiyona dönebilirsin.

---

## Git Neyi Takip Eder?

- Dosya değişiklikleri
- Satır değişiklikleri
- Kim değiştirdi
- Ne zaman değiştirdi

---

## Git = Snapshot Sistemi

Git her commit’te projenin snapshot’unu alır.

---

# 2. Git Temel Mimarisi

Git 3 katmandan oluşur:

- Working Directory
- Staging Area
- Repository

---

## Working Directory

Kod yazdığın yer:

```
project/
   main.py
```

---

## Staging Area

Commit’e hazırlanmış dosyalar.

---

## Repository

Commit’lerin saklandığı yer.

```
.git/
```

---

## Flow

```
edit file
 ↓
git add
 ↓
git commit
```

---

# 3. Commit Nedir?

## Tanım

Commit = Değişiklik snapshot’ı

---

## Örnek

```bash
git commit -m "add user model"
```

Git şunu kaydeder:

- Değişiklik
- Tarih
- Author
- Mesaj

---

## Commit Yapısı

```
commit 8a7f9d1
Author: Salih
Date: today

add user model
```

---

## Commit Neden Önemli?

Şunları sağlar:

- Geçmişe dönme
- Değişiklik takibi
- Debugging

---

## Django Örneği

```python
class User(models.Model):
    name = models.CharField(max_length=100)
```

Commit:

```bash
git commit -m "add User model"
```

---

# 4. Branch Nedir?

## Tanım

Branch = Ayrı geliştirme hattı

---

## Örnek

Ana branch:

```
main
```

Yeni feature geliştiriyorsun:

```
feature/login
```

---

## Görsel

```
main
  |
  |
feature/login
```

---

## Branch Oluşturma

```bash
git branch feature/login
```

Switch:

```bash
git checkout feature/login
```

veya

```bash
git switch feature/login
```

---

## Neden Branch Kullanılır?

Çünkü:

- main stabil kalır
- feature ayrı geliştirilir

Gerçek dünya:

```
main
feature/auth
feature/payment
feature/api
```

---

# 5. Merge Nedir?

## Tanım

Merge = Branch’leri birleştirme

---

## Örnek

Feature branch:

```
feature/login
```

Main’e eklemek:

```bash
git checkout main
git merge feature/login
```

---

## Görsel

Before:

```
main
  |
feature/login
```

After:

```
main
  |
  |
merged
```

---

## Django Gerçek Örnek

Feature branch’te:

```python
class User(models.Model):
    email = models.EmailField()
```

Merge → main branch artık bu kodu içerir.

---

# 6. Rebase Nedir?

## Tanım

Rebase = Commit geçmişini yeniden düzenleme

---

## Merge vs Rebase Farkı

Merge:

- Messy history

Rebase:

- Clean history

---

## Örnek

```bash
git checkout feature/login
git rebase main
```

---

## Rebase Ne Yapar?

Feature commit’lerini main’in üstüne taşır.

---

## Görsel

Before:

```
main
  |
  |
feature
```

After rebase:

```
main
  |
  |
  |
feature commits
```

---

## Neden Rebase Kullanılır?

- Temiz history
- Profesyonel workflow

---

# 7. Conflict Nedir?

## Tanım

Conflict = Aynı satırın farklı değiştirilmesi

---

## Örnek

main:

```python
name = "Ali"
```

feature:

```python
name = "Mehmet"
```

Merge → conflict

Git gösterir:

```
<<<<<<< HEAD
name = "Ali"
=======
name = "Mehmet"
>>>>>>> feature
```

---

## Conflict Çözümü

Seçersin:

```python
name = "Mehmet"
```

Sonra:

```bash
git add .
git commit
```

---

# 8. Gerçek Dünya Git Workflow

Profesyonel yapı:

- main (production)
- develop
- feature branches

---

## Flow

Yeni feature:

```bash
git checkout develop
git checkout -b feature/login
```

Kod yaz:

```bash
git add .
git commit -m "add login"
```

Merge:

```bash
git checkout develop
git merge feature/login
```

Production:

```bash
git checkout main
git merge develop
```

---

# 9. En Önemli Git Komutları

## Repo Oluşturma

```bash
git init
```

## Durum Kontrol

```bash
git status
```

## Dosya Ekleme

```bash
git add .
```

## Commit

```bash
git commit -m "message"
```

## Branch Oluşturma

```bash
git branch feature/login
```

## Branch Değiştirme

```bash
git switch feature/login
```

## Merge

```bash
git merge feature/login
```

## Log

```bash
git log
```

---

# 10. Remote Repository Nedir?

Remote = GitHub vb. uzak repo

Örnek platformlar:

- GitHub
- GitLab
- Bitbucket

---

## Remote Ekleme

```bash
git remote add origin url
```

## Push

```bash
git push origin main
```

## Pull

```bash
git pull origin main
```

---

# 11. Git Neden Kritik?

Çünkü:

- Ekip çalışması
- Production deployment
- CI/CD
- Version control

> Git bilmeyen developer = işe alınmaz.

---

# 12. Django Gerçek Dünya Örneği

Workflow:

```bash
git checkout -b feature/user-api
```

Kod yaz:

```python
class UserViewSet(ModelViewSet):
    pass
```

Commit:

```bash
git commit -m "add user api"
```

Push:

```bash
git push origin feature/user-api
```

PR → Merge → Deploy

---

# 13. Senior Seviyede Bilmen Gereken Kritik Noktalar

## Bilmen Gereken Kavramlar

- Commit
- Branch
- Merge
- Rebase
- Conflict
- Remote
- Push
- Pull

---

## Bilmen Gereken Workflow

- Feature branch
- Pull request
- Merge strategy

---

# Mülakat Soruları

## Git Nedir?

Version control system

---

## Commit Nedir?

Snapshot

---

## Branch Nedir?

Ayrı geliştirme hattı

---

## Merge Nedir?

Branch birleştirme

---

## Rebase Nedir?

History düzenleme

---

## Conflict Nedir?

Çakışma

---

# Özet

**Git:**  
Version control system

**Commit:**  
Snapshot

**Branch:**  
Ayrı geliştirme hattı

**Merge:**  
Birleştirme

**Rebase:**  
History düzenleme

**Conflict:**  
Çakışma