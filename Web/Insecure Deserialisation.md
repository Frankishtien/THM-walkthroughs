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






















