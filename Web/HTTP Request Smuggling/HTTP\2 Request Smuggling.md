# TryHackMe HTTP\2 Request Smuggling Walkthrough

<img width="1904" height="375" alt="image" src="https://github.com/user-attachments/assets/e889d0b6-7d43-4565-9144-6cb855abd40b" />


---

<details>
  <summary>HTTP/2 Explained</summary>


المشكلة في HTTP/1.1
===================

تخيل إنك بتبعت جواب.

كل سطر بتنهيه بعلامة معينة.

HTTP/1.1 بيستخدم

```
\r\n
```

يعني نهاية السطر.

فالسيرفر بيقول:

> "طالما لقيت \r\n يبقى ده آخر السطر."

وبرضو بيفصل بين الـ Headers والـ Body بسطر فاضي.

يعني شكله كده:

```
GET / HTTP/1.1
Host: test.com
Content-Length: 5

hello
```

كل ده عبارة عن Text.

* * * * *

المشكلة الكبيرة
===============

بما إنه Text...

يبقى لازم السيرفر يفضل يقرأ الحروف واحدة واحدة ويقول:

> "آه... لقيت نهاية السطر."

> "آه... ده Header."

> "آه... ده Body."

ولو حصل اختلاف بسيط بين جهازين في تفسير الرسالة...

تحصل مصيبة اسمها

> Request Smuggling

وده اللي الغرفة كلها بتتكلم عنه.

* * * * *

طيب HTTP/2 عمل إيه؟
===================

بدل ما يبعت الكلام كنص...

قال:

> "أنا هبعت Binary."

يعني الجهاز هيشوف

```
0101010101010010
```

مش Text.

إحنا كبشر مش هنفهمه.

لكن الكمبيوتر هيفهمه أسرع.

وده بيخلي القراءة أدق وأسرع.

* * * * *

ليه Binary أحسن؟
----------------

تخيل عندك ملف Word.

لو فتحته في Notepad...

هتشوف رموز غريبة.

لكن Word نفسه فاهمها.

نفس الفكرة.

HTTP/2 بيستخدم Format مخصص للكمبيوتر.

* * * * *

المؤلف قال
==========

> HTTP/2 requests use a completely binary protocol.

يعني

HTTP/1

```
GET /index HTTP/1.1
```

لكن HTTP/2 داخلياً بيبقى Binary.

* * * * *

طيب إحنا ليه بنشوفه مكتوب؟
==========================

عشان الشرح.

ولا Burp ولا المتصفح هيخلوك تقرأ الـ Binary.

بيحولوه لشكل مفهوم.

يعني لما تشوف

```
:method GET
:path /
```

ده مجرد Translation.

* * * * *

مكونات HTTP/2 Request
=====================

المؤلف قال فيه 3 أجزاء.

* * * * *

1) Pseudo-Headers
-----------------

ودي أهم حاجة.

شكلها

```
:method
:path
:scheme
:authority
```

خد بالك من النقطتين (:)

وده سبب تسميتها

Pseudo Header

يعني Header خاص بـ HTTP/2.

* * * * *

### :method

يعني

```
GET
POST
PUT
DELETE
```

مثال

```
:method GET
```

* * * * *

### :path

يعني

```
/login
```

أو

```
/admin
```

* * * * *

### :scheme

يعني البروتوكول

```
http
```

أو

```
https
```

* * * * *

### :authority

وده بديل

```
Host
```

في HTTP/1

كنا نكتب

```
Host: example.com
```

في HTTP/2

تبقى

```
:authority example.com
```

* * * * *

2)Headers العادية
==================

بعد الـ Pseudo Headers

تيجي الهيدرز الطبيعية.

زي

```
user-agent
cookie
accept
content-length
```

بس في HTTP/2

كلها Lowercase.

يعني

❌

```
User-Agent
```

✔

```
user-agent
```



<img width="915" height="242" alt="image" src="https://github.com/user-attachments/assets/131752bf-02c5-455e-a6c3-0967884d4f60" />



* * * * *

3)Body
=======

زي HTTP/1 بالظبط.

لو POST

هيبقى فيه

```
username=admin
password=123
```

أو

JSON

أو

ملفات.

* * * * *

## أهم جزء في الدرس كله

المؤلف قال

> HTTP/2 establishes precise boundaries.

ودي أهم جملة.

* * * * *

يعني إيه Boundaries؟
--------------------

تخيل عندك صندوق.

وجواه

```
اسم
عنوان
سن
```

في HTTP/1

أنت بتعرف نهاية الاسم لما تلاقي

```
\r\n
```






لكن في HTTP/2

هو بيقولك قبل الاسم:

```
الاسم طوله ٤ حروف.
```

مثلاً

```
Name Length = 4

Ahmed
```

مش محتاج أدور على نهاية.

أنا عارف إنها 4 Bytes.

* * * * *

مثال أبسط
---------

لو قلتلك

```
الرسالة طولها 10 حروف
```

يبقى هتقرأ 10 بالظبط.

مش هتخمن.

* * * * *

ليه ده مهم؟
===========

في HTTP/1

السيرفر ممكن يقول

> "أنا شايف الرسالة خلصت."

لكن البروكسي يقول

> "لا لسه."

ومن هنا يبدأ Request Smuggling.

لكن في HTTP/2

كل جزء مكتوب قدامه طوله.

فمفيش تخمين.

* * * * *

مثال
====

Header

```
:method
```

عدد الحروف

```
7
```

لأن

```
:method
```

=

```
7 bytes
```

بعدها

القيمة

```
GET
```

عددها

```
3
```

يبقى البروتوكول يبعت تقريباً

```
Length = 7

:method

Length = 3

GET
```

فالسيرفر هيقرأ بالظبط

```
7
```

وبعدين

```
3
```

وخلاص.

* * * * *

طب الـ Body؟
============

برضو له Length.

يعني لو فيه

```
username=admin
```

بيبقى مكتوب قبله

```
Body Length = XX
```

فمش محتاج

```
Content-Length
```

أصلاً.

* * * * *

المؤلف قال
==========

> Content-Length becomes meaningless.

ليه؟

لأن البروتوكول نفسه عارف طول الـ Body.

مش محتاج Header يقولوله.

* * * * *

طب ليه المتصفح لسه بيبعت Content-Length؟
========================================

دي نقطة مهمة جدًا وهتكون أساس الغرفة كلها.

لأن أحيانًا الطريق بيكون بالشكل ده:

```
Browser
      │
HTTP/2
      │
Reverse Proxy
      │
HTTP/1.1
      │
Backend Server
```

يعني:

-   المتصفح يكلم الـ Proxy بـ **HTTP/2**.
-   لكن الـ Proxy يكلم الـ Backend بـ **HTTP/1.1**.

التحويل ده اسمه **HTTP Downgrade**.

ولما يحصل تحويل لـ HTTP/1.1، الـ Backend يحتاج `Content-Length` لأنه بيعتمد عليه لمعرفة نهاية الـ Body.

عشان كده المتصفحات الحديثة أحيانًا تبعت `Content-Length` حتى وهي بتستخدم HTTP/2، استعدادًا لو حصل التحويل ده.

* * * * *

طيب...
======

إذا كان HTTP/2 حل المشكلة...

إزاي لسه فيه Request Smuggling؟

الإجابة هي مفتاح الروم كله.

المشكلة **مش في HTTP/2 نفسه**.

المشكلة في السيناريو ده:

```
Browser
      │
      │ HTTP/2
      ▼
Reverse Proxy
      │
      │ يحول الرسالة
      ▼
Backend
HTTP/1.1
```

هنا الـ **Reverse Proxy** لازم يحول الرسالة من HTTP/2 إلى HTTP/1.1.

وأثناء التحويل، لو عمله بطريقة غير صحيحة أو فسّر بعض الـ Headers بشكل مختلف، ممكن يرجّع نفس نوع الغموض (Ambiguity) اللي كان موجود في HTTP/1.1.

ومن هنا تظهر ثغرات **HTTP/2 Request Smuggling**.


  
</details>




<details>
  <summary>HTTP/2 Desync</summary>



أول حاجة: يعني إيه HTTP/2 Downgrading؟
======================================

خلينا نرسم شكل الموقع الحقيقي.

```
        Browser
           │
     HTTP/2 Request
           │
           ▼
   Reverse Proxy
           │
   (يحول البروتوكول)
           │
     HTTP/1.1 Request
           ▼
     Backend Server
```

<img width="772" height="221" alt="image" src="https://github.com/user-attachments/assets/45f65a0d-172b-496b-b244-6239faaebb59" />




السؤال:

**ليه أصلاً يعمل Downgrade؟**

لأن تحديث آلاف السيرفرات مرة واحدة لـ HTTP/2 مكلف وصعب.

فالشركة تقول:

> "هنخلي المستخدمين يكلمونا بـ HTTP/2، لكن السيرفرات الداخلية تفضل HTTP/1.1."

* * * * *

طيب الـ Reverse Proxy بيعمل إيه؟
================================

هو مترجم.

يعني العميل يقول:

```
:method POST
:path /login
:authority example.com
```

الـ Proxy يقول:

> "تمام... هحولها لـ HTTP/1.1."

فتبقى:

```
POST /login HTTP/1.1
Host: example.com
```

المفروض مفيش أي مشكلة.

* * * * *

المفروض يحصل إيه؟ (Expected Behaviour)
======================================

الكتاب بيقول:

> المفروض كل HTTP/2 Request تتحول إلى HTTP/1.1 Request واحدة فقط.

يعني

واحد

↓

واحد

```
HTTP/2 Request
        │
        ▼
HTTP/1.1 Request
```

بس...

المشكلة إن كل Proxy بيترجم بطريقة مختلفة.

ودي بداية الثغرة.

* * * * *


<img width="1270" height="541" alt="image" src="https://github.com/user-attachments/assets/b44c798b-9955-418c-a014-b10c7f0ff9b0" />




نقطة مهمة جدًا
==============

افتكر إننا قلنا قبل كده:

HTTP/2 **مش محتاج Content-Length.**

لأنه أصلاً عارف طول الـ Body.

طيب ليه بنشوف

```
content-length: 15
```

؟

عشان لو حصل Downgrade.

الـ Backend هيحتاجه.

* * * * *

بقى ندخل على أول نوع
====================

H2.CL
=====



<img width="1270" height="541" alt="image" src="https://github.com/user-attachments/assets/609bd6f2-7c2d-4a3c-82c4-e65107037a21" />




وده اختصار

```
HTTP/2 + Content-Length
```

وده أشهر نوع.

* * * * *

الفكرة كلها
-----------

أنت تبعت HTTP/2 Request فيها

```
content-length: 0
```

مع إنها فعليًا فيها Body.

* * * * *

مثلاً

```
POST / HTTP/2

content-length: 0

HELLO
```

لاحظ

الـ Body موجود

لكن

Content-Length

بيقول

```
0
```

* * * * *

الـ Proxy يعمل إيه؟
===================

الـ Proxy يقول:

> "أنا مجرد مترجم."

فيحولها

```
POST / HTTP/1.1
Content-Length: 0

HELLO
```

* * * * *

السيرفر يشوفها إزاي؟
====================

السيرفر يقول:

> Content-Length = 0

يبقى

> مفيش Body.

يبقى يعتبر إن الطلب انتهى عند الـ Headers.

* * * * *

ويبقى

```
HELLO
```

واقف لوحده.

السيرفر يقول:

> "دي بداية Request جديدة."

بس...

دي مش Request كاملة.

فيستنى.

* * * * *

يبقى دلوقتي حصل

```
Backend Connection

HELLO
```

واقف مستني يكمل.

وده اسمه

Backend Poisoning

أو

Desync.







* * * * *

بعد ثانية...
============

مستخدم تاني دخل الموقع.

وبعت

```
GET /home HTTP/1.1
Host: site
```

السيرفر هيعمل إيه؟

هيكمل

```
HELLO
```

بأول حاجة وصلت.

فتبقى

```
HELLOGET /home HTTP/1.1
```

لاحظ

HELLO

اتلزقت في أول Request الضحية.



<img width="1270" height="471" alt="image" src="https://github.com/user-attachments/assets/9f5114d6-a6f4-4ff4-9e15-2ec0f54808c1" />



* * * * *

وده اسمه

Request Desynchronization

لأن

الـ Proxy فاكر حاجة

والـ Backend فاكر حاجة تانية.

* * * * *

طيب ليه ده خطر؟
===============

لأن بدل

HELLO

أقدر أحط

```
GET /admin
```

أو

```
GET /logout
```

أو

أي Request.

* * * * *

وده أساس

HTTP Request Smuggling.

* * * * *

النوع الثاني
============

H2.TE
=====



<img width="1270" height="541" alt="image" src="https://github.com/user-attachments/assets/38a7d10e-7c39-4b97-bd5d-cf603dc16d81" />


وده

```
HTTP/2 + Transfer-Encoding
```

* * * * *

فاكر زمان في HTTP/1؟

كان فيه

```
Transfer-Encoding: chunked
```

؟

* * * * *

وده كان معناه

إن البيانات جاية Chunk Chunk.

مثلاً

```
4
test

3
abc

0
```

آخر

Chunk

دايمًا

```
0
```

* * * * *

في HTTP/2

الـ Header ده

مالوش لازمة.

لكن...

ممكن تضيفه.

* * * * *

فتبعت

```
Transfer-Encoding: chunked
```

* * * * *

الـ Proxy يقول

> ماشي.

ويبعته للـ Backend.

* * * * *

الـ Backend يقول

> آه ده Chunked.

ويقرأ أول Chunk.

* * * * *

لو أول Chunk

```
0
```

يبقى

> خلص الطلب.

* * * * *

وباقي البيانات؟

تبقى

Request جديدة.

* * * * *

إذن

H2.CL

و

H2.TE

الفكرة فيهم واحدة.

بس طريقة خداع الـ Backend مختلفة.

* * * * *

الفرق بينهم
===========

H2.CL
-----

بنضحك على

```
Content-Length
```

* * * * *

H2.TE
-----

بنضحك على

```
Transfer-Encoding
```

* * * * *

لكن النتيجة واحدة

↓

Desync.

* * * * *

ندخل على CRLF Injection
=======================

وده موضوع مهم جدًا.

* * * * *

أولًا

يعني إيه

CRLF؟

* * * * *

CR

=

Carriage Return

يعني

```
\r
```

* * * * *

LF

=

Line Feed

يعني

```
\n
```

* * * * *

الاتنين مع بعض

```
\r\n
```

يعني

نهاية السطر.

* * * * *

في HTTP/1

الهيدر بيبقى

```
Host: site
User-Agent: Chrome
```

كل Header وراه

```
\r\n
```

* * * * *

طيب...

لو أنا قدرت أحقن

```
\r\n
```

جوا Header؟

* * * * *

مثلاً

```
name=Ahmed\r\nX-Test:123
```

* * * * *

الـ Proxy يحولها

HTTP/1

تبقى

```
Name: Ahmed
X-Test:123
```

أنا كده

أنشأت Header جديدة.

* * * * *

وده اسمه

Header Injection.

<img width="1270" height="541" alt="image" src="https://github.com/user-attachments/assets/2ec827c8-ea7b-4cc8-b229-48a3eda1e86d" />




* * * * *

لكن الموضوع أخطر.

أنا ممكن أكتب

```
\r\n\r\n
```

يعني

خلص الـ Headers.

وابدأ Request جديدة.

* * * * *

يبقى أقدر أعمل

Request Smuggling

باستخدام

CRLF.

* * * * *

ليه ده موجود في HTTP/2؟
=======================

لأن HTTP/2 Binary.

مفيش مشكلة يبعت

أي Byte.

* * * * *

لكن

بعد التحويل

HTTP/1

الـ

```
\r\n
```

يبقى ليه معنى.

* * * * *

يعني

الثغرة مش في HTTP/2.

الثغرة في

التحويل.

* * * * *

ندخل على اللاب
==============

نفهمه بالراحة.

* * * * *

الموقع عبارة عن

Social Media.

كل واحد عنده بوست.

* * * * *

لما تعمل Like

المتصفح يبعت

```
GET /post/like/123
```

* * * * *

السيرفر يعرف مين عمل Like من

```
Cookie:
sessid=...
```

* * * * *

يعني

الكوكي هي الهوية.

* * * * *

إحنا عايزين إيه؟
================

عايزين

الضحية

تعمل Like لبوستنا.

من غير ما تعرف.

* * * * *

هنعمل إيه؟

هنبعت

POST

بدل

GET.

ليه؟

عشان

POST

فيه Body.

* * * * *

هنكتب

```
Content-Length: 0
```

لكن نحط

Body.

* * * * *

الـ Backend يقول

> مفيش Body.

* * * * *

ويعتبر

الـ Body

Request جديدة.

* * * * *

جوا الـ Body

هنكتب

```
GET /post/like/123
```

لكن...

مش هنكملها.

* * * * *

مثلاً

```
GET /post/like/123 HTTP/1.1
X: f
```

وبس.

* * * * *

السيرفر يقول

> Request ناقصة.

هستنى.

<img width="1342" height="541" alt="image" src="https://github.com/user-attachments/assets/b54cceb9-ac6b-4b24-a763-a40d27804825" />



* * * * *

بعد ثانية

الضحية تدخل.

ويبعت

```
GET /home HTTP/1.1
Cookie:
sessid=victim
```

* * * * *

السيرفر يركبهم مع بعض.

تبقى

```
GET /post/like/123 HTTP/1.1
X: fGET /home HTTP/1.1
Cookie: sessid=victim
```


<img width="1332" height="471" alt="image" src="https://github.com/user-attachments/assets/dc0a0e0f-ce6a-4b73-bec9-65e999af1ac9" />




لاحظ إن:

-   أول سطر (`GET /post/like/123 HTTP/1.1`) هو الـ **Request-Line** الحقيقي، وبالتالي السيرفر ينفذه.
-   سطر الضحية (`GET /home HTTP/1.1`) ما بقاش Request-Line جديد، بل بقى جزءًا من قيمة الهيدر `X:`، فيتجاهله السيرفر كطلب جديد.
-   لكن **Cookie الخاصة بالضحية** لسه موجودة في نفس الـ Request، فالسيرفر ينفذ طلب الـ Like باستخدام جلسة الضحية.

وده سبب إن الضحية تعمل Like لبوستك بدون قصد.

* * * * *

ليه المؤلف قال
==============

> لا تسيب Newline بعد X: f

لو كتبت:

```
GET /post/like/123 HTTP/1.1
X: f

```

(لاحظ السطر الفاضي في الآخر)

فأنت كده قفلت الـ Headers.

ولما Request الضحية تيجي، هتبدأ Request جديدة بدل ما تلزق في قيمة `X:`، وساعتها الهجوم يفشل.

عشان كده لازم تسيب:

```
GET /post/like/123 HTTP/1.1
X: f
```

بدون أي `\r\n\r\n` إضافية في النهاية.

* * * * *

ليه في Burp بنقفل "Update Content-Length"؟
==========================================

لأن Burp لو سبت الخيار شغال هيحسب طول الـ Body الحقيقي، وهيغير:

```
Content-Length: 0
```

إلى القيمة الصحيحة.

وساعتها الـ Backend هيقرأ الـ Body عادي، ومش هيحصل أي Desync.

إحنا **متعمدين** نخلي `Content-Length: 0` رغم إن فيه بيانات بعدها، عشان نخدع الـ Backend.





  
</details>



<details>
  <summary>HTTP/2 Request Tunneling</summary>


Summary: Request Tunnelling vs Desync
----------------------------------------

-   في **Request Smuggling التقليدي (Desync)** الهجوم يعتمد على إن الـ Backend يستخدم **نفس الـ Connection لكل المستخدمين**.
-   لما كل المستخدمين يكونوا على نفس الـ Backend Connection:
    -   المهاجم يقدر يحقن Request مخفي.
    -   الـ Backend ممكن يخلط بين Request المهاجم و Request مستخدم آخر.
    -   النتيجة: التأثير على الضحايا.

* * * * *

-   بعض الـ Proxy implementations تعمل **Per-user Backend Connections**:
    -   كل مستخدم له Connection منفصلة مع الـ Backend.
    -   مثال:

        ```
        Attacker → Connection A → Backend

        Victim   → Connection B → Backend
        ```


<img width="841" height="332" alt="image" src="https://github.com/user-attachments/assets/725c33b9-824f-4f11-b17d-5b3d2d54ac14" />



-   في الحالة دي:
    -   المهاجم لا يستطيع التأثير على Requests الخاصة بالمستخدمين الآخرين.
    -   لأن مفيش Connection مشتركة بينهم.

* * * * *

Request Tunnelling
------------------

-   رغم عدم القدرة على مهاجمة مستخدمين آخرين، ما زال يمكن عمل Smuggling داخل **Connection المهاجم نفسه**.
-   يعني:
    -   بدل ما نخبي Request للضحية.
    -   نخبي Request إضافية ونخليها تعدي من خلال الـ Frontend Proxy للـ Backend.
-   لذلك يسمى:\
    **Request Tunnelling**

    لأنه يعمل كأنه نفق يسمح بتمرير Requests للـ Backend بطريقة غير متوقعة.

* * * * *

الفرق السريع:
-------------

| Desync / Request Smuggling | Request Tunnelling |
| --- | --- |
| يستهدف مستخدم آخر | يستهدف نفس اتصال المهاجم |
| يحتاج Shared Backend Connection | لا يحتاج |
| الهدف: خلط Requests بين المستخدمين | الهدف: تمرير Requests مخفية للـ Backend |

* * * * *

نقطة مهمة:
----------

في اللابات القادمة سيتم استخدام:

-   **HAProxy قديم**
-   فيه ثغرة **CVE-2019-19330**
-   الثغرة تعتمد على **CRLF Injection**
-   وتسمح بعمل Request Smuggling.





  
</details>





<details>
  <summary>HTTP/2 Request Tunneling: Leaking Internal Headers</summary>



Summary: Leaking Internal Headers (Request Tunnelling)
=========================================================

1) الهدف من الهجوم
------------------

في Request Tunnelling، إحنا مش بنحاول نأثر على مستخدمين تانيين.

هدفنا هنا:

> معرفة الـ Headers الداخلية التي يضيفها الـ Frontend Proxy قبل إرسال الطلب للـ Backend.

مثال:

الـ Proxy يضيف:

```
X-Internal: secret
```

إحنا لا نراها، لكن الـ Backend يراها.

* * * * *

2) ليه نحتاج الـ Internal Headers؟
==================================

لو عايزين نعمل Request مخفي للـ Backend، ممكن الـ Backend يحتاج Headers معينة.

لذلك نحتاج نعرف:

-   ما هي الـ Headers التي يضيفها الـ Proxy؟
-   أين يتم وضعها؟
-   ما قيمتها؟

* * * * *

3) طريقة تسريب الـ Headers
==========================

نستغل خاصية في التطبيق:

Endpoint:

```
POST /hello
```

يأخذ Parameter اسمه:

```
q
```

ويقوم بعكسه في الـ Response.

مثال:

Request:

```
POST /hello

q=test
```

Response:

```
test
```

إذن نستخدمه ليطبع لنا الـ Headers السرية.



<img width="1483" height="317" alt="image" src="https://github.com/user-attachments/assets/81158a7d-ff8e-4a8b-afd3-46c946b9969e" />



* * * * *

4) شكل الاتصال
==============

```
Attacker
    |
    v
Frontend Proxy (HAProxy)
    |
    v
Backend
```

الـ Proxy يحول:

```
HTTP/2
```

إلى:

```
HTTP/1.1
```

ويضيف Headers داخلية أثناء التحويل.

* * * * *

5) مكان الثغرة
==============

نسخة HAProxy المستخدمة فيها:

**CRLF Injection**

يعني نستطيع إدخال:

```
\r\n
```

داخل قيمة Header.

والـ CRLF معناها:

-   `\r\n` = نزول سطر جديد.
-   `\r\n\r\n` = نهاية الـ Headers وبداية الـ Body.

* * * * *

6) مكان الحقن
=============

نضيف Header نتحكم فيه:

```
Foo: bar
```

ثم نجعل قيمة Foo تحتوي على CRLF.

بدل:

```
Foo: bar
```

تصبح القيمة:

```
bar
Content-Length: 0

POST /hello HTTP/1.1
Host: MACHINE_IP:8100
Content-Length: 300

q=
```


<img width="1336" height="652" alt="image" src="https://github.com/user-attachments/assets/4ea38fa9-ccde-4afb-84ef-8ec9b06aa6ec" />




* * * * *

7) ماذا يحدث؟
=============

الـ Frontend يرى:

Request واحد HTTP/2.

لكن بعد التحويل، الـ Backend يراه كأنه:

Request 1:
----------

```
POST /hello HTTP/1.1
Foo: bar
Content-Length: 0

```

بسبب:

```
Content-Length: 0
```

ينتهي الطلب الأول.

* * * * *

Request 2:
----------

```
POST /hello HTTP/1.1
Host: MACHINE_IP:8100
Content-Length: 300

q=...
```

الـ Backend يعتبره Request جديد.

* * * * *

8) أين تذهب الـ Internal Headers؟
=================================

الـ Proxy يضيف Headers مثل:

```
X-Internal: secret
```

ونحن نجعلها تدخل في:

```
q=
```

لأن التطبيق يعكس قيمة q.

فتظهر لنا في الـ Response.

* * * * *

9) لماذا نستخدم Content-Length؟
===============================

في الطلب الأول:
---------------

```
Content-Length: 0
```

الهدف:

إجبار الـ Backend على إنهاء الطلب الأول وعدم انتظار Body.

* * * * *

في الطلب الثاني:
----------------

```
Content-Length: 300
```

الهدف:

تحديد حجم البيانات التي نريد قراءتها.

لو الرقم صغير:

-   نحصل على جزء فقط من الـ Headers.

لو الرقم كبير:

-   الـ Connection قد ينتظر بيانات غير موجودة.

* * * * *

10) خطوات Burp باختصار
======================

1.  التقط Request الخاص بـ:

```
POST /hello
```

1.  أرسله إلى Repeater.
2.  احذف الـ Body.
3.  ضع:

```
Content-Length: 0
```

1.  أضف Header:

```
Foo: bar
```

1.  عدل قيمة Foo من Inspector.
2.  أدخل CRLF باستخدام:

```
SHIFT + ENTER
```

1.  ضع الـ Payload داخل قيمة Foo.
2.  أرسل الطلب مرتين بسرعة.








<img width="1919" height="776" alt="image" src="https://github.com/user-attachments/assets/5d897f8b-e06f-4ef6-a30b-266ef090caa9" />

<img width="1919" height="702" alt="image" src="https://github.com/user-attachments/assets/cbbd39b3-bc7a-43e5-9d50-46958afc1c78" />



  
</details>



<details>
  <summary>HTTP/2 Request Tunneling: Bypassing Frontend Restrictions</summary>


.

* * * * *

Summary: Bypassing Frontend Restrictions
===========================================

الفكرة
------

بعض المواقع تحط الحماية على **Frontend Proxy** بدل الـ Backend.

مثال:

```
/admin  ❌ ممنوع
/hello  ✅ مسموح
```

يعني لو طلبت:

```
GET /admin
```

الـ Proxy يمنع الطلب قبل ما يوصل للـ Backend.

* * * * *

الهدف
=====

الوصول إلى:

```
/admin
```

بدون أن يعرف الـ Frontend Proxy.

* * * * *

الفكرة الأساسية
===============

نستخدم Resource مسموح مثل:

```
/hello
```

كغطاء (Cover).

ونخفي داخله Request آخر إلى:

```
/admin
```

* * * * *

كيف يتم ذلك؟
============

نستغل ثغرة:

**CRLF Injection**

داخل Header نتحكم فيه مثل:

```
Foo: bar
```

ونجعل قيمة الـ Header تحتوي على Request جديد.

* * * * *

ماذا يرى الـ Frontend؟
======================

يرى فقط:

```
POST /hello HTTP/2
```

فيقول:

✅ الطلب مسموح.

* * * * *

ماذا يرى الـ Backend؟
=====================

بعد التحويل إلى HTTP/1.1 يرى:

Request الأول
-------------

```
POST /hello HTTP/1.1
```

ثم بسبب الـ CRLF يبدأ:

Request الثاني
--------------

```
GET /admin HTTP/1.1
```

فيتعامل معه كطلب مستقل.


<img width="1187" height="652" alt="image" src="https://github.com/user-attachments/assets/4c9f416e-997e-4789-8d17-ee8337a52b90" />


* * * * *

لماذا يستخدم POST؟
==================

لأن:

```
GET
```

قد يتم تقديمه من الـ Cache الخاصة بالـ Proxy.

وبالتالي قد لا يصل للـ Backend.

أما:

```
POST
```

فعادة لا يتم Cache له.

لذلك يصل دائمًا إلى الـ Backend.

* * * * *

لماذا نرسل الطلب مرتين؟
=======================

لأن الـ Request الثاني يكون غير مكتمل في البداية.

الإرسال الأول:

-   يضع الـ Request المخفي داخل الـ Connection.

الإرسال الثاني:

-   يجعل الـ Backend ينفذ الطلب ويرسل الرد الخاص بـ `/admin`.



---


```http
bar
Host: 10.112.149.110:8100

GET /admin HTTP/1.1
X: f
```


<img width="1916" height="696" alt="image" src="https://github.com/user-attachments/assets/e1e82f0c-57f4-402b-a3a4-e2d98c91198a" />






  
</details>





























































































































































