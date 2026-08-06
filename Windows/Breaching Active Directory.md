# Breaching Active Directory

<img width="1831" height="377" alt="image" src="https://github.com/user-attachments/assets/cec3bbfc-4fc0-4d49-a397-6dfccd744b68" />

----


<img width="1620" height="671" alt="image" src="https://github.com/user-attachments/assets/d5105048-002b-457f-9e8b-7c8e184286d4" />

---



<details>
  <summary>Introduction to AD Breaches</summary>

ليه الـ Active Directory مهم؟
=============================

تخيل شركة فيها:

-   500 موظف
-   1000 جهاز
-   50 سيرفر

هل الأدمن هيعمل User على كل جهاز لوحده؟

طبعًا لا.

هنا بيجي دور **Active Directory (AD)**.

الـ AD عبارة عن نظام مركزي لإدارة:

-   المستخدمين Users
-   الأجهزة Computers
-   الجروبات Groups
-   الصلاحيات Permissions
-   السياسات Policies

يعني بدل ما كل جهاز يكون لوحده، كله بيتجمع تحت إدارة مركزية.

* * * * *

ليه المهاجمين بيحبوا AD؟
========================

لأن الـ AD ماسك كل حاجة تقريبًا.

لو خدت صلاحيات عالية جوه الـ AD:

-   تقدر تدخل أي جهاز
-   تقدر تعمل Users جديدة
-   تقدر تغير Passwords
-   تقدر تسيطر على الدومين كله

عشان كده بيقولوا:

> Active Directory = Keys To The Kingdom

* * * * *

هدف الروم ده؟
=============

قبل ما تعمل:

-   Privilege Escalation
-   Lateral Movement
-   Domain Admin

لازم الأول تدخل الشبكة.

يعني لازم تحصل على:

```
username + password
```

أو أي Credential صالح.

* * * * *

يعني ايه Breaching AD؟
======================

كلمة Breach معناها:

> الحصول على أول نقطة دخول.

يعني أول Account صالح في الدومين.

حتى لو كان User عادي جدًا.

مثال:

```
ahmed
Password123
```

مش مهم يكون Admin.

المهم نقدر نعمل Authentication.

* * * * *

ليه User عادي كفاية؟
====================

لأن بمجرد ما يبقى عندك حساب:

تقدر تعمل Enumeration.

يعني تبدأ تجمع معلومات عن:

-   Users
-   Groups
-   Computers
-   Shares
-   Policies

ومن هنا تبدأ رحلة التصعيد.

* * * * *

الروم هيعلمك إيه؟
=================

هيعلمك طرق سرقة أو استخراج Credentials.

* * * * *

1) NTLM Authenticated Services
------------------------------

هنشوف خدمات تستخدم NTLM.

NTLM بروتوكول Authentication قديم من مايكروسوفت.

مثال:

```
SMB
HTTP
MSSQL
```

لو المستخدم دخل Password عند خدمة معينة ممكن نلتقط الـ Hash.

* * * * *

2) LDAP Bind Credentials
------------------------

LDAP هو البروتوكول اللي بنتكلم بيه مع الـ AD.

تخيل إن الـ AD قاعدة بيانات ضخمة.

الـ LDAP هو اللغة اللي بنسأل بيها.

مثال:

```
هاتلي المستخدمين
هاتلي الجروبات
هاتلي الأجهزة
```

أحيانًا Credentials بتبقى مخزنة غلط ونقدر نستخرجها.

* * * * *

3) Authentication Relays
------------------------

من أشهر هجمات الـ AD.

الفكرة:

بدل ما أسرق Password.

أخلي الضحية تعمل Authentication عندي.

وأعيد توجيه Authentication لسيرفر تاني.

* * * * *

مثال بسيط:

```
Victim ---> Me ---> Server
```

أنا وسيط.

والسيرفر يفتكر إني الضحية.

* * * * *

4) Microsoft Deployment Toolkit (MDT)
-------------------------------------

أداة تستخدمها الشركات لتثبيت ويندوز على أجهزة كثيرة.

بدل ما الأدمن يثبت ويندوز 500 مرة.

بيجهز Image واحدة.

والأجهزة تسحبها.

* * * * *

المشكلة؟

أحيانًا Credentials الأدمن بتبقى متخزنة فيها.

* * * * *

5) Configuration Files
----------------------

ملفات إعدادات.

زي:

```
.xml
.ini
.txt
.ps1
```

كتير من الأدمنز للأسف يسيبوا فيها:

```
username=admin
password=P@ssword123
```

وده كنز للمهاجم.

* * * * *

الجزء الخاص بالشبكة
===================

الروم جهزلك لاب كامل.

فيه:

```
THMDC
```

وده الـ Domain Controller.

* * * * *

Domain Controller يعني ايه؟
===========================

هو السيرفر المسؤول عن:

-   Authentication
-   DNS
-   Users
-   Groups

يعني قلب الشبكة.

* * * * *

ليه محتاج DNS؟
==============

دي نقطة مهمة جدًا.

* * * * *

في الشبكات العادية
------------------

أنت بتكتب:

```
google.com
```

الـ DNS يحولها لـ:

```
142.250.x.x
```

* * * * *

في الـ AD
---------

الأجهزة لا تعتمد على IP غالبًا.

بتستخدم أسماء.

مثال:

```
THMDC.za.tryhackme.com
```

بدل:

```
10.x.x.x
```

* * * * *

ليه Kerberos محتاج DNS؟
=======================

Kerberos بيقول:

```
أنا عايز Ticket لـ THMDC.za.tryhackme.com
```

مش:

```
10.10.10.10
```

عشان كده لو DNS بايظ:

Kerberos يفشل.

وأغلب أدوات الـ AD تفشل.

* * * * *

ليه كاتب:
=========

```
nslookup thmdc.za.tryhackme.com
```

عشان تتأكد إن:

```
اسم السيرفر
↓
تم تحويله لـ IP
```

بنجاح.

* * * * *

أهم درس في الصفحة كلها
======================

لو AD مش شغالة:

**أول سؤال تسأله لنفسك:**

```
هل DNS شغال؟
```

مش:

```
هل AD بايظة؟
```

ولا:

```
هل اللاب فيه مشكلة؟
```

* * * * *

خطوات التشخيص الصحيحة
=====================

### 1

```
ping <DC-IP>
```

لو مردش:

الشبكة نفسها فيها مشكلة.

* * * * *

### 2

```
nslookup za.tryhackme.com <DC-IP>
```

لو فشل:

DNS على الـ DC فيه مشكلة.

* * * * *

### 3

```
nslookup thmdc.za.tryhackme.com
```

لو فشل:

DNS عندك أنت متظبطش.

  
</details>



<details>
  <summary>practical</summary>



## after start the machine wait for 5 min

```
sed -i '1s|^|nameserver 10.200.70.101\n|' /etc/resolv-dnsmasq
```


<details>
  <summary>why</summary>


### لماذا استخدمنا DNS الخاص بالـ Domain Controller بدل `/etc/hosts`؟

يمكن إضافة:

```text
10.200.70.101 thmdc.za.tryhackme.com
```

إلى ملف `/etc/hosts`، وهذا يكفي لتحويل اسم السيرفر إلى IP.

لكن في بيئة **Active Directory** لا نحتاج فقط إلى معرفة الـ IP، بل نحتاج أيضًا إلى معلومات خدمات مثل:

* LDAP
* Kerberos
* Global Catalog

هذه المعلومات يتم الحصول عليها من **DNS Records** خاصة (مثل SRV Records)، وهي غير مدعومة في `/etc/hosts`.

لذلك قمنا بإضافة:

```bash
nameserver 10.200.70.101
```

حتى يستخدم الجهاز DNS الخاص بالـ Domain Controller، ويستطيع العثور على جميع خدمات الـ AD تلقائيًا كما يحدث في بيئات Active Directory الحقيقية.

**الخلاصة:**

```text
/etc/hosts
= Name → IP فقط

AD DNS
= Name → IP + Kerberos + LDAP + خدمات الدومين الأخرى
```

  
</details>


```
nslookup thmdc.za.tryhackme.com
```

<img width="971" height="220" alt="image" src="https://github.com/user-attachments/assets/2abd4f61-3762-4b5c-8e8b-f350fa597252" />


## now our network ready to work on it



  
</details>


----

<details>
  <summary>OSINT and Phishing</summary>


الجزء ده بيتكلم عن **أشهر طريقتين للحصول على أول Credentials في الـ Active Directory** قبل ما تبدأ أي Enumeration أو Privilege Escalation.

الفكرة كلها:

> عندك شبكة ضخمة، لكن أنت لسه معندكش Username أو Password واحد صالح للدخول.

فإزاي نجيب أول Account؟

* * * * *

الطريقة الأولى: OSINT
=====================

OSINT اختصار:

```
Open Source Intelligence
```

يعني جمع معلومات من مصادر متاحة للعامة.

* * * * *

الفكرة
------

بدل ما تخترق الشركة مباشرة.

تشوف هي سربت معلومات بنفسها ولا لأ 😅

* * * * *

مثال 1: Stack Overflow
----------------------

تخيل مطور عنده مشكلة وكتب سؤال:

```
conn = ldap.connect(
    "ldap://dc.company.local",
    "admin",
    "Password123"
)
```

هو كان عايز مساعدة تقنية.

لكن نسي يخفي الـ Password.

بقت متاحة للعالم كله.

* * * * *

مثال 2: GitHub
--------------

مطور رفع مشروع على GitHub.

وفي ملف:

```
username="ahmed"
password="Welcome123!"
```

نسي يشيل البيانات قبل الـ Commit.

* * * * *

مثال 3: Data Breaches
---------------------

وده بيحصل كتير جدًا.

* * * * *

أحمد عنده إيميل الشركة:

```
ahmed@company.com
```

راح سجل بيه في موقع ألعاب أو متجر.

* * * * *

الموقع اتخترق.

وقاعدة البيانات اتسربت.

* * * * *

بقت بياناته موجودة:

```
ahmed@company.com
Password123
```

* * * * *

هنا المهاجم يفكر:

> طب ما يمكن أحمد لسه بيستخدم نفس الباسورد في الشركة؟

* * * * *

مواقع مشهورة
============

زي:

-   Have I Been Pwned
-   DeHashed

دي مواقع بتجمع بيانات التسريبات المعروفة.

* * * * *

المشكلة
-------

حتى لو لقيت Password

مش معناه إنها لسه شغالة.

مثال:

```
2021:
Password123

2026:
Winter2026!
```

أكيد غيرها.

* * * * *

عشان كده لازم نختبرها.

وده اللي الروم بيقوله:

في التاسكات الجاية هنشوف خدمات تسمح باختبار الـ Credentials.

* * * * *

ليه OSINT مهم؟
==============

لأنه:

```
قليل التكلفة
قانونيًا أقل خطورة
مفيهوش ضوضاء كثيرة
```

وممكن يجيب نتائج ممتازة.

* * * * *

الطريقة الثانية: Phishing
=========================

دي أشهر طريقة في العالم الحقيقي.

* * * * *

الفكرة ببساطة:

بدل ما أسرق الباسورد.

أخلي المستخدم يديهولي بنفسه.

😂

* * * * *

سيناريو
-------

أبعت إيميل:

```
Dear Employee,

Your password will expire.
Please login here:
```

مع لينك مزيف.

* * * * *

الضحية تدخل:

```
Username
Password
```

* * * * *

وأنا أخزن البيانات.

* * * * *

نوع تاني
========

مش لازم Password.

* * * * *

أبعت ملف:

```
Invoice.pdf.exe
```

أو

```
salary_report.docm
```

* * * * *

الضحية تشغله.

يتثبت برنامج خبيث.

* * * * *

RAT يعني إيه؟
=============

اختصار:

```
Remote Access Trojan
```

* * * * *

تخيله كأنه باب خلفي.

بعد ما الضحية تشغله:

```
Victim PC
      │
      ▼
 Attacker
```

* * * * *

أقدر:

-   أشوف الملفات
-   أشغل أوامر
-   أرفع ملفات
-   أنزل ملفات

* * * * *

ليه ده خطير في الـ AD؟
======================

لأن البرنامج بيشتغل تحت صلاحيات المستخدم.

مثال:

```
Mohamed Logged In
```

* * * * *

الـ RAT هيشتغل كأنه:

```
Mohamed
```

* * * * *

يعني أنا بقيت أتصرف بهويته داخل الدومين.

* * * * *

الفرق بين OSINT و Phishing
==========================

| OSINT | Phishing |
| --- | --- |
| أبحث عن بيانات مسربة | أخدع الضحية |
| لا أتفاعل مع الهدف | أتفاعل مع الهدف |
| هادئ جدًا | أكثر وضوحًا |
| قد تكون البيانات قديمة | غالبًا بيانات حديثة |
| لا يحتاج وصول للشبكة | يحتاج وصول للضحية |

* * * * *

ليه الروم بدأ بالجزء ده؟
========================

لأنه بيقولك:

قبل ما نتكلم عن:

-   NTLM
-   LDAP
-   Responder
-   Relays
-   Kerberos

لا تنسَ أن أحيانًا أسهل اختراق في العالم هو:

```
Google
GitHub
LinkedIn
StackOverflow
Data Breaches
```

مش لازم تبدأ بأداة هاكر خارقة.

أحيانًا أول Username وPassword بييجوا من معلومة صاحب الشركة أو الموظف سربها بنفسه. 😄🔥



وده بالضبط اللي بنسميه في الـ Kill Chain:

```
Reconnaissance
↓
Initial Access
```



  
</details>



---

<details>
  <summary>NTLM Authenticated Services</summary>

أولًا: يعني إيه NTLM؟
=====================

زمان قبل Kerberos، مايكروسوفت كانت بتستخدم بروتوكول اسمه:

```
NTLM
```

اختصار:

```
New Technology LAN Manager
```

وده مسؤول عن التحقق من هوية المستخدم.

* * * * *

مثال بسيط
---------

أنت عايز تدخل على:

```
mail.company.com
```

أو

```
vpn.company.com
```

أو

```
intranet.company.com
```

الموقع نفسه مش بيحتفظ بالباسورد.

بدل كده بيقول:

> يا Domain Controller، الراجل ده فعلًا اسمه أحمد والباسورد صح؟

* * * * *

طيب NetNTLM إيه؟
================

دي طريقة الـ Challenge-Response اللي NTLM بيستخدمها.

الفكرة:

بدل ما تبعت الباسورد مباشرة.

يحصل حوار بين:

```
Client
   ↓
Application
   ↓
Domain Controller
```

* * * * *

مثال مبسط جدًا

المستخدم:

```
Ahmed
Password123
```

* * * * *

السيرفر يقول:

```
اثبت إنك تعرف الباسورد
```

ويبعث Challenge.

* * * * *

الجهاز يحسب Response باستخدام الباسورد.

* * * * *

الـ DC يعمل نفس الحساب.

لو النتيجة متطابقة:

```
Authentication Success
```

* * * * *

لو مختلفة:

```
Authentication Failed
```

* * * * *

ليه الشركات بتحب ده؟
====================

لأن الباسورد لا يتم تخزينه في التطبيق.

* * * * *

بدل:

```
OWA
VPN
Website
```

كل واحد يحتفظ بالباسورد.

* * * * *

يكون عندنا:

```
Domain Controller
```

هو المكان الوحيد المسؤول عن الـ Credentials.

* * * * *

أمثلة لخدمات تستخدم NTLM
========================

الروم ذكر:

### Outlook Web App

```
mail.company.com
```

* * * * *

### RDP

```
mstsc
```

* * * * *

### VPN

```
vpn.company.com
```

* * * * *

### Web Applications

أي موقع داخلي عامل:

```
Windows Authentication
```

* * * * *

يعني إيه Windows Authentication؟
================================

أكيد شفت النافذة دي:

```
Username:
Password:
Domain:
```

أو المتصفح يطلب منك Credentials.

دي غالبًا NTLM Authentication.

* * * * *

فين الهجوم هنا؟
===============

تخيل أثناء الـ OSINT جمعنا:

```
anthony.reynolds
henry.taylor
louise.talbot
...
```

يعني عندنا Users.

لكن معندناش Passwords.

* * * * *

أول فكرة تخطر ببال المهاجم
==========================

أجرب كلمات سر كثيرة على كل User.

مثال:

```
anthony.reynolds
123456

anthony.reynolds
Password123

anthony.reynolds
Welcome1
```

* * * * *

ده اسمه:

```
Brute Force
```

* * * * *

ليه ده مش هيشتغل؟
=================

لأن أغلب بيئات الـ AD فيها:

```
Account Lockout Policy
```

* * * * *

يعني لو غلطت 5 مرات:

```
User Locked
```

* * * * *

فتلاقي نفسك قفلت حسابات الموظفين كلها 😂

والـ SOC يصحى عليك.

* * * * *

الحل: Password Spraying
=======================

ودي نقطة مهمة جدًا.

* * * * *

Brute Force
-----------

نجرب:

```
Ahmed → 1000 passwords
```

* * * * *

Password Spraying
-----------------

نجرب:

```
1000 users → password واحد
```

* * * * *

مثال:

```
Ahmed     → Changeme123
Ali       → Changeme123
Mona      → Changeme123
Omar      → Changeme123
```

* * * * *

وبعدين نستنى شوية.

لو عايزين نجرب Password تاني:

```
Welcome123
```

* * * * *

ليه الطريقة دي ذكية؟
====================

لأن كل User اتجرب عليه مرة واحدة فقط.

* * * * *

فمش هتوصل للـ Lockout.

* * * * *

ليه اختاروا Changeme123 ؟
=========================

لأن أثناء الـ OSINT عرفوا:

> الشركة بتدي الموظفين الجدد الباسورد الابتدائي:

```
Changeme123
```

* * * * *

نظريًا الموظف لازم يغيرها.

لكن عمليًا؟

ناس كثيرة بتنسى 😂

* * * * *

نروح للكود
==========

الروم أعطاك:

```
response = requests.get(
    url,
    auth=HttpNtlmAuth(
        self.fqdn + "\\" + user,
        password
    )
)
```

* * * * *

السطر ده معناه:

```
جرب Username
+
Password
```

على موقع NTLM.

* * * * *

مثال:

```
za.tryhackme.com\ahmed
Changeme123
```

* * * * *

بعد كده
=======

الكود يراقب:

```
response.status_code
```

* * * * *

لو رجع
======

```
200 OK
```

يبقى:

```
Username صحيح
Password صحيح
```

* * * * *

لو رجع
======

```
401 Unauthorized
```

يبقى:

```
Username/Password غلط
```

* * * * *

يعني إيه 200؟
=============

الموقع قال:

> أهلاً بيك، اتفضل ادخل.

* * * * *

يعني إيه 401؟
=============

الموقع قال:

> لأ، بياناتك غلط.

* * * * *

مثال عملي
=========

عندنا:

```
anthony.reynolds
henry.taylor
mona.smith
omar.hassan
```

* * * * *

نجرب:

```
Password = Changeme123
```

* * * * *

النتائج:

```
anthony.reynolds -> 401
henry.taylor -> 401
mona.smith -> 200
omar.hassan -> 401
```

* * * * *

إذن:

```
mona.smith : Changeme123
```

Credential صالح.

* * * * *

ليه الهجوم ده قوي؟
==================

لأنه يعتمد على حاجة بشرية جدًا:

> الموظفين لا يغيرون الباسورد الافتراضي.

* * * * *

ليه الروم بيبدأ بيه؟
====================

لأن السيناريو الواقعي غالبًا:

```
OSINT
↓
جمع Users
↓
Password Spraying
↓
الحصول على أول Account
↓
AD Enumeration
↓
Privilege Escalation
```

  
</details>




<details>
  <summary>practical</summary>


```
cd /root/Rooms/BreachingAD/task3/
```


<img width="1030" height="172" alt="image" src="https://github.com/user-attachments/assets/bf2d85a7-3b65-488b-96ea-60cd014e7a7a" />


````
python3 ntlm_passwordspray.py -u usernames.txt -f za.tryhackme.com -p Changeme123 -a http://ntlmauth.za.tryhackme.com/
````

## at first it not work because DNS Resolution so i try

```
echo "10.200.70.201 ntlmauth.za.tryhackme.com" >> /etc/hosts
```

## it work 

<img width="1828" height="695" alt="image" src="https://github.com/user-attachments/assets/ece98fe5-887f-4387-b7ff-6bb0f5a196d4" />



  
</details>
 


----

<details>
  <summary>LDAP Bind Credentials</summary>



LDAP Bind Credentials & LDAP Pass-back Attack
=============================================

أولًا: ليه الـ Application محتاج LDAP؟
--------------------------------------

تخيل عندك Application أو Printer أو GitLab.

المستخدم يعمل Login.

التطبيق محتاج يعرف:

> هل اليوزر ده موجود في الـ Active Directory؟

التطبيق بيستخدم بروتوكول اسمه:

```
LDAP
```

عشان يسأل الـ Active Directory.

* * * * *

ثانيًا: Service Account
-----------------------

التطبيق ميقدرش يكلم الـ AD بدون Authentication.

لذلك الشركة تعمل Account مخصوص للتطبيق.

مثال:

```
Username: svcLDAP
Password: password11
```

وده اسمه:

```
Service Account
```

مش Account لموظف، لكن للتطبيق.

* * * * *

ثالثًا: الطابعة بتعمل إيه؟
--------------------------

الطابعة تحتفظ بإعدادات زي:

```
LDAP Server = THMDC.za.tryhackme.com
Username = svcLDAP
Password = ********
```

كل مرة تحتاج تتحقق من مستخدم، تعمل Login باستخدام الـ Service Account.

* * * * *

فين المشكلة؟
============

إحنا مش شايفين الباسورد.

لكن نقدر نغير:

```
LDAP Server
```

* * * * *

بدل:

```
THMDC.za.tryhackme.com
```

نحطه:

```
IP جهازنا
```

فتبقى الطابعة بتكلم جهازنا بدل الـ Domain Controller.

* * * * *

ليه الـ Netcat لوحده مش كفاية؟
==============================

لأن الطابعة أول ما تتصل، بتسأل:

> إنت LDAP Server؟

> بتدعم Authentication إيه؟

الـ Netcat مجرد Listener.

مش هيعرف يرد.

فالطابعة تقفل الاتصال.

* * * * *

الحل
====

نشغل:

```
OpenLDAP
```

ونخليه Rogue LDAP Server.

* * * * *

ليه عدلنا إعدادات OpenLDAP؟
---------------------------

عشان نخليه يقول للطابعة:

```
أنا بدعم:

PLAIN
LOGIN
```

فقط.

* * * * *

بدل:

```
Kerberos
NTLM
SASL
```

* * * * *

ليه PLAIN مهم؟
==============

لأن الطابعة هتبعت:

```
Username
Password
```

بشكل واضح (Plain Text).

وده اللي إحنا عايزينه.

* * * * *

tcpdump عمل إيه؟
================

بعد ما الطابعة بعتت الـ Credentials.

احنا شغلنا:

```
tcpdump
```

عشان يقرأ الباكتس.

فنشوف:

```
za.tryhackme.com\svcLDAP
password11
```

* * * * *

ليه اسمه LDAP Pass-back؟
========================

لأن الطبيعي:

```
Printer
    │
    ▼
Domain Controller
```

لكن إحنا غيرنا الـ LDAP Server إلى جهازنا.

فبقت:

```
Printer
    │
    ▼
Attacker
```

فالـ Credentials "رجعت" للمهاجم بدل ما تروح للـ Domain Controller.

* * * * *

الهجوم في صورة واحدة
====================

```
Application/Printer
        │
        │ يستخدم Service Account
        ▼
 Active Directory (LDAP)
```

⬇️ المهاجم يغير LDAP Server

```
Application/Printer
        │
        ▼
Rogue LDAP Server (Attacker)
        │
        ▼
Username + Password
```



  
</details>




<details>
  <summary>practical</summary>



## download OpenLDAP 

```
sudo apt update
sudo apt install -y slapd ldap-utils
```


## prepaire slapd


```
sudo dpkg-reconfigure -p low slapd
```

```
Omit OpenLDAP server configuration?  -> No
DNS domain name                     -> za.tryhackme.com
Organization name                   -> za.tryhackme.com
Administrator password              -> any password you wnat
Database backend                    -> MDB
Remove database when purged?        -> No
Move old database?                  -> Yes
```


<img width="1538" height="752" alt="image" src="https://github.com/user-attachments/assets/9919e843-be8c-4881-8371-059e650f7ad3" />

---

## create LDIF file

```
nano olcSaslSecProps.ldif
```

```
dn: cn=config
replace: olcSaslSecProps
olcSaslSecProps: noanonymous,minssf=0,passcred
```


Why did the lab environment make this configuration change?
----------------------------

By default, OpenLDAP enforces a policy of:

```
minssf=128
```

This means the connection must be encrypted (via TLS/SSL) before allowing:

-   PLAIN
-   LOGIN

However, the lab aims to simulate an old or misconfigured Domain Controller that permits NTLM authentication without TLS.

So, we set:

```
minssf=0
```

This means:

> Allow Simple Bind even if the connection is unencrypted.

This is what enables the lab tools to function.


## apply change

```
sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif
sudo service slapd restart
```

## check chanes 

```
ldapsearch -H ldap:// -x -LLL -s base -b "" supportedSASLMechanisms
```

<img width="1342" height="243" alt="image" src="https://github.com/user-attachments/assets/8f7d796e-6a0a-4c06-9088-721d86fc2b31" />

> ## the fucken lab have Differences between AttackBox/OpenLDAP versions
> we should see just : `LOGIN`,`PLAIN`

## open TCPDUMP

```
sudo tcpdump -A -i breachad tcp port 389
```

## now open send test of printer 


```
echo "10.200.70.201 printer.za.tryhackme.com" >> /etc/hosts
```

**`open`**
```
http://printer.za.tryhackme.com/settings.aspx
```

<img width="1373" height="435" alt="image" src="https://github.com/user-attachments/assets/0c643e33-650a-475a-864c-7fb0008631ee" />

## change server to our ip and click test settings

<img width="1103" height="364" alt="image" src="https://github.com/user-attachments/assets/c4821900-6ed7-4800-8223-c1f3c527f11f" />

## check tcpdump 


# 🙂

  
</details>



----


<details>
  <summary>Authentication Relays</summary>










  
</details>

































