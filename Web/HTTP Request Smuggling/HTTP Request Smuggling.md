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

<img width="1222" height="370" alt="image" src="https://github.com/user-attachments/assets/6b2f0a3b-f848-47f2-a4a4-83c03e4e7fd5" />


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



![](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/2e8e7deef6eaa04f3461647e61bf3566.svg)


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
e
q=smuggledData
0
```

* * * * *

نفهمها:

أول سطر:
--------

```
e
```

ده حجم الـ chunk.

لكن بالـ Hexadecimal.

يعني:

```
e = 14
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



<img width="927" height="425" alt="image" src="https://github.com/user-attachments/assets/349d70a3-9b43-47ca-bda6-e53fbb5a5ce2" />


  
</details>




<details>
  <summary>Request Smuggling CL.TE</summary>



أولًا يعني إيه CL.TE ؟
======================

الاسم نفسه بيفسر كل حاجة.

```
CL = Content-Length

TE = Transfer-Encoding
```

يعني:

**Front-end بيستخدم Content-Length**

لكن

**Back-end بيستخدم Transfer-Encoding**

وده سبب المشكلة.

* * * * *

الصورة الطبيعية
===============

عندك:

```
Attacker
    |
    |
Front-end (Proxy)
    |
    |
Back-end
```

الاتنين بيقرأوا نفس الـ Request.

لكن...

الـ Proxy يقول:

> "أنا هصدق Content-Length"

أما الـ Backend يقول:

> "لا... أنا هصدق Transfer-Encoding"

وده معناه إن كل واحد هيحدد **نهاية الـ Request** بطريقة مختلفة.


<img width="927" height="425" alt="image" src="https://github.com/user-attachments/assets/ebae78bf-98eb-48ce-9558-91d02380ac3c" />





* * * * *

تعال نشوف الـ Payload
=====================

```
POST /search HTTP/1.1
Host: example.com

Content-Length:130
Transfer-Encoding: chunked

0

POST /update HTTP/1.1
Host: example.com
Content-Length:13

isadmin=true
```


هنقسمها.

* * * * *

أول جزء
-------

```
POST /search HTTP/1.1
```

ده الـ Request الأساسي.

* * * * *

بعدها:

```
Content-Length:130
```

الـ Proxy يقول:

> "تمام... هقرأ 130 بايت."

* * * * *

لكن الـ Backend يشوف:

```
Transfer-Encoding: chunked
```

فيقول:

> "أنا ماليش دعوة بالـ Content-Length."

* * * * *

بعدين يشوف
----------

```
0
```


يعني:

> انتهى الـ Body.

يعني بالنسبة للـ Backend...

الـ Request انتهى هنا.

* * * * *

لكن الـ Proxy شايف حاجة تانية
=============================

الـ Proxy يقول:

"لا لا...

أنا لسه معدتش 130 بايت."

فيكمل قراءة:

```
POST /update HTTP/1.1
```

ويعتبرها...

جزء من الـ Body.

* * * * *

يعني بالنسبة للـ Proxy:

```
Request 1
|
|
POST /search

Body

0

POST /update

isadmin=true
```

كل ده Request واحدة.
لكن بيعتبر ال `POST /update`  جزئ من ال body

* * * * *

أما الـ Backend...

هيشوف:

```
Request 1

POST /search

Body

0
```

خلصت.

ثم بعدها يلاقي:

```
POST /update HTTP/1.1
```

فيقول:

> أوه...

ده Request جديدة.

* * * * *

يعني الـ Backend هيشغلها فعلاً.

وده اسمه:

> Request Smuggling

لأنك "هربت"

Request كاملة

داخل Request تانية.

* * * * *

ليه ده خطر؟
===========

تخيل بدل:

```
POST /update
```

كتبت:

```
POST /admin/deleteAllUsers
```

أو

```
GET /admin
```

أو

```
POST /change-password
```

الـ Backend ممكن ينفذها.

مع إن الـ Proxy مكانش يقصد يبعتها كـ Request مستقلة.

* * * * *

ليه اسمه Smuggling؟
===================

Smuggling يعني تهريب.

أنت عملت:

```
Request 1

جواها

Request 2
```

فالـ Proxy افتكرها Body.

لكن الـ Backend افتكرها Request جديدة.

* * * * *

نقطة مهمة جدًا
==============



> لو Content-Length غلط

يحصل مشاكل.


* * * * *

عندك Body:

```
username=test&query=test
```

عددها

24 Byte.

* * * * *

يبقى الصح:

```
Content-Length:24
```

<img width="1960" height="666" alt="image" src="https://github.com/user-attachments/assets/b1d25092-5057-4168-ac34-ee35b48ceb4d" />

<img width="1302" height="206" alt="image" src="https://github.com/user-attachments/assets/1fc62668-60a8-41e9-b550-3f5a92e0b0ec" />




* * * * *

لكن لو كتبت

```
Content-Length:10
```

الـ Proxy هيقول:

> اقرأ أول 10 Bytes بس.

<img width="2034" height="670" alt="image" src="https://github.com/user-attachments/assets/59d4e713-c0b0-4160-a3e0-de76817316c3" />


يعني:

```
username=t
```

بس.

وسيبقى الباقي:

```
est&query=test
```

<img width="1258" height="210" alt="image" src="https://github.com/user-attachments/assets/c4ac18f6-6b6d-42e2-b6e7-002893b50808" />


لسه موجود.

وده يخلي السيرفر يفسره بطريقة مختلفة.

* * * * *

مثال
====

الـ Body

```
username=test&query=test
```

24 Byte

لو قلت

```
Content-Length:15
```

السيرفر هيقرأ

```
username=test&
```

ويقف.

والباقي

```
query=test
```

هيفضل موجود في الـ TCP connection.

وده من الأسباب اللي بتخلي الـ Request التالية تلخبط الدنيا.

* * * * *

ليه لازم يكون الـ Content-Length دقيق؟
======================================

علشان الـ Proxy يعرف:

> فين نهاية الـ Request.

لو الرقم غلط:

هيقف بدري.

أو

هيكمل زيادة.

وفي الحالتين

يحصل اختلاف مع الـ Backend.


  
</details>





<details>
  <summary>Request Smuggling TE.CL</summary>



CL.TE
-----

```
Proxy  ---> Content-Length

Backend ---> Transfer-Encoding
```

TE.CL
-----

```
Proxy  ---> Transfer-Encoding

Backend ---> Content-Length
```


* * * * *

دلوقتي نشوف الـ Payload
=======================

```
POST / HTTP/1.1
Host: example.com

Content-Length: 4
Transfer-Encoding: chunked

78
POST /update HTTP/1.1
Host: example.com
Content-Length:15

isadmin=true
0
```

* * * * *

هنشوف الـ Proxy بيقرأ إزاي
==========================

الـ Proxy يعتمد على:

```
Transfer-Encoding: chunked
```

فيشوف:

```
78
```

طيب دي كام؟

```
78 Hex = 120 Decimal
```

يعني الـ Proxy يقول:

> "اقرأ الـ 120 Byte دول كلهم."

فيقرأ:

```
POST /update HTTP/1.1
Host: example.com
Content-Length:15

isadmin=true
```

وبعدين يشوف:

```
0
```

فيقول:

> انتهت الـ Chunked Body.

إذن بالنسبة للـ Proxy:

```
كل ده Request واحدة.
```

* * * * *

دلوقتي الـ Backend
==================

الـ Backend مش مهتم بالـ Chunked.

هو يشوف:

```
Content-Length:4
```

فيقول:

> "أنا هقرأ 4 Bytes بس."

* * * * *

أول 4 Bytes هما
---------------

```
78\r\n
```

(يعني الرقم `78` ونهاية السطر).

فبالنسبة له، بعد أول 4 بايت انتهى الـ Body.

* * * * *

بعدها يبص يلاقي:

```
POST /update HTTP/1.1
...
```

فيقول:

> "دي Request جديدة."

ويبدأ ينفذها.

* * * * *

الفرق بين CL.TE و TE.CL
=======================

### CL.TE

الـ Backend هو اللي خلص بدري لأنه شاف:

```
0
```

بينما الـ Proxy كان لسه مستني حسب `Content-Length`.

* * * * *

### TE.CL

العكس.

الـ Backend خلص بدري لأنه شاف:

```
Content-Length:4
```

بينما الـ Proxy كان لسه بيقرأ لحد:

```
0
```

* * * * *


ليه اختاروا Content-Length = 4 ؟
================================

دي من أكتر الحاجات اللي بتلخبط الناس.

بص على بداية الـ Body:

```
78
```

لكن في الحقيقة اللي بيتبعت على الشبكة مش `78` بس.

في HTTP بعد كل سطر فيه:

```
\r\n
```

يعني:

```
7  = 1 byte
8  = 1 byte
\r = 1 byte
\n = 1 byte
```

يبقى:

```
78\r\n = 4 Bytes
```

عشان كده اختاروا:

```
Content-Length:4
```

فالـ Backend يقرأ:

```
78\r\n
```

ويقف.

وأول حاجة يشوفها بعد كده هي:

```
POST /update
```

وده المطلوب.

* * * * *

سؤال مهم
========

يمكن تقول:

> "طب ليه الـ Backend اعتبر `POST /update` Request جديدة؟"

لأنه بعد ما خلص يقرأ الأربع بايت اللي طلبهم `Content-Length`، هو **لسه فاتح نفس اتصال TCP**، ولسه فيه بيانات جاية. أول سطر بعد كده شكله:

```
POST /update HTTP/1.1
```

وده بالضبط شكل بداية Request جديدة في HTTP/1.1، فيبدأ يفسرها كطلب جديد.




| CL.TE                                   | TE.CL                                                                    |
| --------------------------------------- | ------------------------------------------------------------------------ |
| Proxy يعتمد على **Content-Length**      | Proxy يعتمد على **Transfer-Encoding**                                    |
| Backend يعتمد على **Transfer-Encoding** | Backend يعتمد على **Content-Length**                                     |
| الـ Backend ينهي الـ Request عند `0`    | الـ Backend ينهي الـ Request عند عدد البايتات المحدد في `Content-Length` |







  
</details>



<details>
  <summary>Transfer Encoding Obfuscation (TE.TE)</summary>




أولًا يعني إيه TE.TE؟
=====================

الاسم يقول:

```
Transfer-Encoding
Transfer-Encoding
```

يعني:

```
Proxy  ---> يستخدم TE

Backend ---> يستخدم TE
```

طيب هتقول:

> "أمال فين المشكلة؟ ما الاتنين بيستخدموا نفس الـ Header!"

**وده السؤال الصح.** 👏

* * * * *

فين المشكلة؟
============

المشكلة **مش إنهم بيستخدموا Header مختلفة.**

المشكلة إنهم **بيفسروا نفس Header بطريقة مختلفة.**

يعني:

الاتنين شايفين:

```
Transfer-Encoding
```

لكن واحد يقول:

> "ده Header صحيحة."

والتاني يقول:

> "لا... دي Header بايظة."

* * * * *

مثال
----

لو كتبت:

```
Transfer-Encoding: chunked
```

دي صحيحة 100%.

* * * * *

لكن لو كتبت:

```
Transfer-Encoding: chunked1
```

دي غلط.

* * * * *

أو

```
Transfer-Encoding : chunked
```

(مسافة زيادة)

أو

```
Transfer-Encoding: chunk
```

أو

```
Transfer-Encoding: chunked, cow
```

فيه عشرات الطرق لتشويه (Obfuscate) الـ Header.

* * * * *

ليه نشوهها؟
===========

علشان نخلي:

الـ Proxy يقول:

> "أنا هعتبرها Transfer-Encoding."

لكن

الـ Backend يقول:

> "لا... دي Header غير صالحة."

أو العكس.

* * * * *

يعني TE.TE ممكن يتحول لإيه؟
===========================

وده أهم سطر في الدرس.

TE.TE **مش هجوم مستقل.**

هو مجرد طريقة تخلي:

```
Proxy يستخدم TE

Backend يستخدم CL
```

فتبقى:

```
TE.CL
```

أو

```
Proxy يستخدم CL

Backend يستخدم TE
```

فتبقى:

```
CL.TE
```

يعني TE.TE هو **وسيلة** لإجبار أحد الطرفين على تجاهل `Transfer-Encoding` واستخدام `Content-Length` بدلًا منها.

* * * * *

المثال
======

```
POST /

Content-Length:4

Transfer-Encoding: chunked

Transfer-Encoding: chunked1
```

* * * * *

الـ Proxy
---------

يشوف:

```
Transfer-Encoding: chunked
```

ويقول:

> تمام.

* * * * *

يشوف:

```
Transfer-Encoding: chunked1
```

ويقول:

> دي غلط.

فيتجاهلها.

ويكمل بالـ chunked العادية.

* * * * *

أما الـ Backend...

ممكن يعمل حاجة مختلفة.

مثلاً يقول:

> وجود Header متضاربة.

أو

> آخر Header هي اللي تمشي.

أو

> هتجاهل الأولى.

أو

> هرفض الاتنين.

وده بيعتمد على نوع السيرفر وإصداره.

* * * * *

ليه اسمه Obfuscation؟
=====================

Obfuscation يعني:

> تشويه أو تمويه.

يعني تخلي الـ Header شكلها غريب.

مثلاً:

```
Transfer-Encoding: chunked
Transfer-Encoding: chunked1
```

أو

```
Transfer-Encoding: xchunked
```

أو

```
Transfer-Encoding: chunked;
```

أو

```
Transfer-Encoding: chunked\t
```

كل Server له طريقة مختلفة في التعامل مع الحاجات دي.

* * * * *

طيب لو حصل اختلاف؟
==================

نفترض:

الـ Proxy قال:

> هستخدم Transfer-Encoding.

لكن الـ Backend قال:

> لا... الـ Transfer-Encoding بايظة.

هروح أستخدم:

```
Content-Length
```

خلاص.

بقت:

```
TE.CL
```

* * * * *

ولو العكس.

الـ Proxy رفض الـ TE.

لكن الـ Backend قبلها.

تبقى:

```
CL.TE
```

* * * * *

يبقى TE.TE عبارة عن إيه؟
========================

بص للرسم ده.

```
Attacker
    |
    |
Transfer-Encoding مكتوبة بطريقة غريبة
    |
    |
--------------------------
|                        |
Proxy              Backend
|                        |
يفهمها            يفهمها بشكل مختلف
```

ومن هنا يبدأ الاختلاف.

* * * * *

أهم معلومة في الجزئيه دي 
=======================

ناس كتير فاكرة:

> TE.TE يعني لازم يبقى فيه اتنين Transfer-Encoding.

وده **مش صحيح**.

thm قالت:

> **The TE.TE vulnerability doesn't always require multiple Transfer-Encoding headers.**

يعني ممكن يبقى فيه **Header واحدة بس**، لكن مكتوبة بشكل يخلي مكون يقبلها ومكون تاني يرفضها أو يفسرها بشكل مختلف.

مثلاً:

```
Transfer-Encoding: chunked;
```

بعض السيرفرات تقول:

> عادي.

وسيرفرات تانية تقول:

> Header غير صحيحة.

وهنا يبدأ الاختلاف.







| النوع     | الـ Proxy                                                       | الـ Backend       |
| --------- | --------------------------------------------------------------- | ----------------- |
| **CL.TE** | Content-Length                                                  | Transfer-Encoding |
| **TE.CL** | Transfer-Encoding                                               | Content-Length    |
| **TE.TE** | الاتنين بيستخدموا Transfer-Encoding، لكن **يختلفوا في تفسيرها** |                   |






  
</details>





<details>
  <summary>Walkthrough</summary>






  
</details>

























- how i now the proxy/server will user what ??
- TE.TE how i know whick one will accept the header 
>    by testing (jsut try!!!)























































