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


![image](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/adaa5ac3ca48557bdf12e4b7758fd429.svg)





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



<img width="1125" height="393" alt="image" src="https://github.com/user-attachments/assets/0cdbcd95-8e6e-44f2-b6f1-6556a15bbd58" />





  
</details>



<details>
  <summary>Behind the Scenes</summary>



أولًا: شكل HTTP Request
=======================

أي Request في HTTP بيتكون من 3 أجزاء أساسية:

```
Request Line
Headers
Body
```

يعني شكله كده:

```
POST /admin/login HTTP/1.1

Host: example.com
Content-Type: application/json
Content-Length: 20

username=ahmed
```

* * * * *

1) Request Line
===============

أول سطر في الطلب:

```
POST /admin/login HTTP/1.1
```

بيتكون من 3 حاجات:

Method
------

يعني الأمر اللي بتقوله للسيرفر.

أمثلة:

```
GET
```

يعني:

> هاتلي حاجة

مثلا:

```
GET /profile
```

* * * * *

```
POST
```

يعني:

> أنا هبعت بيانات

مثلا:

```
POST /login
```

معاه username و password.

* * * * *

Path
----

ده المكان اللي أنت عايزه داخل الموقع.

مثلا:

```
/admin/login
```

يعني صفحة تسجيل دخول الأدمن.

* * * * *

HTTP Version
------------

مثلا:

```
HTTP/1.1
```

ده إصدار البروتوكول.

مهم تعرف إن:\
HTTP/1.1 مختلف عن HTTP/2.

والـ Request Smuggling غالبًا بيظهر مع طريقة تعامل HTTP/1.1 مع الـ requests.

* * * * *

2) Headers
==========

الـ Headers هي معلومات إضافية عن الطلب.

اعتبرها زي ظرف الرسالة.

الرسالة نفسها:

```
Body
```

والظرف عليه معلومات:

```
Headers
```

مثال:

```
Host: website.com

Content-Type: application/json

Cookie: session=12345
```

الـ Headers تقول للسيرفر:

-   مين الموقع؟
-   نوع البيانات؟
-   هل فيه تسجيل دخول؟
-   حجم البيانات؟

* * * * *

3) Body
=======

ده البيانات الفعلية.

مثلا:

Login:

```
username=admin&password=test
```

أو JSON:

```
{
 "user":"admin",
 "pass":"123"
}
```

عادة:

GET:

```
Body = empty
```

لكن POST:

```
Body = data
```

* * * * *

أهم جزء: Content-Length 🔴
==========================

ده Header بيقول للسيرفر:

> حجم الـ Body كام بالبايت؟

مثال:

```
POST /submit HTTP/1.1
Host: good.com
Content-Length: 14

q=smuggledData
```

يعني السيرفر يقول:

"تمام، أنا منتظر 14 byte بعد الـ headers."

فيقرأ:

```
q=smuggledData
```

وبس.

* * * * *

المشكلة تبدأ هنا 😈
===================

لو حصل اختلاف في تحديد حجم الـ request.

يعني:

Front-end يقول:

> الـ request انتهى هنا

لكن Backend يقول:

> لا، لسه فيه بيانات

هنا يحصل Request Smuggling.

* * * * *

Transfer-Encoding: chunked
==========================

ده طريقة ثانية لمعرفة حجم الـ Body.

بدل ما تقول الحجم مرة واحدة:

```
Content-Length: 100
```

تقول:

"هجيب البيانات على أجزاء."

يعني chunks.

مثال:

```
Transfer-Encoding: chunked
```

بعدها:

```
b
q=smuggledData
0
```

* * * * *

نفهمها:

أول سطر:
--------

```
b
```

ده حجم الـ chunk.

لكن بالـ Hexadecimal.

يعني:

```
b = 11
```

بالعشري.

يعني السيرفر يستقبل 11 byte:

```
q=smuggledData
```

* * * * *

آخر حاجة:
---------

```
0
```

معناها:

> خلاص مفيش chunks تاني.

انتهى الـ Body.

* * * * *

طيب ليه عندنا طريقتين للحجم؟
============================

هنا المشكلة 👀

HTTP عنده طريقتين:

طريقة 1:

```
Content-Length
```

وطريقة 2:

```
Transfer-Encoding: chunked
```

المفروض تستخدم واحدة.

لكن لو الاتنين موجودين:

```
Content-Length: 20

Transfer-Encoding: chunked
```

يحصل confusion.

* * * * *

هنا يولد HTTP Request Smuggling 🔴
==================================

تخيل عندك:

```
User
 |
 |
Front-end Proxy
 |
 |
Backend
```

الـ Front-end يقرأ الـ request بطريقة.

والـ Backend يقرأه بطريقة مختلفة.

مثلا:

Front-end:
----------

يقول:

"أنا هثق في Content-Length"

```
Content-Length: 50
```

فيقرأ أول 50 byte فقط.

* * * * *

Backend:
--------

يقول:

"أنا هثق في Transfer-Encoding"

ويقرأ chunks.

* * * * *

النتيجة:

الاتنين عندهم حدود مختلفة للـ request.

يعني:

Front-end شايف:

```
Request 1
```

لكن Backend شايف:

```
Request 1 + Request مخبي
```

وده معنى:

Smuggling
---------

يعني:

"تهريب Request داخل Request آخر."

* * * * *

أنواع HTTP Request Smuggling اللي هتقابلها
==========================================

بعد شوية هتدرس:

CL.TE
-----

يعني:

Front-end يستخدم:

```
Content-Length
```

Backend يستخدم:

```
Transfer-Encoding
```

* * * * *

TE.CL
-----

العكس.

Front-end يستخدم:

```
Transfer-Encoding
```

Backend يستخدم:

```
Content-Length
```

* * * * *

TE.TE
-----

الاتنين يستخدموا Transfer-Encoding لكن بطريقة مختلفة.



  
</details>











































































