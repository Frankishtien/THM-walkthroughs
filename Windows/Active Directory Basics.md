# Active Directory Basics


<img width="1893" height="357" alt="image" src="https://github.com/user-attachments/assets/63be96df-4536-4efb-a681-41be228eaac5" />





<details>
  <summary>Windows Domains</summary>


# مقدمة إلى Windows Domain

## 💻 الموضوع ببساطة

تخيل أن لديك شركة صغيرة بها 5 أجهزة كمبيوتر و5 موظفين. في هذه الحالة، يمكنك التحكم في كل جهاز على حدة بسهولة:

- ✅ الدخول إلى كل جهاز بنفسك.
- ✅ إنشاء حسابات للمستخدمين على كل جهاز.
- ✅ إصلاح أي جهاز معطل بالذهاب إليه مباشرة.

**ولكن... ماذا لو نمت الشركة وأصبح لديك:**

- 👉 **157** جهاز كمبيوتر
- 👉 **320** مستخدمًا
- 👉 **4** فروع في أماكن مختلفة

هل ستتمكن من التحكم في كل جهاز وكل مستخدم بنفس الطريقة اليدوية؟ **بالتأكيد لا!** 😅 لأن هذا سيستغرق وقتًا ومجهودًا هائلاً.

---

## 🏢 الحل: Windows Domain

الحل هو **Windows Domain** (نطاق ويندوز)، وهو نظام يجعل إدارة جميع الأجهزة والمستخدمين **مركزية وسهلة**.

### 🌐 ما هو Windows Domain؟

هو مجموعة من المستخدمين والأجهزة التي تديرها الشركة من مكان واحد مركزي. يعتمد هذا النظام على ما يسمى بـ **Active Directory (AD)**، وهي قاعدة بيانات تسجل فيها كل المستخدمين والأجهزة.

أما السيرفر الذي يقوم بتشغيل الـ Active Directory فيُطلق عليه اسم **Domain Controller (DC)**.

---

### 🌟 فوائد Windows Domain

1.  **✅ إدارة الحسابات بسهولة**
    - يمكنك من مكان واحد (Active Directory) إضافة أو حذف مستخدم أو تغيير بياناته، وستُطبق هذه التغييرات على جميع الأجهزة في نفس الوقت.

2.  **✅ إدارة السياسات الأمنية**
    - يمكنك عبر الـ Active Directory التحكم في صلاحيات المستخدمين، مثل منع الوصول إلى لوحة التحكم (Control Panel) أو تثبيت البرامج.

3.  **✅ مرونة في الدخول على الأجهزة**
    - يستطيع المستخدم تسجيل الدخول إلى أي جهاز كمبيوتر في الشبكة بنفس اسم المستخدم وكلمة المرور الخاصة به، لأن جميع الأجهزة تتحقق من صحة بياناته عبر الـ Active Directory.

---

### 🎓 مثال من الواقع

إذا كنت قد درست في مدرسة أو جامعة وكنت تستخدم أجهزة الكمبيوتر في المعمل بنفس حسابك الخاص، فذلك لأن هذه الأجهزة كلها جزء من **Domain**. كان الـ **Active Directory** هو الذي يتحقق من بياناتك ويطبق عليك السياسات المحددة (مثل منعك من فتح لوحة التحكم أو تنزيل برامج).

---

### 👨‍💼 سيناريو شركة THM Inc.

- **دورك:** أنت مسؤول الشبكة الجديد في الشركة.
- **البيئة:** الشركة لديها دومين باسم `THM.local`.
- **صلاحياتك:** لديك صلاحيات كاملة (Administrator) على الـ **Domain Controller** لتنفيذ مهامك.
- **مهمتك:** ستقوم بالاتصال بالسيرفر (الذي يحتوي على Active Directory) لمراجعة الإعدادات وإجراء التعديلات المطلوبة.

---

### 🛠 كيفية الاتصال بالسيرفر؟

سيكون لديك سيرفر جاهز في التمرين (يظهر في المتصفح أو يمكنك الاتصال به عبر RDP). استخدم البيانات التالية:

- **Username:** `THM\Administrator`
- **Password:** `Password321`

> **ملاحظة:** إضافة `THM\` قبل اسم المستخدم `Administrator` تعني أنك تخبر الجهاز بأن هذا الحساب تابع للدومين المسمى `THM`.

  
</details>







<details>
  <summary>Active Directory</summary>


يعني إيه Active Directory (AD)؟
-------------------------------

Active Directory Domain Services (**AD DS**) هو **عقل الشبكة** في بيئة ويندوز دومين.\
تقدر تعتبره **دليل / كتالوج مركزي** فيه كل حاجة موجودة في الشبكة:

-   مستخدمين

-   أجهزة

-   جروبات

-   سيرفرات

-   برنترات

-   شيرز

وأهم نقطة:\
👉 **أي حاجة في AD اسمها Object**

* * * * *

1️⃣ Users (المستخدمين)
----------------------

الـ Users هما أكتر Object مهم.

### User = Security Principal

يعني:

-   يقدر يعمل **Login**

-   يقدر يتمنح **Permissions**

-   يقدر يوصل لريسورس (ملفات -- سيرفر -- طابعة)

### نوعين Users:

#### 👤 People

-   موظفين الشركة

-   Dev / HR / IT

#### ⚙️ Service Accounts

-   حسابات بتشغل Services زي:

    -   IIS

    -   MSSQL

-   صلاحياتها محدودة جدًا (Least Privilege)

🔐 **أمنيًا:**\
Service Account متسرب = كنز للمهاجم\
(خصوصًا Kerberoasting)

* * * * *

2️⃣ Machines (الأجهزة)
----------------------

أي جهاز يدخل الدومين → AD يعمل له **Machine Account**

-   يعتبر **Security Principal**

-   له Username + Password

-   باسورد بيتغير تلقائي (120 char 😈)

### اسمها شكله إيه؟

```
DC01$
WIN10-IT01$
```

علامة `$` = Machine Account

⚠️ **CTF / Pentest معلومة خطيرة:**\
لو معاك باسورد Machine Account\
→ تقدر تعمل **Lateral Movement**

* * * * *

3️⃣ Security Groups (الجروبات)
------------------------------

بدل ما تدي صلاحيات User User\
تديها **Group** وخلاص

### مميزات:

-   أسهل إدارة

-   أقل أخطاء

-   scalable

### الجروبات تقدر تحتوي:

-   Users

-   Machines

-   Groups جوه Groups

* * * * *

أهم جروبات في الدومين 🔥
------------------------

| Group | خطره |
| --- | --- |
| **Domain Admins** | ملك الدومين 👑 |
| Server Operators | يتحكم في DC |
| Backup Operators | يقرأ أي ملف |
| Account Operators | ينشئ Accounts |
| Domain Users | كل المستخدمين |
| Domain Computers | كل الأجهزة |
| Domain Controllers | كل الـ DCs |

🎯 **Pentest Rule:**\
أي طريق يوصلك لـ **Domain Admin** = GAME OVER

* * * * *

4️⃣ Active Directory Users and Computers
----------------------------------------

ده الـ GUI اللي بتدير منه كل حاجة.

تقدر من خلاله:

-   تنشئ Users / Groups

-   Reset Password

-   تنقل Computers

-   تشوف Hierarchy الدومين

* * * * *

5️⃣ Organizational Units (OUs)
------------------------------

الـ OU = **Container**

### وظيفتها الأساسية:

-   تنظيم المستخدمين

-   تطبيق **Group Policy (GPO)**

### مثال:

```
THM
 ├─ IT
 ├─ Sales
 ├─ HR
 ├─ R&D 
 
 ```

👤 User واحد → OU واحدة فقط\
(عشان السياسات متتلغبطش)

* * * * *

Containers الافتراضية
---------------------

Windows بيعملها لوحده:

-   **Builtin** → جروبات ويندوز

-   **Computers** → أي جهاز جديد

-   **Domain Controllers** → الـ DCs

-   **Users** → Default users

-   **Managed Service Accounts** → Service users

* * * * *

الفرق المهم جدًا 🔥
-------------------

### Security Groups vs OUs

| حاجة | تستخدم في |
| --- | --- |
| **OU** | سياسات (GPO) |
| **Group** | صلاحيات (Permissions) |

### مثال:

-   Sales OU\
    → سياسة تمنع USB

-   Finance Group\
    → صلاحية قراءة Folder مالي

👤 User:

-   عضو في **OU واحدة**

-   عضو في **Groups كتير**

* * * * *

من منظور أمني / CTF 🧠
----------------------

ركز دايمًا على:

-   Domain Admins

-   Service Accounts

-   Machine Accounts

-   Weak Group Memberships

-   Misconfigured OUs & GPOs






  
</details>




<details>
  <summary>Managing Users in AD</summary>




* * * * *

1️⃣ حذف OU زيادة (ليه مش راضية تتحذف؟)
--------------------------------------

### ليه الـ OU مش بتتحذف؟

Windows عامل حماية افتراضية اسمها:

> **Protect object from accidental deletion**

عشان تحميك من كارثة إنك تمسح OU بالغلط.

* * * * *

### الحل ✔️

1.  افتح **Active Directory Users and Computers**

2.  من فوق:

    `View → Advanced Features ✅ `

3.  كليك يمين على الـ OU الزيادة

4.  **Properties → Object**

5.  شيل علامة الصح من:

    `Protect object  from accidental deletion `

6.  OK → Delete

⚠️ تحذير:

-   أي Users / Groups / OUs جوه الـ OU ده → هيتحذفوا

* * * * *

2️⃣ تعديل المستخدمين حسب Org Chart
----------------------------------

بعد حذف الـ OU:

-   راجع كل Department

-   اعمل:

    -   Create Users ناقصين

    -   Delete Users زيادة

📌 ده مهم جدًا في الحياة العملية:

-   Users زيادة = Attack Surface أكبر

-   حسابات قديمة = كنز للمهاجم

* * * * *

3️⃣ Delegation (ليه مهمة؟)
--------------------------

مش كل حاجة لازم Domain Admin يعملها.

### Delegation = أقل صلاحية تؤدي الغرض

(Principle of Least Privilege)

* * * * *

السيناريو:
----------

Phillip مسؤول IT Support\
→ محتاج:

-   Reset Passwords

-   بس **مش Domain Admin**

* * * * *

4️⃣ تفويض Phillip على Sales OU
------------------------------

### الخطوات:

1.  كليك يمين على:

    `OU = Sales `

2.  **Delegate Control**

3.  Add user:

    `phillip → Check  Names → OK  `

4.  اختار:

    `Reset  user passwords and force password change at next logon `

5.  Next → Finish

🎉 كده Phillip يقدر:

-   يغير باسورد

-   بس **داخل Sales فقط**

* * * * *

5️⃣ تسجيل الدخول بحساب Phillip
------------------------------

### RDP:

`Username: THM\phillip  Password: Claire2008  `

⚠️ مهم جدًا:\
لازم تكتب:

`THM\phillip`

مش بس phillip

* * * * *

6️⃣ ليه مش ينفع ADUC؟
---------------------

Phillip **مش عنده صلاحية** يفتح Active Directory Users and Computers\
وده المطلوب 👌

الحل؟\
👉 **PowerShell**

* * * * *

7️⃣ Reset Password لسوفي (Sophie)
---------------------------------

### الأمر:

`Set-ADAccountPassword sophie -Reset  -NewPassword (Read-Host  -AsSecureString  -Prompt  'New Password') -Verbose  `

-   هيطلب منك باسورد جديد

-   مش هيظهر على الشاشة

* * * * *

8️⃣ إجبار Sophie تغيّر الباسورد
-------------------------------

عشان متفضلش تستخدم باسورد أنت عارفه:

`Set-ADUser  -ChangePasswordAtLogon  $true  -Identity sophie -Verbose  `

🔐 دي Best Practice أمنية جدًا

* * * * *

9️⃣ تسجيل الدخول بحساب Sophie
-----------------------------

### RDP:

`Username: THM\sophie  Password: (الباسورد الجديد)  `

-   هيطلب تغيير الباسورد أول Login

-   بعد الدخول:\
    👉 Desktop → **FLAG 🚩**










<img width="1073" height="796" alt="image" src="https://github.com/user-attachments/assets/57275abe-7ac7-48d9-87cf-26843330f03b" />

<img width="1198" height="585" alt="image" src="https://github.com/user-attachments/assets/392dc19e-3ab7-4064-8534-6be84611466f" />

<img width="941" height="551" alt="image" src="https://github.com/user-attachments/assets/0d520090-c10d-4a61-b213-a4265f214103" />

<img width="667" height="444" alt="image" src="https://github.com/user-attachments/assets/8ad345d5-01b5-4397-b4f0-4ab6b78e0e88" />

<img width="1066" height="642" alt="image" src="https://github.com/user-attachments/assets/0399e9d9-2088-425f-bf26-4239b8883922" />





----




<img width="907" height="557" alt="image" src="https://github.com/user-attachments/assets/8fce64de-10d3-47eb-864e-dbab1f4218c1" />

<img width="1136" height="619" alt="image" src="https://github.com/user-attachments/assets/005ba90f-3563-4adf-b5cb-1bed937fc5ee" />


> ### Now phillip can change password of users in sales put he can't open active directory so he will chane it from powershell and will force this user to change password after login




```
rdesktop 10.64.183.136 -u phillip -p Claire2008
```




<img width="1700" height="826" alt="image" src="https://github.com/user-attachments/assets/f3f5782d-2226-4b4e-973d-9764f0995ba2" />

<img width="1652" height="785" alt="image" src="https://github.com/user-attachments/assets/d5369f82-641f-48d3-b308-4017b3059f78" />

---

```
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose
```


<img width="1058" height="210" alt="image" src="https://github.com/user-attachments/assets/f39f407e-3a4f-414c-bd64-9c9355677415" />

```
Hello@123
```

```
rdesktop 10.64.183.136 -u Sophie -p Hello@123 
```




<img width="1547" height="717" alt="image" src="https://github.com/user-attachments/assets/6ca4cb3f-f21a-42d8-8b9f-41a6e203785c" />


<img width="1356" height="637" alt="image" src="https://github.com/user-attachments/assets/3a09f18c-642b-4a8c-a10b-a719ce62be8f" />

<img width="1323" height="712" alt="image" src="https://github.com/user-attachments/assets/772465d9-63e3-4947-b2ae-f99701f9ac29" />



  
</details>







<details>
  <summary>Managing Computers in AD</summary>




🧠 الفكرة الأساسية
------------------

بشكل افتراضي:

-   أي جهاز يدخل الدومين

-   **مش DC**\
    ➡️ يتحط في Container اسمه **Computers**

وده **وحش جدًا أمنيًا وإداريًا**.

ليه؟

-   Servers ≠ User PCs

-   سياسات مختلفة

-   مخاطرة إن سياسات غلط تتطبق

* * * * *

❌ ليه Computers Container مشكلة؟
--------------------------------

-   مش OU → **ماينفعش تطبق GPOs بمرونة**

-   كل الأجهزة متلخبطة:

    -   Laptops

    -   PCs

    -   Servers

-   صعب تحكم:

    -   USB

    -   RDP

    -   Admin rights

    -   Updates

* * * * *

✅ الحل الصح (Best Practice)
---------------------------

تقسم الأجهزة حسب **وظيفتها**.

أقل تقسيم محترم:

### 1️⃣ Workstations

أجهزة المستخدمين:

-   PCs

-   Laptops

📌 خصائصها:

-   Users عاديين

-   Browsing

-   Email

-   Office

🔐 قواعد أمنية:

-   ممنوع Admin login

-   USB محدود

-   Hardening عالي

* * * * *

### 2️⃣ Servers

أجهزة بتقدم Services:

-   File Server

-   Web Server

-   DB Server

📌 خصائصها:

-   Access محدود

-   Admins بس

-   سياسات مختلفة تمامًا

* * * * *

### 3️⃣ Domain Controllers

-   أهم أجهزة في الدومين

-   فيها:

    -   Password Hashes

    -   Kerberos Keys

🔴 Windows أصلاً عاملهم OU لوحدهم\
**ما تلمسهمش**

* * * * *

🏗️ المطلوب تعمله في المهمة
---------------------------

### إنشاء OU جديدة

تحت:

```
thm.local  
```

اعمل:

```
OU = Workstations
OU = Servers
```

* * * * *

✋ ليه تحت Root؟
---------------

عشان:

-   سياسات عامة

-   Hierarchy واضح

-   GPO inheritance مظبوط

* * * * *

🧱 الشكل النهائي
----------------

```
thm.local
  ├── Workstations
  ├── Servers
  ├── Domain Controllers
  ├── THM
  │   ├── IT
  │   ├── Sales
  │   ├── Marketing
  │   └── ... 

```

* * * * *

🔄 نقل الأجهزة
--------------

من:

`Computers  `

### نقل:

-   PCs & Laptops → **Workstations**

-   Servers → **Servers**

💡 كليك يمين → Move

* * * * *

🎯 ليه ده مهم أمنيًا؟
---------------------

### بعد التقسيم تقدر:

-   تطبق GPO مختلف لكل نوع

-   تمنع:

    -   Admin logon على Workstations

    -   USB

-   تفصل Updates

-   تمنع Lateral Movement


















<img width="1100" height="413" alt="image" src="https://github.com/user-attachments/assets/9699c74f-75c1-448e-86af-06f61562bffc" />


---

<img width="1210" height="772" alt="image" src="https://github.com/user-attachments/assets/0da2e311-e3b5-4aee-9f18-899fdbba1a7a" />

<img width="693" height="429" alt="image" src="https://github.com/user-attachments/assets/0ea9bd60-278e-4870-a86d-0aba9a5f454d" />


MOVE 

<img width="1197" height="624" alt="image" src="https://github.com/user-attachments/assets/f68e15b9-ea0b-4db2-8c5b-2bd9fc27638d" />

<img width="976" height="551" alt="image" src="https://github.com/user-attachments/assets/457528e8-21e4-4f93-b994-69a09fa188af" />

<img width="1061" height="599" alt="image" src="https://github.com/user-attachments/assets/c8c0064f-8b56-4727-80cc-75c8fe5d35d4" />

<img width="964" height="633" alt="image" src="https://github.com/user-attachments/assets/f5d33041-f6f8-473b-a1b4-7a25907de717" />


  
</details>










































































