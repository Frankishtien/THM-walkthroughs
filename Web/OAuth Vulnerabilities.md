# OAuth Vulnerabilities



<img width="1901" height="356" alt="image" src="https://github.com/user-attachments/assets/d1148a06-f9ce-4619-bba5-5e426fdeab7c" />




---

<details>
  <summary>Key Concepts</summary>


🎯 الفكرة الأساسية من OAuth 2.0
-------------------------------

OAuth مش نظام تسجيل دخول...\
هو **نظام تفويض (Authorization)**.

يعني:

> تطبيق ياخد إذن يوصل لبياناتك... من غير ما يعرف الباسورد بتاعك.

زي لما موقع يقولك:

> "Login with Google"

هو مش بياخد باسوردك...\
هو بياخد إذن من Google.

* * * * *

👑 الأطراف الأساسية في OAuth
============================

* * * * *

1️⃣ Resource Owner (صاحب البيانات)
----------------------------------

ده المستخدم نفسه.

📌 في المثال:\
إنت اللي عندك حساب في تطبيق القهوة.

📌 في الواقع:\
اليوزر اللي عنده حساب Google أو Facebook.

هو صاحب القرار:\
يسمح للتطبيق يوصل لبياناته أو لأ.

* * * * *

2️⃣ Client (التطبيق)
--------------------

هو التطبيق اللي عايز يوصل للبيانات.

📌 مثال:\
تطبيق القهوة\
أو موقع بيسمحلك تعمل Login بـ Google.

هو بيقول:

> "لو سمحت يا Authorization Server، إديني صلاحية أوصل لبيانات اليوزر."

* * * * *

3️⃣ Authorization Server
------------------------

ده السيرفر المسؤول عن:

-   التحقق من هوية المستخدم

-   إصدار التوكنات

📌 مثال:\
سيرفر Google لما تعمل Login with Google.

هو اللي:

-   يخليك تدخل باسورد

-   يسألك توافق على الصلاحيات

-   يدي التطبيق Access Token

⚠️ هنا نقطة مهمة للـ Pentesting:\
أي ضعف هنا = كارثة.

* * * * *

4️⃣ Resource Server
-------------------

ده السيرفر اللي فيه البيانات المحمية.

📌 مثال:\
API بتاع Google\
أو قاعدة بيانات القهوة.

هو بيقول:

> "هاتلي Access Token صح... أديك البيانات."

* * * * *

🔑 أهم المفاهيم التقنية
=======================

* * * * *

🎫 Authorization Grant
----------------------

ده الإثبات إن المستخدم وافق.

أشهر نوع:

### Authorization Code (الأكثر أماناً)

الفلُو بيبقى:

1.  اليوزر يعمل Login

2.  ياخد Authorization Code

3.  التطبيق يبدله بـ Access Token

* * * * *

🔐 Access Token
---------------

ده المفتاح المؤقت.

-   ليه صلاحيات محددة (Scope)

-   ليه مدة انتهاء

بيتبعت مع كل API request:

Authorization: Bearer eyJhbGciOi...

لو اتسرق = المهاجم يقدر يدخل كأنه المستخدم.

* * * * *

🔄 Refresh Token
----------------

لما الـ Access Token يخلص،\
التطبيق يستخدم Refresh Token عشان يجيب واحد جديد.

⚠️ لو ده اتسرق → Session hijacking محترم.

* * * * *

🔁 Redirect URI
---------------

دي نقطة خطيرة جدًا أمنيًا.

بعد ما تعمل Login،\
السيرفر بيرجعك للـ Client على URI معين.

لو حصل:

-   Open Redirect

-   أو misconfigured redirect

المهاجم ممكن يسرق Authorization Code.

ودي من أشهر ثغرات OAuth.

* * * * *

🎯 Scope
--------

بيحدد:\
التطبيق مسموح له يعمل إيه؟

مثلاً:

-   read profile

-   read email

-   modify data

⚠️ لو التطبيق طلب scope زيادة عن اللزوم\
→ Violation لمبدأ Least Privilege

* * * * *

🛡 State Parameter
------------------

ده Anti-CSRF protection.

الـ Client يبعت قيمة عشوائية:

state=ajsd8293js

ولما السيرفر يرجع الرد،\
لازم يرجع بنفس القيمة.

لو مفيش State أو مش بيتتحقق منه:\
→ OAuth CSRF Attack

* * * * *

🔄 الفرق بين Authorization Endpoint و Token Endpoint
====================================================

### Authorization Endpoint

المستخدم بيتسجل دخول فيه.

### Token Endpoint

التطبيق يبدل فيه:\
Authorization Code → Access Token

* * * * *

🧠 الفكرة الكبيرة اللي لازم تستوعبها كـ Pentester
=================================================

OAuth = سلسلة خطوات

لو كسرت خطوة واحدة:

-   سرقت Code

-   لعبت في Redirect

-   غيرت Scope

-   استغليت ضعف في State

-   سرقت Access Token

يبقى كأنك سيطرت على حساب المستخدم بدون باسورد.

* * * * *

🔥 أشهر ثغرات OAuth
===================

-   Missing state validation

-   Open Redirect in redirect_uri

-   Code leakage via referer

-   Access token in URL fragment

-   Improper scope validation

-   Account takeover via misbinding



<img width="1564" height="315" alt="image" src="https://github.com/user-attachments/assets/57a9c8f4-2460-48e6-9170-16c45d032502" />




  
</details>





<details>
  <summary>OAuth Grant Types</summary>



🥇 1️⃣ Authorization Code Grant (الأهم والأقوى)
===============================================

ده الـ Flow الأساسي والأكثر أمانًا.

### 💡 بيستخدم في:

-   Server-side apps (PHP, Java, .NET)

-   Login with Google / Facebook

* * * * *

🔄 الفلو بيحصل إزاي؟
--------------------

1.  اليوزر يضغط Login

2.  التطبيق يحوله لـ Authorization Server

3.  اليوزر يعمل Login ويوافق

4.  السيرفر يرجع بـ Authorization Code

5.  التطبيق (السيرفر بتاعه) يبدل الـ Code بـ Access Token (Server-to-Server)

* * * * *

🔐 ليه آمن؟
-----------

لأن:

-   الـ Access Token مش بيعدي على المتصفح

-   التبادل بيحصل بين سيرفرين

-   يقدر يستخدم Refresh Token

* * * * *

🎯 كـ Pentester تركز على إيه؟
-----------------------------

-   هل فيه `state`؟

-   هل `redirect_uri` بيتحقق منه؟

-   هل ممكن أسرق الـ authorization code؟

-   هل فيه code reuse؟

-   هل فيه OAuth misbinding؟





<img width="1158" height="813" alt="image" src="https://github.com/user-attachments/assets/714b1d22-149c-4a7d-966c-f63e75e1cc60" />



* * * * *

🥈 2️⃣ Implicit Grant (قديم وأضعف)
==================================

ده كان معمول للتطبيقات اللي مش تقدر تخزن Secret بأمان (SPA, JS apps).

* * * * *

🔄 الفلو
--------

1.  اليوزر يتحول لـ Authorization Server

2.  يعمل Login

3.  السيرفر يرجع Access Token مباشرة في URL Fragment

مثال:

https://client.com/callback#access_token=eyJhbGciOi...

* * * * *

❌ المشكلة هنا؟
--------------

-   التوكن بيعدي على المتصفح

-   ممكن يتحفظ في:

    -   Browser history

    -   Logs

    -   Referer header

-   مفيش Refresh Token

* * * * *

🎯 كـ Pentester
---------------

تركز على:

-   Token leakage via referer

-   XSS + token theft

-   Browser history exposure

-   Token interception

⚠️ عشان كده الـ Implicit Flow بقى deprecated تقريبًا.




<img width="1160" height="707" alt="image" src="https://github.com/user-attachments/assets/b9947d4e-9921-4893-9a8b-a91cd36afd9e" />




* * * * *

🥉 3️⃣ Resource Owner Password Credentials (ROPC)
=================================================

ده أخطر Flow.

* * * * *

💡 الفكرة
---------

التطبيق يقول للمستخدم:

> إديني اليوزرنيم والباسورد وأنا أجيبلك توكن.

يعني:\
اليوزر بيدّي الباسورد للتطبيق نفسه.

* * * * *

🔄 الفلو
--------

1.  اليوزر يدخل username/password في التطبيق

2.  التطبيق يبعتهُم لـ Authorization Server

3.  السيرفر يتحقق ويرجع Access Token

* * * * *

❌ ليه خطر؟
----------

-   التطبيق بيشوف الباسورد

-   مناسب بس لـ First-party apps

-   مش مناسب Third-party إطلاقًا

* * * * *

🎯 كـ Pentester
---------------

تركز على:

-   هل الباسورد بيتسجل في logs؟

-   هل فيه rate limit؟

-   هل فيه MFA؟

-   هل ممكن brute-force؟




<img width="1187" height="698" alt="image" src="https://github.com/user-attachments/assets/0b777458-cde4-4d47-a41a-1e265824e2f8" />



* * * * *

🏢 4️⃣ Client Credentials Grant (Server-to-Server)
==================================================

ده مفيهوش مستخدم خالص.

* * * * *

💡 الفكرة
---------

سيرفر عايز يكلم API سيرفر تاني.

* * * * *

🔄 الفلو
--------

1.  Client يبعث:

    -   client_id

    -   client_secret

2.  Authorization Server يرجع Access Token

3.  يستخدمه في API calls

* * * * *

🎯 مثال
-------

-   Microservices

-   Background jobs

-   Internal APIs

* * * * *

🎯 كـ Pentester
---------------

تركز على:

-   هل الـ client_secret مكشوف؟

-   هل فيه hardcoded secrets؟

-   هل scope مضبوط؟

-   هل token lifespan طويل زيادة؟


<img width="997" height="777" alt="image" src="https://github.com/user-attachments/assets/52f71bc0-88f8-438c-b8fc-66919af316db" />




* * * * *

🧠 مقارنة سريعة بين الأربعة
===========================

| Grant Type | آمن؟ | فيه User؟ | فيه Refresh Token؟ | استخدامه |
| --- | --- | --- | --- | --- |
| Authorization Code | ✅ قوي جدًا | نعم | نعم | Web apps |
| Implicit | ❌ أضعف | نعم | لا | SPA قديم |
| ROPC | ❌ خطر | نعم | أحيانًا | First-party |
| Client Credentials | ✅ | لا | لا غالبًا | Server-to-server |


---



<img width="1566" height="213" alt="image" src="https://github.com/user-attachments/assets/20d64c98-19b1-479e-b73a-78a44eb77539" />





  
</details>













<details>
  <summary>How OAuth Flow Works</summary>



🎬 المشهد العام
===============

عندنا:

-   🌐 `bistro.thm` → ده **Client**

-   ☕ `coffee.thm` → ده **OAuth Provider**

-   👤 Tom → Resource Owner

هدف Bistro:

> يدخل Tom بحسابه في CoffeeShopApp من غير ما يعرف باسورده.

* * * * *

🥇 المرحلة 1: Authorization Request
===================================

Tom يدخل هنا:

http://bistro.thm:8000/oauthdemo

يضغط:

> Login with OAuth

Bistro يعمل Redirect للـ Authorization Server:

http://coffee.thm:8000/o/authorize/?\
client_id=xxx\
&response_type=code\
&redirect_uri=http://bistro.thm:8000/oauthdemo/callback\
&state=xyz123

* * * * *

📌 معنى كل باراميتر
-------------------

| Parameter | معناه |
| --- | --- |
| response_type=code | عايزين Authorization Code |
| client_id | هوية التطبيق |
| redirect_uri | يرجع المستخدم فين |
| state | حماية ضد CSRF |
| scope | الصلاحيات المطلوبة |

* * * * *

🎯 كـ Pentester تركز على:
-------------------------

-   هل redirect_uri بيتحقق منه؟

-   هل state موجود؟

-   هل scope قابل للتلاعب؟

* * * * *

🥈 المرحلة 2: Authentication & Consent
======================================

Tom يوصل لـ coffee.thm

1️⃣ يدخل username/password\
2️⃣ يشوف شاشة الموافقة\
3️⃣ يوافق

* * * * *

🔐 هنا بيحصل حاجتين مهمين:
--------------------------

-   Authentication (التحقق من الهوية)

-   Authorization (الموافقة على الصلاحيات)

لو Tom رفض → العملية تقف.

* * * * *

🥉 المرحلة 3: Authorization Response
====================================

لو وافق، السيرفر يرجعه لـ:

https://bistro.thm:8000/callback?code=AuthCode123&state=xyz123

لاحظ:

-   رجع `code`

-   رجع نفس `state`

* * * * *

🎯 كـ Pentester
---------------

دي أهم لحظة في الفلو 👀

اسأل نفسك:

-   هل أقدر أغير redirect_uri؟

-   هل أقدر أسرق الـ code؟

-   هل الـ state بيتتحقق منه فعلًا؟

-   هل ممكن reuse للـ code؟

لو سرقت الـ Code قبل ما يتحول لـ Token → ممكن تاخد التوكن لنفسك 😈

* * * * *

🏃 المرحلة 4: Token Request (Server-to-Server)
==============================================

هنا بقى الحتة الآمنة 🔐

Bistro يعمل POST request للـ Token Endpoint:

POST /o/token/

ويرسل:

grant_type=authorization_code\
code=AuthCode123\
redirect_uri=http://bistro.thm:8000/oauthdemo/callback\
client_id=xxx\
client_secret=yyy

وبيستخدم:

Authorization: Basic base64(client_id:client_secret)

* * * * *

💡 مهم جدًا
-----------

الـ client_secret عمره ما يظهر في المتصفح.

ده بيحصل بين السيرفرين بس.

* * * * *

🎯 كـ Pentester
---------------

تركز على:

-   هل client_secret مكشوف في JS؟

-   هل redirect_uri بيتحقق منه مرة تانية؟

-   هل code مرتبط بالـ client_id؟

-   هل ممكن أستخدم code صادر لتطبيق آخر؟

* * * * *

🏅 المرحلة 5: Token Response
============================

لو كل حاجة صح، السيرفر يرد بـ JSON:

{\
  "access_token": "eyJhbGciOi...",\
  "token_type": "Bearer",\
  "expires_in": 3600,\
  "refresh_token": "abc123"\
}

* * * * *

📌 معنى كل حاجة
---------------

| Field | معناه |
| --- | --- |
| access_token | المفتاح اللي يسمح بالدخول |
| token_type | غالبًا Bearer |
| expires_in | مدة الصلاحية |
| refresh_token | لتجديد التوكن |

* * * * *

🏁 المرحلة 6: استخدام التوكن
============================

دلوقتي Bistro يقدر يعمل request للـ Resource Server:

Authorization: Bearer eyJhbGciOi...

ويجيب بيانات Tom.

* * * * *

🧠 الصورة الكاملة
=================

1️⃣ User يضغط Login\
2️⃣ Redirect لـ OAuth Provider\
3️⃣ Login + Consent\
4️⃣ يرجع Code\
5️⃣ Client يحوله لـ Access Token\
6️⃣ يستخدم التوكن للوصول للبيانات

* * * * *

🔥 فين نقاط الهجوم؟
===================

كـ Pentester محترم تركز على:

### 🎯 1. Redirect URI Manipulation

لو مش strict matching\
→ Code interception

* * * * *

### 🎯 2. Missing State Validation

→ OAuth CSRF

* * * * *

### 🎯 3. Code Leakage

-   Referer header

-   Logs

-   Open redirect

* * * * *

### 🎯 4. Client Secret Exposure

-   Hardcoded

-   Public repo

-   JS files

* * * * *

### 🎯 5. Scope Escalation

هل أقدر أطلب صلاحيات أعلى؟

* * * * *

💀 الخلاصة الأمنية
==================

Authorization Code Flow آمن\
بس بشرط:

-   تحقق صارم من redirect_uri

-   استخدام state

-   ربط code بالـ client_id

-   منع إعادة استخدام code

-   حماية client_secret













<img width="1695" height="697" alt="image" src="https://github.com/user-attachments/assets/bbb051c3-999b-4fa6-bf95-0037d297aff0" />

<img width="1376" height="707" alt="image" src="https://github.com/user-attachments/assets/e03b979f-0ea7-48e3-94a6-b22da6399d4f" />

```url
http://coffee.thm:8000/accounts/login/?next=/o/authorize/?
client_id=zlurq9lseKqvHabNqOc2DkjChC000QJPQ0JvNoBt
&response_type=code
&redirect_uri=http://bistro.thm:8000/oauthdemo/callback
```





















  
</details>










<details>
  <summary>Identifying the OAuth Services</summary>


🥇 أول علامة: شاشة تسجيل الدخول
===============================

👀 بص على صفحة الـ Login
------------------------

لو لقيت أزرار زي:

-   Login with Google

-   Login with Facebook

-   Login with GitHub

🚨 ده مؤشر قوي إن OAuth مستخدم.

لأن ده معناه إن الموقع مش بيتحقق من الباسورد بنفسه...\
هو بيفوض جهة تانية تعمل Authentication.

* * * * *

🥈 تحليل الـ Network Traffic
============================

افتح DevTools → Network\
أو استخدم Burp Suite 🔥

لما تضغط Login with Google مثلاً، المفروض تشوف:

### 🔁 Redirect (HTTP 302)

المتصفح يتحول لعنوان فيه باراميترات شبه كده:

https://provider.com/authorize?\
response_type=code\
&client_id=abc123\
&redirect_uri=https://target.com/callback\
&scope=profile\
&state=xyzSecure123

* * * * *

📌 وجود الباراميترات دي = OAuth 100%
------------------------------------

| Parameter | دليل على OAuth |
| --- | --- |
| response_type | نوع الـ Flow |
| client_id | تعريف التطبيق |
| redirect_uri | هيرجع فين |
| scope | الصلاحيات |
| state | CSRF protection |

لو شفتهم... يبقى دخلت في OAuth Flow رسميًا 👌

* * * * *

🥉 طب أعرف Framework إزاي؟
==========================

دي بقى النقطة اللي تفرق Pentester عادي عن تقيل 😏

* * * * *

🧠 1️⃣ من شكل الـ Endpoints
===========================

كل Framework ليه Pattern معين.

### مثال:

### Django OAuth Toolkit

غالبًا يستخدم:

/o/authorize/\
/o/token/

أو أحيانًا:

/oauth/authorize/\
/oauth/token/

* * * * *

### Spring Security OAuth (Java)

غالبًا:

/oauth/authorize\
/oauth/token

* * * * *

### Node.js Passport

غالبًا تشوف:

/auth/google\
/auth/google/callback

* * * * *

لو عرفت الـ pattern → تقدر تبحث عن ثغرات معروفة للـ framework ده 🔥

* * * * *

🧠 2️⃣ من الـ HTTP Headers
==========================

بص على:

-   Server header

-   X-Powered-By

-   WWW-Authenticate

-   Error responses

مثال:

WWW-Authenticate: Bearer realm="oauthlib"

كلمة `oauthlib` دي دليل 👀

* * * * *

🧠 3️⃣ من Error Messages
========================

دي كنز حقيقي 💀

جرب تبعت:

-   redirect_uri غلط

-   client_id غلط

-   scope مش مسموح

وشوف الرد.

لو الرد فيه:

-   django

-   spring

-   oauthlib

-   passport

-   grant_type invalid

ده بيساعدك تحدد المكتبة.

* * * * *

🧠 4️⃣ من الـ Source Code (لو متاح)
===================================

لو التطبيق Open Source\
أو عندك access للـ code

دور على:

django-oauth-toolkit\
oauthlib\
spring-security-oauth\
passport-google-oauth20

* * * * *

🧠 5️⃣ من طريقة معالجة التوكن
=============================

بعض الفريموركات:

-   بترجع JSON معين

-   صيغة error معينة

-   structure مميز للـ access_token

مثال:

Django OAuth Toolkit بيرجع:

{\
  "access_token": "...",\
  "expires_in": 36000,\
  "token_type": "Bearer",\
  "scope": "read write"\
}

تفاصيل بسيطة زي دي تفرق.

* * * * *

🎯 ليه تحديد الـ Framework مهم؟
===============================

لأن كل Framework ليه:

-   Misconfiguration شائعة

-   CVEs سابقة

-   طريقة تحقق مختلفة من redirect_uri

-   Handling مختلف للـ state

يعني بدل ما تجرب blind exploitation\
تبدأ تستهدف framework معروف.






<img width="592" height="312" alt="image" src="https://github.com/user-attachments/assets/cad3f5fd-cf9e-4d34-8ea9-0c4dc5852388" />








  
</details>





<details>
  <summary>Exploiting OAuth - Stealing OAuth Token</summary>


👀

* * * * *

🧠 الأول نفهم الفكرة الأساسية
=============================

في Authorization Code Flow:

1.  المستخدم يعمل Login

2.  السيرفر يرجع:

https://client.com/callback?code=AUTH_CODE

الـ **Authorization Code** ده هو مفتاح مؤقت\
اللي يملكه يقدر يحوله لـ Access Token.

* * * * *

🎯 دور الـ redirect_uri
=======================

الـ redirect_uri هو:

> العنوان اللي Authorization Server هيرجع عليه بالكود

السيرفر لازم يتحقق إن الـ redirect_uri:

-   متسجل مسبقًا

-   مطابق حرفيًا للقيمة المخزنة

لو التحقق مش صارم ❌\
هنا تبدأ المصيبة.

* * * * *

💀 السيناريو الهجومي
====================

التطبيق مسجل كذا redirect_uri:

-   `http://bistro.thm/callback`

-   `http://dev.bistro.thm/callback`

المهاجم سيطر على:

dev.bistro.thm

وده Subdomain takeover أو اختراق سيرفر تجريبي.

* * * * *

🥷 خطة المهاجم
==============

1️⃣ يجهز صفحة خبيثة
-------------------

<form action="http://coffee.thm:8000/oauthdemo/oauth_login/" method="get">\
    <input type="hidden" name="redirect_uri"\
           value="http://dev.bistro.thm:8002/malicious_redirect.html">\
    <input type="submit" value="Hijack OAuth">\
</form>

هو هنا بيعمل إيه؟

بيبدأ OAuth Flow\
لكن بيحقن redirect_uri بتاعه.

* * * * *

2️⃣ الضحية يضغط اللينك
----------------------

Tom يفتح:

http://dev.bistro.thm:8002/redirect_uri.html

يضغط Login via OAuth\
يسجل دخول بـ:

victim : victim123

* * * * *

3️⃣ الكارثة بتحصل هنا 💀
------------------------

Authorization Server يرجع:

http://dev.bistro.thm:8002/malicious_redirect.html?code=ABC123

الكود راح لمين؟\
راح لدومين المهاجم.

* * * * *

🧠 كيف المهاجم يسرق الكود؟
==========================

باستخدام JavaScript:

const urlParams = new URLSearchParams(window.location.search);\
const code = urlParams.get('code');\
console.log("Intercepted Authorization Code:", code);

المهاجم:

-   يحفظ الكود في DB

-   يبعته لسيرفره

-   يعمله log

والضحية مش حاسس بحاجة\
لأن ممكن يحصل Redirect سريع بعدها.

* * * * *

⚔️ المرحلة الأخطر
=================

المهاجم معاه Authorization Code.

يعمل إيه؟

يروح:

http://bistro.thm:8000/oauthdemo/callbackforflag/?code=ABC123

السيرفر هناك:

-   يبدل الكود بـ Access Token

-   يديه جلسة دخول

🔥🔥🔥\
كده حصل Account Takeover كامل\
من غير ما يعرف باسورد الضحية.

* * * * *

🎯 ليه الثغرة حصلت؟
===================

بسبب:

1️⃣ وجود أكثر من redirect_uri\
2️⃣ عدم التأكد الصارم من النطاق\
3️⃣ إمكانية التحكم في subdomain مسجل مسبقًا

* * * * *

🧨 أنواع أخطاء redirect_uri الشائعة
===================================

### ❌ 1. Partial Matching

يسمح بـ:

bistro.thm.attacker.com

* * * * *

### ❌ 2. Wildcards

يسمح بـ:

*.bistro.thm

* * * * *

### ❌ 3. Subdomain Takeover

تسجيل subdomain مهجور.

* * * * *

### ❌ 4. Open Redirect chaining

redirect_uri صحيح\
لكن الصفحة نفسها فيها Open Redirect.

* * * * *

🧠 كـ Pentester تعمل إيه؟
=========================

Checklist احترافية 👇
---------------------

-   هل redirect_uri لازم يطابق حرفيًا؟

-   هل فيه wildcard؟

-   هل فيه subdomains مهجورة؟

-   هل ممكن أضيف باراميتر إضافي؟

-   هل فيه open redirect في الموقع؟

* * * * *

🔥 ليه Authorization Code خطير؟
===============================

لأنه:

-   Single-use

-   قصير العمر

-   لكنه كافي لتحويله لـ Access Token

لو سرقته في اللحظة الصح\
تكسب الحساب.





----

<img width="1136" height="687" alt="image" src="https://github.com/user-attachments/assets/208c3dc9-5e1e-4df2-8c44-bbcd61cabfdb" />

```
 http://bistro.thm:8000/oauthdemo/callbackforflag/?code=tqAjWT4AiQdVoM8skkhzR1DIevjomR
```

<img width="1327" height="367" alt="image" src="https://github.com/user-attachments/assets/b78903a8-67e0-4e6b-a466-053d449d3dfd" />









  
</details>




<details>
  <summary></summary>
</details>





































