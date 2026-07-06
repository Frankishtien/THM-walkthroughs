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






  
</details>

























































