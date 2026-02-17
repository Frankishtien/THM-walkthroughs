# Enumeration & Brute Force



<img width="1901" height="378" alt="image" src="https://github.com/user-attachments/assets/1210bd29-1ad0-48db-adc8-e380059b40b5" />



<details>
  <summary>Authentication Enumeration</summary>


🧠 Authentication Enumeration -- الخلاصة العملية
-----------------------------------------------

### 🔍 يعني إيه؟

ببساطة:\
**بتحاول تعرف معلومات عن نظام الدخول من غير ما تسجل دخول فعلي**\
(يوزر موجود؟ سياسة الباسورد؟ في فرق ردود؟)

* * * * *

🎯 أهم حاجتين بنطلعهم
---------------------

### 1️⃣ هل اليوزر موجود ولا لأ

### 2️⃣ إيه سياسة الباسورد

* * * * *

🧪 الطرق العملية (اللي تهمك في التحديات)
----------------------------------------

### 🔑 1) صفحة الـ Login

ركز على **الرسائل**:

-   ❌ `Username not found`

-   ❌ `Incorrect password`

📌 لو في فرق =\
➡️ اليوزر صحيح\
➡️ نكمل عليه brute-force أو logic bug

* * * * *

### 🔁 2) Forgot Password

-   `User does not exist`

-   `Reset link sent`

📌 الفرق ده = **User Enumeration واضح**

* * * * *

### 📝 3) Register

-   `Username already taken`

-   `Email already registered`

📌 دي أسهل طريقة تجمع users list

* * * * *

### 🧾 4) Password Policy

لو شفت رسالة زي:

> Must contain uppercase, number, symbol

📌 معناها:

-   اعمل wordlist مخصص

-   متضيعش وقتك بكلام فاضي

مثال توليد باسوردات:

Password@123\
Admin#2026\
Love!Note9

* * * * *

### ⚠️ 5) Verbose Errors (الخطر الحقيقي)

أي فرق رد:

-   Status code

-   Message

-   Response time

📌 حتى **فرق 100ms** ممكن يكون user موجود

* * * * *

### 🧨 6) Data Breach

لو اليوزر طلع موجود:

-   جرب باسوردات شائعة

-   جرب combo من breaches

📌 دايمًا اليوزر هو المفتاح 🔑

* * * * *

🛠️ Tools تحبها
---------------

-   Burp Intruder

-   ffuf

-   wfuzz

-   curl + diff

-   custom wordlists


<img width="1628" height="215" alt="image" src="https://github.com/user-attachments/assets/5044fbfc-496d-4424-9fc4-afa5fc55338a" />



  
</details>



<details>
  <summary>Enumerating Users via Verbose Errors</summary>










  
</details>






































