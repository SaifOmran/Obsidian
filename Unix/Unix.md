# Oracle Solaris 11.4 Administration Notes
## Session 1

> **Prerequisite:** Linux Administration 1 & 2

---

# 1. Solaris Architecture

## Solaris vs Linux

- Solaris هو نظام **UNIX** من Oracle.
- Linux هو نظام Unix-like.
- معظم أوامر الـ Shell متشابهة.
- الاختلاف الحقيقي في أدوات الإدارة (Administration Tools).

---

## أهم المجلدات

| Directory | الاستخدام |
|-----------|-----------|
| `/etc` | ملفات إعدادات النظام |
| `/var` | Logs, Spool, Package Data |
| `/usr` | البرامج والمكتبات |
| `/opt` | البرامج الخارجية |
| `/export/home` | Home Directories (بديل `/home` في كثير من إصدارات Solaris) |
| `/devices` | الأجهزة الحقيقية |
| `/dev` | Device Links |
| `/kernel` | Kernel Modules |

---

## أوامر مهمة

عرض معلومات النظام

```bash
uname -a
```

عرض إصدار Solaris

```bash
cat /etc/release
```

عرض عدد المعالجات

```bash
psrinfo
```

عرض معلومات الهاردوير

```bash
prtconf
```

عرض الأقراص

```bash
format
```

---

# 2. IPS (Image Packaging System)

بديل:

- yum
- dnf

في Solaris هو:

```bash
pkg
```

---

## أشهر أوامر pkg

عرض الحزم المثبتة

```bash
pkg list
```

البحث عن Package

```bash
pkg search <package>
```

عرض معلومات Package

```bash
pkg info <package>
```

تثبيت Package

```bash
pkg install <package>
```

حذف Package

```bash
pkg uninstall <package>
```

تحديث النظام

```bash
pkg update
```

عرض الـ Publishers

```bash
pkg publisher
```

---

## Dependency Resolution

مثل yum و dnf تمامًا.

مثال

```bash
pkg install nginx
```

إذا احتاج nginx إلى Libraries أو Packages أخرى، سيقوم pkg بتثبيتها تلقائيًا.

---

# 3. Repositories (Publishers)

في Solaris تسمى الـ Repositories باسم:

> **Publishers**

عرضها

```bash
pkg publisher
```

إضافة Publisher

```bash
pkg set-publisher -g http://repo.example.com solaris
```

---

# 4. pkg info -r

```bash
pkg info -r vim
```

الخيار:

```text
-r = Repository
```

يعرض معلومات الحزمة الموجودة في الـ Repository حتى لو لم تكن مثبتة.

---

# 5. pkg install -n

```bash
pkg install -n vim
```

الخيار

```text
-n = No Execute
```

أو

```text
Dry Run
```

يعرض:

- Dependencies
- Required Space
- Boot Environment Creation
- Conflicts

ولا يقوم بتثبيت أي شيء.

---

# 6. pkg install -nv

```bash
pkg install -nv vim
```

يتكون من:

```text
-n = Dry Run
-v = Verbose
```

يعرض تفاصيل أكثر بدون تنفيذ التثبيت.

---

# 7. Boot Environment (BE)

من أهم مميزات Solaris.

Boot Environment هو نسخة قابلة للإقلاع من نظام التشغيل مبنية على ZFS.

---

## لماذا Boot Environment؟

قبل تنفيذ:

```bash
pkg update
```

قد يقوم Solaris بإنشاء Boot Environment جديد تلقائيًا.

إذا فشل التحديث:

1. Activate للـ Boot Environment القديم.
2. Reboot.
3. يعود النظام كما كان.

بدون Restore أو إعادة تثبيت.

---

## كيف يعمل؟

يعتمد على:

- ZFS Snapshot
- ZFS Clone
- Copy-on-Write

ولذلك إنشاؤه سريع ولا يستهلك مساحة كبيرة في البداية.

---

## أوامر Boot Environment

عرض جميع الـ Boot Environments

```bash
beadm list
```

إنشاء Boot Environment

```bash
beadm create before_patch
```

تفعيل Boot Environment

```bash
beadm activate before_patch
```

حذف Boot Environment

```bash
beadm destroy before_patch
```

---

## Active Flags

| Flag | المعنى                                   |
| ---- | ---------------------------------------- |
| N    | Active Now                               |
| R    | Next Reboot                              |
| NR   | Running الآن وسيُستخدم في الإقلاع القادم |

---

## Workflow

```text
pkg update
      │
      ▼
Create Boot Environment
      │
      ▼
Update System
      │
      ▼
Reboot
      │
      ▼
Problem?
      │
      ▼
beadm activate old_BE
reboot
```

---

# Linux vs Solaris

| Linux | Solaris |
|--------|----------|
| yum / dnf | pkg |
| systemctl | svcadm |
| systemctl status | svcs |
| ifconfig | ipadm |
| ext4 / xfs | ZFS |
| Snapshots (LVM/Btrfs) | Boot Environment |

---

# أوامر للحفظ
```

---

## Package Management

```bash
pkg list
pkg search
pkg info
pkg info -r
pkg install
pkg install -n
pkg install -nv
pkg uninstall
pkg update
pkg publisher
```

---

## Boot Environment

```bash
beadm list
beadm create
beadm activate
beadm destroy
```

---

# Interview Notes

- `pkg` يقوم بحل الـ Dependencies تلقائيًا.
- Solaris يستخدم **Publishers** بدلًا من Repositories.
- `pkg info -r` يبحث في الـ Repository وليس في الحزم المثبتة فقط.
- `pkg install -n` يقوم بمحاكاة التثبيت (Dry Run).
- `pkg install -nv` يقوم بمحاكاة التثبيت مع عرض تفاصيل إضافية.
- Boot Environment يسمح بالرجوع للنظام بسهولة بعد التحديثات.
- Boot Environment يعتمد على ZFS (Snapshots + Clones + Copy-on-Write).

---