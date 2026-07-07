# CSRF
---

<img width="1897" height="373" alt="image" src="https://github.com/user-attachments/assets/8face8cf-6aeb-4713-b61a-11a6fb41f675" />

---

<details>
  <summary>Overview of CSRF Attack</summary>


يعني إيه CSRF؟
--------------

**CSRF = Cross-Site Request Forgery**

هو هجوم بيخلي **المستخدم نفسه** (المتسجل دخول بالفعل) يبعت Request للموقع **من غير ما يقصد**.

الفكرة الأساسية:

-   المستخدم عامل Login في موقع معين.
-   المتصفح مخزن الـ **Session Cookie**.
-   المهاجم يخدع المستخدم إنه يفتح لينك أو صفحة فيها كود مخفي.
-   المتصفح يبعت الـ Request للموقع **ومعه الـ Cookie تلقائيًا**.
-   الموقع يفتكر إن المستخدم هو اللي طلب العملية وينفذها.

* * * * *

دورة الهجوم (Cycle)
-------------------

### 1\. المهاجم يعرف شكل الـ Request

مثلاً يعرف إن تغيير الإيميل بيتم كده:

```
POST /change-emailemail=hacker@gmail.com
```

* * * * *

### 2\. يجهز صفحة خبيثة

مثلاً يعمل صفحة فيها Form مخفي:

```html
<form action="https://bank.com/change-email" method="POST">
    <input type="hidden" name="email" value="hacker@gmail.com">
</form>

<script>
document.forms[0].submit();
</script>
```

* * * * *

### 3\. الضحية يفتح الصفحة

الضحية أصلاً عامل Login على الموقع.

لما يفتح الصفحة:

-   المتصفح يبعت الـ Request.
-   الـ Session Cookie تتبعت تلقائيًا.
-   الموقع يفتكر إن الطلب جاي من المستخدم الحقيقي.
-   يتم تغيير الإيميل أو تنفيذ العملية.

* * * * *

ليه الهجوم نجح؟
---------------

لأن الموقع **معرفش يفرق** بين:

-   Request حقيقي من المستخدم.
-   Request اتعمل من موقع تاني.

* * * * *

أضرار CSRF
----------

المهاجم ممكن يعمل:

-   ✅ تغيير كلمة السر.
-   ✅ تغيير الإيميل.
-   ✅ تحويل فلوس.
-   ✅ شراء منتجات.
-   ✅ حذف بيانات.
-   ✅ تغيير إعدادات الحساب.

كل ده باسم المستخدم الحقيقي.

* * * * *

ليه الـ Cookie مهمة؟
--------------------

المتصفح تلقائيًا بيبعت الـ Cookie مع أي Request لنفس الموقع.

يعني حتى لو الـ Request بدأ من موقع المهاجم، طالما رايح لنفس الدومين، الـ Cookie هتتبعت، والموقع هيعتبر المستخدم مسجل دخول.



  
</details>




<details>
  <summary>Types of CSRF Attack</summary>


1\. Traditional CSRF (الهجوم التقليدي)
======================================

وده أشهر وأقدم نوع.

### الفكرة

المهاجم يعمل **Form أو Link**، ولما الضحية يضغط عليه، المتصفح يبعت Request للموقع اللي الضحية عامل عليه Login.

مثال:

أنت فاتح موقع البنك.

المهاجم يبعتلك إيميل فيه:

> اضغط هنا علشان تكسب 1000$

أول ما تضغط:

المتصفح يبعت Request زي:

```
POST /transferamount=1000to=attacker
```

ويضيف تلقائياً

```
Cookie: session=abc123
```

فالبنك يفتكر إن **أنت** اللي حولت الفلوس.

* * * * *

خطوات الهجوم
------------

1.  الضحية عامل Login.
2.  المهاجم يبعت Link أو Form.
3.  الضحية يضغط.
4.  المتصفح يبعت الـ Cookie.
5.  الموقع ينفذ العملية.

* * * * *

2\. XMLHttpRequest / AJAX CSRF
==============================

<details>
  <summary>what is Asynchronous HTTP Request</summary>



يعني إيه Asynchronous HTTP Request؟
-----------------------------------

كلمة **Asynchronous** معناها **"غير متزامن"** أو ببساطة: الصفحة **مش بتستنى الرد** عشان تكمل شغلها.

### مثال من غير Asynchronous (Synchronous)

زمان لما تضغط زرار "تسجيل الدخول":

1.  تضغط Login.
2.  الصفحة تبيض أو تعمل Loading.
3.  المتصفح يبعت Request.
4.  يستنى Response.
5.  يحمل صفحة جديدة.

يعني المتصفح **واقف مستني** الرد.

```
ضغطت الزر    ↓إرسال Request    ↓انتظار الرد    ↓تحميل صفحة جديدة
```

* * * * *

مع Asynchronous
---------------

دلوقتي في مواقع زي Gmail أو Facebook أو X:

تدوس لايك ❤️

هل الصفحة كلها بتعمل Reload؟

**لأ.**

اللي بيحصل:

-   JavaScript يبعت Request في الخلفية.
-   الصفحة تفضل شغالة عادي.
-   أول ما السيرفر يرد، يتم تحديث الجزء المطلوب فقط.

```
ضغطت Like     ↓JavaScript بعت Request     ↓الصفحة لسه شغالة     ↓الرد وصل     ↓عدد اللايكات اتحدث
```

وده هو **Asynchronous HTTP Request**.

* * * * *




  
</details>



ده نفس الهجوم، لكن بدل ما يستخدم Form، بيستخدم JavaScript.

يعني يستخدم

-   XMLHttpRequest
-   Fetch API

بدل

```
<form>
```

* * * * *

### مثال

عندك موقع بريد إلكتروني.

فيه زرار:

```
Change Email
```

ولما تدوس عليه، الصفحة **مش بتعمل Reload**.

لكن JavaScript بيبعت

```
fetch("/api/updateEmail", {    method: "POST",    body: JSON.stringify({        email: "new@email.com"    })})
```

وده اسمه AJAX Request.

* * * * *

المهاجم يعمل صفحة فيها

```
fetch("https://mailbox.thm/api/updateEmail",{    method:"POST",    body:...})
```

لو الموقع معندوش حماية CSRF...

المتصفح هيبعت

```
Cookie
```

مع الـ Request.

وهيتغير الإيميل.

* * * * *

خطواته
------

1.  الضحية عامل Login.
2.  يزور موقع المهاجم.
3.  JavaScript يشتغل.
4.  يعمل AJAX Request.
5.  الـ Cookie تتبعت.
6.  الموقع ينفذ الطلب.

* * * * *

الفرق بين Traditional و AJAX
----------------------------

Traditional

```
Form↓↓POST↓↓الموقع
```

AJAX

```
JavaScript↓↓Fetch/XMLHttpRequest↓↓الموقع
```

الفكرة واحدة.

الطريقة مختلفة.

* * * * *

3\. Flash-based CSRF
====================

وده بقى قديم جداً.

زمان قبل HTML5 كان فيه

```
Adobe Flash
```

وكان بيشغل ملفات

```
.swf
```

* * * * *

المهاجم يعمل ملف

```
evil.swf
```

ويحطه على موقعه.

لما الضحية يفتحه،

ملف الـ Flash يبعت Requests للموقع اللي الضحية عامل عليه Login.

زي بالضبط الـ Form أو الـ AJAX.

* * * * *

لكن...

Adobe Flash اتقفل رسمياً سنة 2020.

يعني النوع ده تقريباً مش هتشوفه في الواقع دلوقتي.


| النوع               | بيستخدم إيه؟                         | مستخدم دلوقتي؟                    |
| ------------------- | ------------------------------------ | --------------------------------- |
| Traditional CSRF    | HTML Form أو Link                    | ✅ نعم                             |
| XMLHttpRequest CSRF | JavaScript (Fetch أو XMLHttpRequest) | ✅ نعم، وشائع في التطبيقات الحديثة |
| Flash CSRF          | ملفات Flash (.swf)                   | ❌ لا، لأنه أصبح منتهي الدعم       |



<img width="1672" height="360" alt="image" src="https://github.com/user-attachments/assets/c9347206-8665-4a58-b73a-636fbd29b920" />


  
</details>




<details>
  <summary>Basic CSRF - Hidden Link/Image Exploitation</summary>


الفكرة الأساسية
===============

المهاجم **مش بيخترق البنك**.

هو بس **بيستغل إن الضحية عامل Login بالفعل**.

* * * * *

السيناريو
=========

الضحية (Josh)
-------------

Josh فاتح:

-   mailbox.thm
-   mybank.thm

وعامل Login على البنك.

يعني المتصفح عنده Session Cookie.

مثلاً

```
session=abc123
```

* * * * *

المهاجم
-------

المهاجم عنده حساب في نفس البنك.

دخل يشوف الموقع بيشتغل إزاي.

لقى إن تحويل الفلوس بيحتاج حاجتين بس:

```
to_accountamount
```

يعني الـ Request شكله كده:

```
POST /transfer.phpto_account=GB82MYBANK5698amount=1000
```

لاحظ...

**مفيش أي CSRF Token.**

ودي هي المشكلة.

* * * * *

المهاجم يعمل إيه؟
=================

يبعت إيميل:

> مبروك كسبت رحلة دبي ✈️

وفيه لينك:

```
<a href="http://mybank.thm:8080/dashboard.php?to_account=GB82MYBANK5698&amount=1000">Click Here</a>
```

* * * * *

الضحية يضغط
===========

أول ما Josh يضغط...

المتصفح يروح لـ

```
mybank.thm
```

ويبعت معاه تلقائياً

```
Cookie: session=abc123
```

لأن Josh عامل Login.

* * * * *

البنك يشوف:

```
الطلب جاي مع Session صحيحة
```

فيقول

> أكيد Josh هو اللي طلب تحويل الفلوس.

ويحول

1000$

لحساب المهاجم.

* * * * *

ليه الهجوم نجح؟
===============

لأن البنك كان بيتأكد من:

✅ المستخدم عامل Login.

لكن **مكانش بيتأكد إن الطلب نفسه جاي من صفحة البنك الحقيقية.**

* * * * *

إيه هو "Missing Link"؟
======================

في THM بيقول:

> What was the missing link?

الإجابة:

**السيرفر لم يتحقق من أن الطلب صادر من الصفحة الشرعية للموقع، ولم يستخدم CSRF Token.**

* * * * *

طيب هو CSRF Token بيحل المشكلة إزاي؟
====================================

بدل الفورم يبقى:

```
<input name="amount">
```

يبقى كمان فيه

```
<inputtype="hidden"name="csrf_token"value="A8h72KD9...">
```

يعني الفورم هيبقى فيه قيمة إضافية سرية.

* * * * *

لما المستخدم يدوس Submit

هيتبعت

```
amount=1000csrf_token=A8h72KD9...
```

* * * * *

السيرفر يقول

> هل الـ Token صح؟

لو

✅ صح

ينفذ العملية.

لو

❌ مش موجود

يرفض.

* * * * *

ليه المهاجم مش يضيف الـ Token؟
==============================

لأنه **مش بيعرف قيمته**.

الـ Token بيتولد لكل Session أو لكل طلب، وبيكون غير قابل للتخمين.

يعني المهاجم يقدر يعمل:

```
amount=1000
```

لكن ميقدرش يعرف:

```
csrf_token=A8h72KD9...
```

* * * * *

السيرفر يشوف

```
Token Missing
```

ويرفض الطلب.

* * * * *

ليه الـ Token بيتحط Hidden؟
===========================

عشان المستخدم مش محتاج يشوفه.

لكن المتصفح هيبعته مع الفورم.

يعني

```
<inputtype="hidden"name="csrf_token"value="...">
```

هيتبعت تلقائياً مع باقي البيانات.



----


1- add cookie value 

```
isBanned : False
```

2- login 

```
GB82MYBANK5698  : GB82MYBANK5698 
```

3- click on link on mail 




  
</details>





<details>
  <summary>Double Submit Cookie Bypass</summary>


أولاً: إيه هو Double Submit Cookie؟
===================================

بدل ما السيرفر يحتفظ بالـ Token عنده، بيعمل الآتي:

لما تعمل Login:

السيرفر يبعت Token مرتين:

1.  في Cookie

```
csrf-token = x8K92As...
```

1.  داخل الـ Form

```
<input type="hidden"       name="csrf_token"       value="x8K92As...">
```

* * * * *

لما تضغط Submit

المتصفح يبعت:

```
Cookie:csrf-token=x8K92As...
```

ومع الـ Form

```
csrf_token=x8K92As...
```

السيرفر يعمل مقارنة:

```
if(cookie == form)
```

لو الاتنين زي بعض

✅ ينفذ

لو مختلفين

❌ يرفض.

* * * * *

لحد هنا كل حاجة تمام.
---------------------

لكن...

* * * * *

فين الغلطة؟
===========

المبرمج عمل الـ Token بطريقة سيئة جدًا.

بدل ما يولد Token عشوائي.

عمله كده:

```
Token = Base64(Account Number)
```

يعني

رقم الحساب

```
GB82MYBANK5699
```

يبقى

```
R0I4Mk1ZQkFOSzU2OTk=
```

بس.

* * * * *

المهاجم عمل إيه؟
----------------

دخل على حسابه هو.

شاف الـ Cookie.

كانت مثلاً

```
R0I4Mk1ZQkFOSzU2OTc=
```

فتح موقع CyberChef.

عمل

```
Base64 Decode
```

طلعلوه

```
GB82MYBANK5697
```

يعني اكتشف إن

> الـ Token = مجرد Base64 لرقم الحساب.

يعني **سهل جدًا توقعه**.

* * * * *

طيب هيجيب Token بتاع Josh إزاي؟
===============================

المهاجم يعرف رقم حساب Josh.

يبقى يعمل

```
Base64 Encode
```

لرقم حساب Josh.

ويطلع Token الصح.

* * * * *

لكن لسه فيه مشكلة.
==================

السيرفر بيقارن

```
Cookie
```

مع

```
Form
```

يعني لازم يغير الـ Cookie كمان.

* * * * *

هنا بقى استخدم Subdomain Cookie Injection
=========================================

المهاجم عنده

```
attacker.mybank.thm
```

وده Subdomain.

يقدر يكتب

```
setcookie('csrf-token',base64_encode("GB82MYBANK5699"),['domain'=>'mybank.thm']);
```

يعني يقول للمتصفح:

> احفظ Cookie جديدة خاصة بـ

```
mybank.thm
```

* * * * *

المتصفح يسمع الكلام.

ويبقى عند Josh

```
csrf-token=Base64(Josh Account)
```

* * * * *

بعد كده
=======

المهاجم يبعت Form

وفيه

```
<inputname="csrf_token"value="Base64(Josh Account)">
```

يعني

الـ Cookie

=

الـ Hidden Field

* * * * *

السيرفر يعمل

```
if(base64_decode($_POST['csrf_token'])==base64_decode($_COOKIE['csrf-token']))
```

هيلاقيهم متساويين.

فيقول

✅ الطلب صحيح.

ويغير الباسورد.

* * * * *

المشكلة كانت فين؟
=================

السيرفر كان بيتأكد إن

```
Cookie == Form
```

لكن...

**مكانش بيتأكد إن الـ Token نفسه عشوائي وغير قابل للتخمين.**

وده خطأ في تصميم الحماية.

* * * * *

ليه CyberChef؟
==============

لأن الـ Token كان

```
Base64
```

وBase64 **مش تشفير (Encryption)**.

ده مجرد **Encoding**.

أي حد يقدر يفكه.





<img width="1796" height="710" alt="image" src="https://github.com/user-attachments/assets/279765bb-4cb4-49d7-b035-5c3b64dc8863" />



  
</details>



<details>
  <summary>Samesite Cookie Bypass</summary>


أولاً: يعني إيه SameSite Cookie؟
================================

لما السيرفر يعمل Cookie ممكن يحط لها Attribute اسمه:

```
SameSite
```

وده يقول للمتصفح:

> "ابعت الـ Cookie دي إمتى لو الطلب جاي من موقع تاني؟"

عندنا 3 حالات:

* * * * *

1) SameSite=Strict



ده أقوى وضع.

يعني:

لو أنت داخل:

```
mybank.thm
```

ورحت لينك من:

```
attacker.com
```

وطلب حاجة من البنك...

المتصفح يقول:

"لا، الـ Cookie مش هتتبعت."

يعني:

```
attacker.com
        |
        |
        X
        |
mybank.thm
```

الـ Cookie لا تخرج.

* * * * *

2) SameSite=Lax




ده اللي عندنا في السيناريو.

هو وسط بين الحماية والسهولة.

يسمح بالـ Cookie في:

✅ GET\
✅ فتح لينك عادي (Top-level navigation)

لكن يمنع:

❌ POST من موقع خارجي

مثال:

لو عندك:

```
<a href="https://mybank.thm/logout.php">Click</a>
```

ده GET.

Lax يسمح بإرسال الـ Cookie.

لكن:

```
<form method="POST">
```

لا.

* * * * *

3) SameSite=None


يعني:

"ابعت الـ Cookie دائمًا"

حتى مع مواقع خارجية.

وده لازم معه:

```
Secure
```

* * * * *

نخش على سيناريو THM
===================

المهاجم بالفعل قدر يدخل حساب Josh وغير الباسورد.

دلوقتي عايز يعمل حاجة تانية:

**يطلعه من الحساب (Logout).**

ليه؟

لأن Josh لو فضل داخل ممكن يعمل معاملات.

* * * * *

المهاجم لقى Cookie اسمها logout
-------------------------------

مثلاً:

```
logout=xxxxxxx
```

والسيرفر عنده:

```
if($_COOKIE["logout"]=="xxxxxxx"){ session_destroy();}
```

يعني لو الـ Cookie موجودة بالقيمة الصح:

يعمل Logout.

* * * * *

المشكلة:
========

المهاجم عايز يخلي Josh يزور:

```
mybank.thm/logout.php
```

من موقعه.

يعني CSRF.

* * * * *

السؤال:
-------

هل الـ Cookie هتتبعت؟

الإجابة:

حسب SameSite.

وهنا كانت:

```
SameSite=Lax
```

والطلب:

```
GET
```

فالمتصفح يقول:

تمام، ده Top-level navigation.

ويرسل:

```
Cookie: logout=xxxxxxx
```

* * * * *

الهجوم
======

المهاجم يبعت إيميل:

"شارك في استبيان واربح Ferrari"

وفيه:

```
<a href="https://mybank.thm/logout.php">Survey Link</a>
```

Josh يضغط.

يحصل:

```
Josh
 |
 | click
 ↓
logout.php
 |
 ↓
Browser sends logout cookie
 |
 ↓
Server destroys session
 |
 ↓
Josh logged out
```

* * * * *

ليه Strict كان هيمنع ده؟
========================

لو كانت:

```
SameSite=Strict
```

كان هيحصل:

```
attacker.com

   |
   |
   ↓

mybank.thm/logout.php
```

لكن:

```
Cookie ❌
```

مش هتتبعت.

فالسيرفر مش هيلاقي:

```
logout=xxxxxxx
```

والهجوم يفشل.

* * * * *

الجزء الثاني: Lax + POST
========================

هنا بقى حركة أذكى.

السؤال:

"طيب Lax يمنع POST، هل خلاص مفيش CSRF؟"

لا.

في Chrome فيه استثناء.

* * * * *

الاستثناء:
----------

لو Cookie اتعملت حديثًا (أقل من دقيقتين)

Chrome يسمح بإرسالها حتى مع:

```
POST
```

من موقع خارجي.

* * * * *

السيناريو
=========

عندنا Cookie:

```
isBanned=false
```

والهدف:

نخليها:

```
isBanned=true
```

* * * * *

فيه API:

```
POST /index.php
```

يستقبل:

```
isBanned=true
```

ويغير الـ Cookie.

* * * * *

المحاولة الأولى
---------------

المهاجم يعمل:

```
<form method="POST"><input name="isBanned" value="true"></form>
```

ويرسل.

لكن يفشل.

ليه؟

لأن:

```
SameSite=Lax
```

ومر وقت.

فالمتصفح لا يرسل:

```
isBanned cookie
```

والسيرفر يقول:

"مفيش Cookie"

ويرفض.

* * * * *

الحل اللي عمله المهاجم
======================

استغل نافذة الدقيقتين.

عمل Chain:

الخطوة 1
--------

يعمل Logout:

```
logout.php
```

ليه؟

لأن Logout يحدث Cookie جديدة.

* * * * *

الخطوة 2
--------

بعد ثانية يعمل POST:

```
index.php
```

ويغير:

```
isBanned=true
```

* * * * *

السبب:

Cookie اتعملت منذ ثانية فقط.

Chrome يقول:

"دي Cookie جديدة، هسمح بإرسالها."

* * * * *

النتيجة:

```
Logout
   |
   ↓
Cookie updated
   |
   ↓
POST خلال أقل من دقيقتين
   |
   ↓
Cookie sent
   |
   ↓
isBanned=true
```



  
</details>





<details>
  <summary>Few Additional Exploitation Techniques</summary>

أولًا: يعني إيه XMLHttpRequest؟
-------------------------------

`XMLHttpRequest` (أو XHR) هي طريقة في JavaScript تخلي الصفحة تبعت طلبات HTTP في الخلفية (AJAX) من غير ما تعمل Refresh للصفحة.

مثلاً بدل ما المستخدم يضغط Submit ويستنى الصفحة تحمل، JavaScript تبعت الطلب في الخلفية وتحدث البيانات.

مثال عادي:

```
var xhr = new XMLHttpRequest();
xhr.open("POST", "/updateprofile");xhr.send();
```

* * * * *

طيب إيه علاقة ده بـ CSRF؟
=========================

تخيل السيناريو ده:

1.  أنت عامل Login على موقع البنك.
2.  المتصفح مخزن Session Cookie بتاع البنك.
3.  فتحت موقع تاني خبيث.

لو الموقع الخبيث قدر يخلي متصفحك يبعت Request للبنك، المتصفح هيبعت الـ Cookies تلقائياً.

البنك هيقول:

> آه ده المستخدم الحقيقي.

مع إن المستخدم عمره ما ضغط على زر في البنك.

وده هو CSRF.

* * * * *

الكود الموجود
=============

```
var xhr = new XMLHttpRequest();
xhr.open('POST','http://mybank.thm/updatepassword',true);
```

هنا بيجهز Request نوعه POST.

الهدف:

```
/updatepassword
```

* * * * *

بعد كده

```
xhr.setRequestHeader("X-Requested-With","XMLHttpRequest");
```

بيضيف Header.

بعض المواقع تستخدمه علشان تعرف إن الطلب جاي من AJAX.

* * * * *

بعدها

```
xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded');
```

يعني البيانات هتتبعت بالشكل ده:

```
username=ahmed&password=123
```

* * * * *

بعدها

```
xhr.onreadystatechange = function() {
```

ده Event.

كل ما حالة الـ Request تتغير ينفذ الكود.

* * * * *

```
if(xhr.readyState === XMLHttpRequest.DONE&& xhr.status === 200)
```

يعني:

لو الطلب خلص

وكان الرد

```
200 OK
```

اعرض

```
alert("Action executed!");
```

* * * * *

آخر سطر

```
xhr.send('action=execute&parameter=value');
```

هنا البيانات اللي بتتبعت.

يعني كأنك بعت Form.

* * * * *

السؤال المهم
============

هل الكود ده هيشتغل من أي موقع؟

الإجابة:

**مش دائمًا.**

لأن المتصفح فيه حاجة اسمها

Same-Origin Policy (SOP)
------------------------

ودي من أهم وسائل الحماية.

* * * * *

يعني إيه SOP؟
-------------

لو الصفحة مفتوحة من

```
evil.com
```

متقدرش بسهولة تبعت AJAX لـ

```
mybank.thm
```

لأنهم Origin مختلف.

فالـ Browser هيمنع قراءة الرد، وغالبًا هيطبق قيود على الطلب حسب نوعه.

* * * * *

طيب هو ليه المقال بيتكلم عن CORS؟
=================================

لأن CORS هو الاستثناء.

يعني السيرفر يقول للمتصفح:

> أنا موافق إن مواقع تانية تبعتلي Requests.

مثلاً

```
Access-Control-Allow-Origin:*
```

يعني:

أي موقع في الدنيا مسموح.

ودي إعدادات ممكن تكون خطيرة حسب الاستخدام.

* * * * *

المثال
======

```
header('Access-Control-Allow-Origin: *');
```

يعني:

```
evil.com
```

مسموح.

```
google.com
```

مسموح.

```
facebook.com
```

مسموح.

أي Origin.

* * * * *

المشكلة
-------

لو الموقع بيقبل تعديل بيانات المستخدم

ومافيش CSRF Token

يبقى مهاجم ممكن يستغل الإعدادات دي في بعض السيناريوهات، خصوصًا لو فيه سوء إعدادات إضافية تتعلق بالاعتمادات (credentials).

* * * * *

ليه بيقول
=========

```
Access-Control-Allow-Origin: *
```

مع

```
Access-Control-Allow-Credentials: true
```

مينفعوش؟

لأن مواصفات CORS بتمنع ده.

لو سمحت بإرسال Cookies

لازم تحدد Origin معين.

مش ينفع تقول:

```
كل المواقع
```

وفي نفس الوقت:

```
ابعت Cookies.
```

وده هيبقى خطر جدًا.

* * * * *

Referer Header
==============

كل Request بيبقى فيه غالبًا Header اسمه

```
Referer
```

مثلاً

أنت كنت في

```
https://mybank.thm/profile
```

وروحت تبعت Request

هيبقى

```
Referer:https://mybank.thm/profile
```

* * * * *

بعض المواقع تعمل Check

لو الـ Referer مش من

```
mybank.thm
```

ارفض الطلب.

* * * * *

ليه دي حماية ضعيفة؟
-------------------

لأن:

-   بعض المتصفحات أو الإضافات (Extensions) قد تمنع إرسال الـ Referer.
-   أدوات الخصوصية قد تحذفه.
-   بعض إعدادات المواقع (`Referrer-Policy`) قد تقلل أو تمنع إرساله.

لذلك الاعتماد عليه وحده ليس حماية كافية ضد CSRF.

* * * * *

أفضل حماية من CSRF
==================

المقال بيقول في الآخر إن فيه Mitigation.

أهمها:

-   ✅ استخدام **CSRF Token** فريد لكل Session أو لكل طلب حساس.
-   ✅ التحقق من `Origin` أو `Referer` كطبقة إضافية، وليس الوحيدة.
-   ✅ استخدام `SameSite` على الـ Cookies (`Lax` أو `Strict`) لتقليل إرسالها في الطلبات عبر المواقع.
-   ✅ طلب إعادة إدخال كلمة المرور أو تأكيد إضافي قبل العمليات الحساسة (مثل تغيير كلمة المرور).




<img width="1620" height="478" alt="image" src="https://github.com/user-attachments/assets/3e4eef27-0b21-4bd8-a581-af7ac05efac8" />



  
</details>




<details>
  <summary>Defence Mechanisms</summary>




```
<inputtype="hidden"name="csrf_token"value="abc123xyz">
```

كل مستخدم ليه Token مختلف.

كل Session ليها Token مختلف.

كل Request لازم يبعت الـ Token.

السيرفر يعمل مقارنة.

لو غلط

```
403 Forbidden
```

* * * * *

2) SameSite Cookie
==================

الـ Cookie فيها Attribute اسمه

```
SameSite
```

قيمه ممكن تكون

```
Strict
```

أو

```
Lax
```

أو

```
None
```

* * * * *

### Strict

أقوى حماية.

الكوكيز متتبعتش نهائي مع أي Cross-Site Request.

يعني CSRF شبه مستحيل.

* * * * *

### Lax

أخف شوية.

بيسمح ببعض الـ GET Requests.

لكن يمنع أغلب POST.

وده الأكثر استخدامًا.

* * * * *

### None

يبعت Cookies لأي موقع.

ولازم يكون معاه

```
Secure
```

وده بيستخدم لو فيه حاجة محتاجة Cross-Origin.

* * * * *

3) Referrer Policy
==================

الموقع يحدد

إيه المعلومات اللي تتبعت في

```
Referer
```

مثلاً

```
strict-origin
```

أو

```
same-origin
```

وده يساعد يقلل المعلومات اللي تتسرب.

* * * * *

4) CSP
======

يعني

Content Security Policy

وده Header بيقول

مين مسموح يحمل JavaScript

مين مسموح يحمل CSS

مين مسموح يحمل Images

مثلاً

```
script-src 'self'
```

يعني

متنفذش JavaScript غير من موقعي.

وده بيقلل فرص استغلال XSS، وبالتالي يقلل فرص سرقة الـ CSRF Tokens أو حقن سكربتات خبيثة.

* * * * *

5) Double Submit Cookie
=======================

دي فكرة ذكية.

بدل Token واحد.

يبقى فيه

Cookie

مثلاً

```
csrf=abc123
```

وفي نفس الوقت

الفورم

```
csrf=abc123
```

السيرفر يعمل مقارنة.

لو الاتنين متساويين

```
OK
```

لو مختلفين

```
Reject
```

الفكرة إن المهاجم يقدر يخلي المتصفح يبعت الـ Cookie تلقائيًا، لكنه لا يستطيع عادةً معرفة قيمتها ليضعها في حقل الطلب، لذلك يفشل التحقق.

* * * * *

6) CAPTCHA
==========

قبل العمليات المهمة

زي

```
Change Password
```

أو

```
Delete Account
```

أو

```
Create Account
```

يطلب

```
I'm not a robot
```

وده يصعب تنفيذ الطلبات بشكل تلقائي، لكنه **ليس حماية أساسية ضد CSRF**؛ بل طبقة إضافية فقط.



| كـ Pentester              | كـ Secure Coder                            |
| ------------------------- | ------------------------------------------ |
| اختبر وجود CSRF           | استخدم CSRF Token                          |
| افحص Headers              | فعل SameSite للكوكيز                       |
| اختبر الـ Session         | استخدم CSP                                 |
| جرّب سيناريوهات مختلفة    | تحقق من `Origin` أو `Referer` كطبقة إضافية |
| اختبر التحقق من الـ Token | يمكن استخدام Double Submit Cookie          |
|                           | أضف CAPTCHA للعمليات الحساسة كطبقة إضافية  |










  
</details>




































