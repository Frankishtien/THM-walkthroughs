# HTTP Request Smuggling

<img width="1905" height="356" alt="image" src="https://github.com/user-attachments/assets/f7d81410-c325-4699-a292-d7dff76c2dd5" />

---


<details>
  <summary>Modern Infrastructure</summary>




1) زمان vs دلوقتي في الويب
--------------------------

زمان كان الموقع غالبًا بسيط:

```
User → Web Server → Database
```

يعني السيرفر الواحد يستقبل الطلب ويجيب البيانات ويرجع الرد.

دلوقتي المواقع الكبيرة بقت معمولة من أجزاء كتير:

```
User
 |
 |
Front-end Server (Proxy / Load Balancer)
 |
 |
Back-end Server
 |
 |
Database / APIs / Microservices
```

كل جزء له وظيفة معينة.

* * * * *

Components of Modern Web Applications
=====================================

1) Front-end Server
-------------------

ده أول حاجة الطلب بتوصلها.

غالبًا بيكون:

-   Reverse Proxy
-   Load Balancer

يعني أنت كمستخدم مش بتكلم الـ backend مباشرة.

مثال:

أنت تفتح:

```
example.com/profile
```

الطلب يروح الأول للـ Front-end server.

هو يقول:

> "تمام، الطلب ده هوديه للسيرفر رقم 3"

وبعدين يحوله للـ backend.

وظيفته:

-   توزيع الطلبات.
-   حماية السيرفرات الداخلية.
-   التعامل مع الـ SSL/TLS أحيانًا.
-   فلترة بعض الطلبات.

* * * * *

2) Back-end Server
------------------

ده العقل اللي بيشغل التطبيق.

هو اللي يعمل:

-   تسجيل الدخول.
-   معالجة البيانات.
-   التواصل مع قاعدة البيانات.

مثال:

أنت تعمل Login:

```
POST /login
username=ahmed
password=123
```

الـ backend:

-   يستقبل البيانات.
-   يراجع قاعدة البيانات.
-   يقولك صح أو غلط.

بيستخدم لغات زي:

-   PHP
-   Python
-   JavaScript (Node.js)

وفريموركات زي:

-   Laravel
-   Django
-   Node.js frameworks

* * * * *

3) Databases
------------

دي المخزن.

بتحفظ:

-   Users
-   Password hashes
-   Posts
-   Products

أمثلة:

SQL:

-   MySQL
-   PostgreSQL

NoSQL:

-   MongoDB وغيرها.

الشكل:

```
Backend
   |
   |
Database
```

* * * * *

4) APIs
-------

الـ API هو طريقة تخلي برنامج يكلم برنامج تاني.

مثال:

تطبيق الموبايل بتاع البنك.

لما تفتح التطبيق:

```
Mobile App
      |
      |
     API
      |
      |
 Backend
```

الـ API ينقل الطلبات والبيانات.

* * * * *

5) Microservices
----------------

بدل ما تعمل Backend واحد ضخم:

```
        Backend
          |
 -----------------
 |       |        |
User   Payment  Product
```

تعمل خدمات صغيرة:

```
User Service

Payment Service

Product Service
```

كل واحدة مستقلة.

الميزة:\
لو خدمة الدفع وقعت، باقي الموقع ممكن يفضل شغال.

* * * * *

Load Balancer و Reverse Proxy
=============================

دول مهمين جدًا للـ HTTP Request Smuggling.

Load Balancer
-------------

تخيل عندك موقع عليه مليون زائر.

عندك 3 سيرفرات:

```
          Users
            |
            |
      Load Balancer
       /     |\
      /      |\
 Server1 Server2 Server3
```

الـ Load Balancer يقول:

"Server1 مشغول، ابعت الطلب لـ Server2"

هدفه:

-   توزيع الحمل.
-   منع سيرفر واحد من الانهيار.
-   زيادة الـ availability.

أمثلة:

-   AWS Elastic Load Balancing
-   HAProxy
-   F5 BIG-IP

* * * * *

Reverse Proxy
-------------

ده قريب جدًا منه.

وظيفته:

```
Client
 |
 |
Reverse Proxy
 |
 |
Web Server
```

هو وسيط بينك وبين السيرفر الحقيقي.

أمثلة:

-   NGINX
-   Apache mod_proxy
-   Varnish

ليه مهم؟

لأن المستخدم لا يرى الـ backend الحقيقي.

بدل:

```
192.168.1.50/login
```

يشوف:

```
website.com/login
```

* * * * *

طيب إيه علاقة ده بـ HTTP Request Smuggling؟
===========================================

هنا بقى مربط الفرس
في الوضع الطبيعي:

```
User
 |
 |
Proxy
 |
 |
Backend
```

الـ Proxy يقرأ الـ HTTP Request بطريقة.

والـ Backend يقرأه بطريقة تانية.

لو حصل اختلاف بينهم، ممكن يحصل مشكلة.

مثال بسيط:

الـ Proxy يقول:

> "الطلب ده طوله 100 byte"

والـ Backend يقول:

> "لا، طوله 150 byte"

هنا يحصل لخبطة:

```
Proxy thinks:

[Request 1]

Backend thinks:

[Request 1][Hidden Request]
```

والـ attacker يقدر يحشر Request جوه Request.

وده هو معنى:

**HTTP Request Smuggling**

يعني:

> تهريب Request إضافي داخل Request عادي بسبب اختلاف فهم الـ Proxy والـ Backend لطول الطلب.

* * * * *

Caching Mechanisms
==================

الكاش = تخزين نسخة جاهزة بدل ما تعيد الحساب كل مرة.

مثال:

بدل ما كل مرة تطلب صورة:

```
image.jpg
```

السيرفر يعملها من جديد.

يحفظها:

```
Cache
 |
 image.jpg
```

المرة الجاية يجيبها بسرعة.

* * * * *

أنواع الكاش:
------------

### 1) Content Caching

يحفظ ملفات ثابتة:

-   Images
-   CSS
-   JavaScript

مثال:

بدل:

```
User → Server → image
```

تصبح:

```
User → Cache → image
```

أسرع.

* * * * *

### 2) Database Query Caching

لو عندك طلب بيتكرر:

مثلا:

"هات بيانات المستخدم رقم 10"

بدل ما كل مرة يسأل الـ DB:

يحفظ النتيجة.

* * * * *

### 3) Full Page Caching

يحفظ الصفحة كاملة.

مثلا موقع أخبار:

بدل توليد الصفحة لكل شخص:

يحفظ نسخة جاهزة.

* * * * *

### 4) CDN / Edge Caching

زي إن الموقع يحط نسخة من المحتوى في أماكن قريبة من المستخدم.

مثلا:

أنت في مصر.

بدل ما تجيب ملف من أمريكا:

تاخده من سيرفر قريب.

* * * * *

### 5) API Caching

لو API بيرجع نفس البيانات كثير:

يحفظ الرد.

* * * * *

الخلاصه
=========================

احفظ الصورة دي:

```
            User
              |
              |
     Reverse Proxy / Load Balancer
              |
              |
          Backend
              |
              |
          Database
```

المشكلة اللي هنستغلها بعدين:

```
Proxy interpretation ≠ Backend interpretation
```

يعني الاتنين مش فاهمين الـ HTTP Request بنفس الطريقة.

ومن هنا يبدأ **HTTP Request Smuggling**.


  
</details>














