# Command Injection


<img width="1904" height="380" alt="image" src="https://github.com/user-attachments/assets/2578b187-6016-4669-8634-7f482de595b8" />


---

<details>
  <summary>Discovering Command Injection</summary>



This vulnerability exists because applications often use functions in programming languages such as PHP, Python and NodeJS to pass data to and to make system calls on the machine's operating system. For example, taking input from a field and searching for an entry into a file. Take this code snippet below as an example:

In this code snippet, the application takes data that a user enters in an input field named `$title `to search a directory for a song title. Let's break this down into a few simple steps.


<img width="1313" height="448" alt="image" src="https://github.com/user-attachments/assets/385eab30-d116-4618-853a-9a2573c33120" />


**1.** The application stores MP3 files in a directory contained on the operating system.

**2.** The user inputs the song title they wish to search for. The application stores this input into the `$title` variable.

**3\. **The data within this `$title` variable is passed to the command `grep` to search a text file named *songtitle.**txt* for the entry of whatever the user wishes to search for.

**4.** The output of this search of *songtitle.**txt* will determine whether the application informs the user that the song exists or not.

Now, this sort of information would typically be stored in a database; however, this is just an example of where an application takes input from a user to interact with the application's operating system.

An attacker could abuse this application by injecting their own commands for the application to execute. Rather than using `grep` to search for an entry in `songtitle.txt`, they could ask the application to read data from a more sensitive file.

Abusing applications in this way can be possible no matter the programming language the application uses. As long as the application processes and executes it, it can result in command injection. For example, this code snippet below is an application written in Python.


<img width="1136" height="393" alt="image" src="https://github.com/user-attachments/assets/a5df0e24-1a4e-478e-9057-860bde757cb7" />



Note, you are not expected to understand the syntax behind these applications. However, for the sake of reason, I have outlined the steps of how this Python application works as well.

1.  The "flask" package is used to set up a web server
2.  A function that uses the "subprocess" package to execute a command on the device
3.  We use a route in the webserver that will execute whatever is provided. For example, to execute `whoami`, we'd need to visit http://flaskapp.thm/whoami




---

<img width="1638" height="465" alt="image" src="https://github.com/user-attachments/assets/c437eaa2-75ec-46d9-851d-09da5931e471" />






  
</details>





<details>
  <summary>Exploiting Command Injection</summary>

You can often determine whether or not command injection may occur by the behaviours of an application, as you will come to see in the practical session of this room.

Applications that use user input to populate system commands with data can often be combined in unintended behaviour. **For example, the shell operators `;`, `&` and `&&` will combine two (or more) system commands and execute them both**. If you are unfamiliar with this concept, it is worth checking out the [Linux fundamentals module](https://tryhackme.com/module/linux-fundamentals) to learn more about this.

Command Injection can be detected in mostly one of two ways:

1.  Blind command injection
2.  Verbose command injection

I have defined these two methods in the table below, where the two sections underneath will explain these in greater detail.



|            |                                                                                                                                                                                                                                                                               |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Method** | **Description**                                                                                                                                                                                                                                                               |
| Blind      | This type of injection is where there is no direct output from the application when testing payloads. You will have to investigate the behaviours of the application to determine whether or not your payload was successful.                                                 |
| Verbose    | This type of injection is where there is direct feedback from the application once you have tested a payload. For example, running the `whoami` command to see what user the application is running under. The web application will output the username on the page directly. |



---


Detecting Blind Command Injection

Blind command injection is when command injection occurs; however, there is no output visible, so it is not immediately noticeable. For example, a command is executed, but the web application outputs no message.

For this type of command injection, we will need to use payloads that will cause some time delay. For example, the `ping` and `sleep` commands are significant payloads to test with. Using `ping` as an example, the application will hang for *x* seconds in relation to how many *pings* you have specified.

Another method of detecting blind command injection is by forcing some output. This can be done by using redirection operators such as `>`. If you are unfamiliar with this, I recommend checking out the [Linux fundamentals module](https://tryhackme.com/module/linux-fundamentals). For example, we can tell the web application to execute commands such as `whoami` and redirect that to a file. We can then use a command such as `cat` to read this newly created file's contents.

Testing command injection this way is often complicated and requires quite a bit of experimentation, significantly as the syntax for commands varies between Linux and Windows.

The `curl` command is a great way to test for command injection. This is because you are able to use `curl` to deliver data to and from an application in your payload. Take this code snippet below as an example, a simple curl payload to an application is possible for command injection.

`curl http://vulnerable.app/process.php%3Fsearch%3DThe%20Beatles%3B%20whoami`

Detecting Verbose Command Injection

Detecting command injection this way is arguably the easiest method of the two. Verbose command injection is when the application gives you feedback or output as to what is happening or being executed.

For example, the output of commands such as `ping` or `whoami` is directly displayed on the web application.

Useful payloads

I have compiled some valuable payloads for both Linux & Windows into the tables below.

### Linux


|   |   |
|---|---|
|**Payload**|**Description**|
|whoami|See what user the application is running under.|
|ls|List the contents of the current directory. You may be able to find files such as configuration files, environment files (tokens and application keys), and many more valuable things.|
|ping|This command will invoke the application to hang. This will be useful in testing an application for blind command injection.|
|sleep|This is another useful payload in testing an application for blind command injection, where the machine does not have `ping` installed.|
|nc|Netcat can be used to spawn a reverse shell onto the vulnerable application. You can use this foothold to navigate around the target machine for other services, files, or potential means of escalating privileges.|

  

### Windows

|   |   |
|---|---|
|**Payload**|**Description**|
|whoami|See what user the application is running under.|
|dir|List the contents of the current directory. You may be able to find files such as configuration files, environment files (tokens and application keys), and many more valuable things.|
|ping|This command will invoke the application to hang. This will be useful in testing an application for blind command injection.|
|timeout|This command will also invoke the application to hang. It is also useful for testing an application for blind command injection if the `ping` command is not installed.|



<details>
  <summary>detail</summary>


إزاي نكتشف **Blind Command Injection**؟
---------------------------------------

### الطريقة الأولى: Time-based ⏱️

نخلي السيرفر "يعلّق"

#### مثال:

`; sleep 5`

لو الصفحة:

-   بتفتح بعد 5 ثواني\
    يبقى:\
    ✅ الأمر اتنفذ\
    ✅ Blind Command Injection موجود

أوامر شائعة:

-   `sleep 5`

-   `ping -c 5 127.0.0.1`

* * * * *

### الطريقة التانية: إجبار السيرفر يطلع Output 📄

نخليه يكتب نتيجة الأمر في ملف وبعدين نقرأه.

#### مثال:

`; whoami > /tmp/test.txt`

وبعدين:

`; cat /tmp/test.txt`

⚠️ الطريقة دي محتاجة تجربة كتير عشان:

-   صلاحيات

-   مكان الملفات

-   نوع النظام (Linux / Windows)

* * * * *

ليه curl أداة ممتازة؟
---------------------

عشان تقدر:

-   تبعت payload بسهولة

-   تتحكم في request كامل

### مثال:

`curl "http://vulnerable.app/process.php?search=The%20Beatles;whoami"`

لو شفت output:

`www-data`

يبقى الإصابة مؤكدة 🎯

* * * * *

مقارنة سريعة 👇
---------------

| النوع | في Output؟ | طريقة الاكتشاف |
| --- | --- | --- |
| Verbose | ✅ | whoami / ls |
| Blind | ❌ | sleep / ping / write file |

  
</details>





---

<img width="1677" height="456" alt="image" src="https://github.com/user-attachments/assets/c1e8daa0-5d75-48db-9dba-a22c1ee8cfb9" />







  
</details>





<details>
  <summary>Remediating Command Injection</summary>


Command injection can be prevented in a variety of ways. Everything from minimal use of potentially dangerous functions or libraries in a programming language to filtering input without relying on a user's input. I have detailed these a bit further below. The examples below are of the PHP programming language; however, the same principles can be extended to many other languages.

**Vulnerable Functions**

In PHP, many functions interact with the operating system to execute commands via shell; these include:

-   Exec
-   Passthru
-   System

Take this snippet below as an example. Here, the application will only accept and process numbers that are inputted into the form. This means that any commands such as `whoami` will not be processed.

<img width="1024" height="179" alt="image" src="https://github.com/user-attachments/assets/66689d69-c545-490b-8343-5ee7be4b6939" />


1.  The application will only accept a specific pattern of characters (the digits  0-9)
2.  The application will then only proceed to execute this data which is all numerical.

These functions take input such as a string or user data and will execute whatever is provided on the system. Any application that uses these functions without proper checks will be vulnerable to command injection.

**Input sanitisation**

Sanitising any input from a user that an application uses is a great way to prevent command injection. This is a process of specifying the formats or types of data that a user can submit. For example, an input field that only accepts numerical data or removes any special characters such as `>` ,  `&` and `/`.

In the snippet below, the `filter_input` [PHP function](https://www.php.net/manual/en/function.filter-input.php) is used to check whether or not any data submitted via an input form is a number or not. If it is not a number, it must be invalid input.

<img width="820" height="163" alt="image" src="https://github.com/user-attachments/assets/d6611569-066d-4c6d-9ec4-351a3445d6d5" />

Bypassing Filters

Applications will employ numerous techniques in filtering and sanitising data that is taken from a  user's input. These filters will restrict you to specific payloads; however, we can abuse the logic behind an application to bypass these filters. For example, an application may strip out quotation marks; we can instead use the hexadecimal value of this to achieve the same result.

When executed, although the data given will be in a different format than what is expected, it can still be interpreted and will have the same result.

<img width="746" height="101" alt="image" src="https://github.com/user-attachments/assets/ba39a75c-f5e7-4c84-8381-dc6f9bb497ee" />


<details>
  <summary>explain</summary>



🔐 منع Command Injection -- الفكرة العامة
========================================

الفكرة الأساسية:

> **ما تدخلش User Input مباشرة في System Commands**

أي حاجة جاية من المستخدم = **غير موثوق فيها** ❌

* * * * *

1️⃣ الدوال الخطيرة في PHP (Vulnerable Functions)
------------------------------------------------

في PHP في دوال بتنفّذ أوامر نظام مباشرة:

| الدالة | بتعمل إيه |
| --- | --- |
| `exec()` | تنفذ أمر وترجع النتيجة |
| `system()` | تنفذ أمر وتطبع الـ output |
| `passthru()` | تنفذ أمر وتعرض الـ raw output |

### مثال خطير ❌

`system("ping " . $_GET['ip']);`

لو المستخدم دخل:

`127.0.0.1; whoami`

السيرفر ينفذ:

`ping 127.0.0.1
whoami`

💥 اختراق كامل

* * * * *

2️⃣ ليه تحديد نوع الإدخال مهم؟
------------------------------

لو التطبيق:

-   **بيسمح بأرقام بس**

-   أو **بيحدد Pattern معين**

ساعتها أوامر زي:

`; whoami`

❌ مش هتعدي

### مثال آمن نسبيًا ✅

`if (ctype_digit($_GET['count'])) {
    system("ping -c " . $_GET['count'] . " 127.0.0.1");
}`

هنا:

-   مسموح: `5`

-   مرفوض: `5; whoami`

* * * * *

3️⃣ Input Sanitisation (تنظيف الإدخال)
--------------------------------------

يعني:

> تتأكد إن الـ input مطابق للي انت مستنيه **قبل ما تستخدمه**

### أمثلة:

-   IP → regex

-   رقم → digits فقط

-   اسم ملف → whitelist

* * * * *

### مثال باستخدام `filter_input`

`$number = filter_input(INPUT_GET, 'num', FILTER_VALIDATE_INT);

if ($number === false) {
    echo "Invalid input";
} else {
    system("ping -c " . $number . " 127.0.0.1");
}`

🔐 هنا:

-   أي حاجة غير رقم → مرفوضة

-   مفيش فرصة لحقن أوامر

* * * * *

4️⃣ إزالة الرموز الخطيرة
------------------------

بعض التطبيقات بتعمل:

-   تشيل `;`

-   تشيل `&`

-   تشيل `>`

بس... 👀\
ده **مش حل كامل**

* * * * *

5️⃣ Bypassing Filters (تفكير المهاجم 🧠)
----------------------------------------

هنا بقى الجزء الممتع 😈\
المطور بيحاول يمنع، وانت بتحاول تلفّ

* * * * *

### مثال: حذف علامات الاقتباس `" '`

المطور شايل:

`" '`

انت تستخدم:

-   Hex

-   Encoding

-   Command substitution

#### مثال:

بدل:

`"whoami"`

تستخدم:

`$(whoami)`

أو:

``whoami``

أو:

`\x77\x68\x6f\x61\x6d\x69`

💡 **النظام بيفهمها في الآخر بنفس المعنى**

* * * * *

### مثال تجاوز فلتر

لو الفلتر بيشيل `;`\
جرب:

-   `&&`

-   `|`

-   newline `%0a`

* * * * *

6️⃣ ليه الفلاتر لوحدها ضعيفة؟
-----------------------------

لأن:

-   المهاجم دايمًا هيلاقي طريقة جديدة

-   Encoding / Obfuscation / Logic Abuse

❌ Blacklist = فشل\
✅ Whitelist = أفضل

* * * * *

7️⃣ الحل الصح فعليًا (Secure Design)
------------------------------------

أفضل حلول:

### ✅ عدم استخدام system أصلاً

-   استخدم APIs

-   Libraries جاهزة

### ✅ Whitelisting

`$allowed = ['1','2','3','4','5'];`

### ✅ Least Privilege

-   السيرفر يشتغل بـ user ضعيف

-   بدون sudo


  
</details>


---
---


<img width="1620" height="212" alt="image" src="https://github.com/user-attachments/assets/bfc966df-be23-40de-a41e-ec2a49bf3bb7" />









  
</details>







<details>
  <summary>Practical: Command Injection (Deploy)</summary>


```
;ls
```


<img width="1306" height="523" alt="image" src="https://github.com/user-attachments/assets/d7e7cbc2-31df-45d0-9786-0907306dc47f" />

```
;cat /home/tryhackme/flag.txt
```

<img width="1579" height="701" alt="image" src="https://github.com/user-attachments/assets/1081c2f8-94f8-402f-b37f-b6e6d2f193f4" />



```
THM{COMMAND_INJECTION_COMPLETE}
```







  
</details>



























































































