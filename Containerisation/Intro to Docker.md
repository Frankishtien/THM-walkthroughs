# Intro to Docker

<img width="1902" height="373" alt="image" src="https://github.com/user-attachments/assets/910487df-0fe2-4b16-9183-f3dd17d41403" />

---

<details>
  <summary>Basic Docker Syntax</summary>




Docker Pull
===========

قبل ما نشغل Container لازم ننزل Image.

عشان كده بنستخدم:

```
docker pull nginx
```

Docker بيروح للـ Registry ويجيب Image اسمها nginx.

* * * * *

Registry ده إيه؟
----------------

زي GitHub ولكن للـ Images.

أشهر Registry:

Docker Hub

لما تكتب:

```
docker pull nginx
```

Docker فعلياً بيروح لـ Docker Hub ويحمل الصورة.

* * * * *

ماذا يحدث أثناء التحميل؟
------------------------

```
docker pull nginx
```

هتشوف:

```
Pulling from library/nginx
```

معناها:

```
Docker Hub
   ↓
nginx repository
   ↓
تحميل الملفات
```

* * * * *

Tag
===

دي مهمة جداً.

كل Image لها:

```
name:tag
```

مثال:

```
ubuntu:22.04
```

-   ubuntu = اسم الصورة
-   22.04 = النسخة

* * * * *

مثال:

```
docker pull ubuntu:22.04
```

يعني:

> نزل Ubuntu إصدار 22.04

* * * * *

مثال آخر:

```
docker pull ubuntu:20.04
```

يعني:

> نزل Ubuntu إصدار 20.04

* * * * *

latest يعني إيه؟
----------------

لو كتبت:

```
docker pull ubuntu
```

Docker يفترض:

```
docker pull ubuntu:latest
```

يعني:

> نزل أحدث إصدار.

لكن في الواقع العملي ناس كتير بتتجنب latest لأن تحديث جديد ممكن يكسر التطبيق.

* * * * *

docker image
============

ده قسم إدارة الصور.

لما تكتب:

```
docker image
```

هيقولك الأوامر المتاحة.

زي:

```
ls
pull
rm
build
```

* * * * *

docker image ls
===============

حرفياً:

```
list images
```

يعني:

> اعرض كل الصور الموجودة عندي.

* * * * *

مثال:

```
docker image ls
```

الناتج:

```
REPOSITORY   TAG      IMAGE ID
ubuntu       22.04    2dc39ba059dc
nginx        latest   2b7d6430f78d
```

* * * * *

REPOSITORY
----------

اسم الصورة.

```
ubuntu
nginx
```

* * * * *

TAG
---

الإصدار.

```
22.04
latest
```

* * * * *

IMAGE ID
--------

معرف فريد للصورة.

```
2dc39ba059dc
```

كل Image لها ID خاص.

زي بصمة.

* * * * *

SIZE
----

حجم الصورة.

مثلاً:

```
77.8 MB
```

أو

```
142 MB
```

* * * * *

docker image rm
===============

لو عاوز تحذف Image.

مثال:

```
docker image rm ubuntu:22.04
```

Docker هيحذف الصورة.

* * * * *

ليه كتبنا:

```
ubuntu:22.04
```

وليس:

```
ubuntu
```

لأن ممكن يكون عندك:

```
ubuntu:18.04
ubuntu:20.04
ubuntu:22.04
```

Docker لازم يعرف أنهي نسخة يحذف.

* * * * *

بعد الحذف
---------

لو كتبت:

```
docker image ls
```

هتلاقي:

```
nginx
```

فقط.

* * * * *

ملخص سريع
=========

تحميل صورة
----------

```
docker pull nginx
```

* * * * *

تحميل إصدار معين
----------------

```
docker pull ubuntu:22.04
```

* * * * *

عرض الصور الموجودة
------------------

```
docker image ls
```

* * * * *

حذف صورة
--------

```
docker image rm ubuntu:22.04
```



  
</details>





<details>
  <summary>Running Your First Container</summary>




  
</details>
































