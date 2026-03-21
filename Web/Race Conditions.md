# TryHackMe Race Conditions Walkthrough 

<img width="1903" height="356" alt="image" src="https://github.com/user-attachments/assets/2206a06d-cadc-4644-95b8-7a7e076c56f4" />

----


<details>
   <summary>Multi-Threading</summary>


🧠 أول حاجة: Program (البرنامج)
-------------------------------

ببساطة:\
**Program = كود مكتوب بس لسه متشغّلش**

📌 زي:

-   سكريبت Python
-   برنامج C
-   Flask app اللي انت شايفه

💡 التشبيه:

> الوصفة بتاعة القهوة ☕ → مجرد تعليمات\
> طول ما حد منفذهاش = مفيش قهوة

✔️ يعني:

-   Static (ساكن)
-   ملوش تأثير لحد ما يتشغل

* * * * *

⚙️ تاني حاجة: Process (العملية)
-------------------------------

**Process = البرنامج وهو شغال**

📌 لما تشغل البرنامج:

```
python app.py
```

💥 كده اتخلق Process

💡 التشبيه:

> انت بدأت فعليًا تعمل القهوة = بقيت في "process"

* * * * *

### 🔥 الـ Process بيبقى فيه إيه؟

1.  **Code** (البرنامج نفسه)
2.  **Memory** (متغيرات -- بيانات)
3.  **State (حالة)**

* * * * *

### 🌀 حالات الـ Process (مهم جدًا)

-   🆕 New → لسه اتخلق
-   ⏳ Ready → مستني CPU
-   🏃 Running → شغال دلوقتي
-   ⏸ Waiting → مستني حاجة (زي request)
-   ❌ Terminated → خلص

💡 مثال Flask:

-   مستني request → Waiting
-   جاله request → Ready
-   بدأ ينفذ → Running
-   رجع يستنى → Waiting

* * * * *

🧵 تالت حاجة: Thread
--------------------

**Thread = جزء صغير جوه الـ Process بيشتغل**

📌 يعني:

-   Process واحد
-   جواه Threads كتير

💡 التشبيه:

> ماكينة القهوة = Process\
> كل portafilter = Thread

* * * * *

### الفرق المهم:

| Process | Thread |
| --- | --- |
| تقيل (Heavy) | خفيف (Lightweight) |
| ليه memory لوحده | بيشارك memory |
| أبطأ | أسرع |

* * * * *

🚀 Multi-threading (الجامد بقى 😎)
----------------------------------

**إنك تشغل أكتر من Thread في نفس الوقت**

* * * * *

### 💥 حالتين مهمين:

#### ❌ Serial (واحد ورا التاني)

```
User1 → User2 → User3
```

🐢 بطيء

* * * * *

#### ✅ Parallel (multi-threading)

```
User1 + User2 + User3 في نفس الوقت
```

⚡ سريع

* * * * *

🌐 مثال عملي (Flask / Server)
-----------------------------

### 🐢 بدون Threads:

-   request واحد بس
-   الباقي واقف مستني

* * * * *

### ⚡ مع Threads:

-   كل request ليه thread
-   السيرفر يخدم ناس كتير مع بعض

* * * * *

🔥 Gunicorn بقى (level أعلى)
----------------------------

```
gunicorn --workers=4  --threads=2
```

### معناها:

-   4 Processes (Workers)
-   كل واحد فيهم فيه 2 Threads

📌 يعني:

```
Total threads = 4 × 2 = 8
```

💥 يقدر يخدم 8 requests في نفس الوقت

* * * * *

⚠️ نقطة مهمة جدًا (تيجي في الامتحان)
------------------------------------

> ❗ مينفعش Processين يشتغلوا على نفس الـ Port

يعني:

```
0.0.0.0:8080
```

Process واحد بس اللي يمسكه

* * * * *

🧠 الزتونة 💣
-------------------

-   Program → كود بس
-   Process → كود شغال
-   Thread → Task صغيرة جوه process
-   Multi-threading → أكتر من task في نفس الوقت


<img width="1359" height="342" alt="image" src="https://github.com/user-attachments/assets/35fd0650-d15e-4c29-89e6-51a60b8ca154" />




</details>




<details>
  <summary>Race Conditions</summary>


🧠 يعني إيه Race Condition؟
---------------------------

**Race Condition = سباق بين Threads / Processes على نفس الحاجة**

📌 يعني:

> مين يوصل الأول ويغير الـ data؟

💡 مثال المطعم:

-   اتنين hosts
-   نفس الترابيزة (resource)
-   الاتنين شايفينها فاضية
-   الاتنين حجزوها 😅

💥 النتيجة:

> **Conflict / Bug / Unexpected behavior**

* * * * *

⚠️ ليه ده بيحصل؟
----------------

بسبب:

-   أكتر من Thread شغالين في نفس الوقت
-   بيستخدموا **Shared Resource** (زي variable / database / file)
-   مفيش **synchronization**

* * * * *

💣 أخطر نوع: TOCTOU
-------------------

**Time Of Check → Time Of Use**

📌 يعني:

1.  البرنامج "يتأكد" من حاجة
2.  قبل ما يستخدمها → حد يغيرها

💥 فيحصل exploit

* * * * *

💰 مثال البنك (A)
-----------------

-   الرصيد = 100
-   Thread 1 يسحب 45
-   Thread 2 يسحب 35

المشكلة:

-   الاتنين شافوا 100 😅

💥 النتيجة:

-   الحساب غلط
-   الفلوس اتسحبت مرتين بس الحساب متخصمش صح

* * * * *

💥 مثال أخطر (B)
----------------

-   الرصيد = 75
-   Thread 1 يسحب 50 ✅
-   Thread 2 يسحب 50 ❌ (المفروض يترفض)

لكن:

-   Thread 2 شاف الرصيد قبل ما يتحدث 😈

💣 النتيجة:

> سحب فلوس مش موجودة أصلاً → **Vulnerability حقيقية**

* * * * *

🧵 مثال الكود (Python)
----------------------


```
import threading

x = 0  # Shared variable

def increase_by_10():
    global x
    for i in range(1, 11):
        x += 1
        print(f"Thread {threading.current_thread().name}: {i}0% complete, x = {x}")

# Create two threads
thread1 = threading.Thread(target=increase_by_10, name="Thread-1")
thread2 = threading.Thread(target=increase_by_10, name="Thread-2")

# Start the threads
thread1.start()
thread2.start()

# Wait for both threads to finish
thread1.join()
thread2.join()

print("Both threads have finished completely.")

```

## **`First output`**

<img width="1577" height="532" alt="image" src="https://github.com/user-attachments/assets/d9eaa64b-debd-40a1-8e41-27c2bf1266d8" />

## **`Second output`**

<img width="1583" height="553" alt="image" src="https://github.com/user-attachments/assets/b1490adb-117f-4ab5-9244-202f9c89a01c" />





### المشكلة هنا:

-   Thread 1 و Thread 2 بيعدلوا نفس المتغير `x`
-   مفيش lock

* * * * *

### 💥 ليه النتايج بتختلف كل مرة؟

لأن:

-   CPU بيقرر مين يشتغل امتى
-   مفيش ترتيب ثابت

📌 مرة:

-   Thread-1 يسبق

📌 مرة:

-   Thread-2 يسبق

💣 اسم ده:

> **Non-deterministic behavior**

* * * * *

🔥 السبب الحقيقي
----------------

### 1\. Shared Resource

زي:

-   variable (x)
-   database row
-   file

* * * * *

### 2\. Parallel Execution

-   requests جاية مع بعض
-   threads شغالة مع بعض

* * * * *

### 3\. Database مشاكل

زي:

```
read → modify → write
```

💥 لو اتنين عملوها مع بعض:

-   البيانات تضرب

* * * * *

### 4\. Third-party APIs

-   لو مش معمولة thread-safe
-   ممكن يحصل race condition

* * * * *

😈 من ناحية Pentesting بقى (المهم 💣)
-------------------------------------

Race Condition بتستخدم في:

### 🔓 1. Double Spending

-   تسحب فلوس مرتين
-   تستخدم coupon أكتر من مرة

* * * * *

### 🔑 2. Bypass Authentication

-   تغير password أو token قبل ما يتشيك

* * * * *

### 🎟 3. Coupon Abuse

-   تستخدم نفس الكوبون 100 مرة

* * * * *

### 📦 4. Inventory bugs

-   تشتري حاجة خلصانة already

* * * * *

🛡 الحل (مهم تفهمه عشان تعرف تكسره 😎)
--------------------------------------

### ✅ 1. Locks (Mutex)

```
lock.acquire()\
# critical section\
lock.release()
```

* * * * *

### ✅ 2. Database Transactions

-   isolation levels
-   row locking

* * * * *

### ✅ 3. Atomic Operations

-   العملية كلها تحصل مرة واحدة

* * * * *

🧠 الخلاصة 💣
--------------------------

-   Race Condition = Threads بتتخانق على data
-   TOCTOU = check يحصل → data تتغير قبل الاستخدام
-   السبب = shared resources + parallel execution
-   الخطر = فلوس، auth، logic كله يبوظ



<img width="1208" height="305" alt="image" src="https://github.com/user-attachments/assets/fcb9db5b-80c1-44ae-abd5-1dba580ab0c4" />


  
</details>




<details>
   <summary>Web Application Architecture</summary>


<details>
   <summary>thm content</summary>


Let's visit web application architecture to explain how race conditions are possible.

Client-Server Model
-------------------

Web applications follow a client-server model:

-   **Client**: The client is the program or application that initiates the request for a service. For example, when we browse a web page, our web browser requests the web page (file) from a web server.
-   **Server**: The server is the program or system that provides these services in response to incoming requests. For instance, the web server responds to an incoming HTTP `GET` request and sends an HTML page (or file) to the requesting web browser (client).

Generally speaking, the client-server model runs over a network. The client sends its request over the network, and the server receives it and processes it before sending back the required resource.

Typical Web Application
-----------------------

A web application follows a multi-tier architecture. Such architecture separates the application logic into different layers or tiers. The most common design uses three tiers:

-   **Presentation tier**: In web applications, this tier consists of the web browser on the client side. The web browser renders the HTML, CSS, and JavaScript code.
-   **Application tier**: This tier contains the web application's business logic and functionality. It receives client requests, processes them, and interacts with the data tier. It is implemented using server-side programming languages such as Node.js and PHP, among many others.
-   **Data tier**: This tier is responsible for storing and manipulating the application data. Typical database operations include creating, updating, deleting, and searching existing records. It is usually achieved using a database management system (DBMS); examples of DBMS include MySQL and PostgreSQL.


----------------------------------------------

![A web browser accessing a web server. The web server is connected to a database over the cloud.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/d81b4960e5e7d02aaf91b4d1f4c336c4.svg)

----------------------------------------------




States
------

Let's visit some examples from business logic before diving deeper. We will consider the following examples:

-   Validating and conducting money transfer
-   Validating coupon codes and applying discounts

### Validating and Conducting Money Transfer

Consider the example of transferring money to a friend or your other account. The program will progress as follows:

1.  The user clicks on the "Confirm Transfer" button
2.  The application queries the database to confirm that the account balance can cover the transfer amount
3.  The database responds to the query
    1.  If the amount is within the account limits, the application conducts the transaction
    2.  If the amount is beyond the account limits, the application shows an error message

![A flowchart for a program responsible for money transfers.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/a85571359f7d2a6df3f6135b3e49a069.svg)

In an ideal scenario, the code above leads to two program states:

-   Amount not sent
-   Amount sent

![A state diagram for program responsible for money transfers; it shows two states.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1750346443769.svg)

### Validating coupon codes and applying discounts

Let's consider the example of applying a discount coupon. The user goes to their shopping cart and adds a coupon to get a discount. The steps might be something along the following lines:

1.  The user enters a coupon code
2.  The application queries the database to determine whether the coupon code is valid and whether any constraints exist
3.  The database responds with validity and constraints
    1.  The discount is applied if the code is valid and there are no constraints on applying it for this user.
    2.  An error message is displayed if the code is invalid or there are constraints on applying it for this user.

![Example flowchart of the code responsible for applying a discount coupon.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f22358005dabe4be5ded6a16b7499ad.svg)

The above code leads to a few program states:

-   Coupon not applied
-   Coupon applied

![A state diagram for the code responsible for applying a discount coupon; it shows two states.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/d6787ffe68c2d225144344700326529d.svg)

Two States? Think Again
-----------------------

Let's continue our analysis of applying a discount coupon. Ideally, we expect two states: **Coupon not applied** and **Coupon applied**. However, this is too simplistic to depict real sophisticated scenarios. We can add an intermediary state: **Checking coupon applicability**.

![A state diagram for the code responsible for applying a discount coupon; it shows three states.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/0fc7e963d1e251b50b379d0a19039e6f.svg)

Depending on how the application is developed, we can expect more states. For example, **Checking coupon applicability** might involve two states: **Checking coupon validity** and **Checking coupon constraints**. A coupon might be valid, but existing constraints prevent it from being applied. Similarly, **Coupon applied** might be divided into two states, one of which is **Recalculating total**.

![A state diagram for the code responsible for applying a discount coupon; it shows five states.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/a39d6bea81fd850ce9e02f9fbfdc4d55.svg)

**Why is this important for race conditions?**

In the state diagram above, we can see that we pass through multiple states before the coupon is marked as applied. Let's draw the states on a time axis, as shown below.

![Timeline showing the different states that the application goes through as it applies a discount coupon.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/4bcf6a7618f9a140f0807ae590106b78.svg)

There is a time window between the instant we try to add a coupon and the instant where the coupon is marked as applied and cannot be applied again. As long as the coupon is not marked as applied, most likely, no controls prevent it from being accepted repeatedly. We might be able to apply it multiple times during this time window.

This situation is similar when considering the states for the program making a money transfer. Although ideally speaking, it would be two states, considering the business logic, we can easily update the diagram to include three states. The reason is that we expect some time spent checking the account balance and limits; although this amount of time might be brief, it is not zero. If we dig deeper, we can uncover more "hidden" states.

![A state diagram for program responsible for money transfers; it shows three states.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1750346478448.svg)

However, even if the web application is vulnerable, we still have one challenge to overcome: timing. Even in vulnerable applications, this "window of opportunity" is relatively short; therefore, exploiting it necessitates that our requests reach the server simultaneously. In practice, we aim to get our repeated requests to reach the server only milliseconds apart.

How can we get our duplicated requests to reach the server within this short window? We need a tool such as Burp Suite.


   
</details>



🌐 أول حاجة: Client-Server Model
================================

🧠 الفكرة ببساطة:
-----------------

-   **Client (المستخدم)** → بيبعت Request
-   **Server (السيرفر)** → يستقبل ويرد

📌 مثال:

```
GET /profile
```

-   المتصفح = Client
-   السيرفر = بيرجع HTML

* * * * *

💡 المهم هنا:
-------------

> كل Request = عملية (Process/Thread) على السيرفر

💥 يعني:\
لو بعت 10 requests مرة واحدة → السيرفر يشغلهم parallel

👀 وهنا يبدأ اللعب...

* * * * *

🏗️ تاني حاجة: Web Application Architecture
===========================================

🔥 3 Layers مهمين:
------------------

### 1️⃣ Presentation (Frontend)

-   Browser
-   HTML / CSS / JS

* * * * *

### 2️⃣ Application (Backend)

-   Node.js / PHP / Python
-   هنا الـ logic (المهم 👀)

* * * * *

### 3️⃣ Data (Database)

-   MySQL / PostgreSQL
-   تخزين البيانات

* * * * *

💣 السيناريو الحقيقي:
---------------------

```
User → Server → Database → Server → User
```

💥 أي race condition بيحصل غالبًا هنا:

> بين الـ **Application ↔ Database**

* * * * *

⚠️ النقطة الخطيرة: States
=========================

🧠 يعني إيه State؟
------------------

**State = المرحلة اللي السيستم فيها**

* * * * *

💰 مثال تحويل فلوس:
-------------------

### الشكل البسيط:

-   ❌ Not Sent
-   ✅ Sent

* * * * *

### لكن الحقيقة 👇

فيه State في النص:

-   🔍 Checking Balance
-   💸 Processing
-   ✅ Done

* * * * *

🎟 مثال الكوبون (الأخطر 🔥)
---------------------------

### الشكل البسيط:

-   ❌ Coupon not applied
-   ✅ Coupon applied

* * * * *

### الشكل الحقيقي 👇

1.  إدخال الكود
2.  🔍 Checking validity
3.  🔍 Checking constraints
4.  💰 Applying discount
5.  🔄 Recalculating total
6.  ✅ Done

* * * * *

💣 فين الـ Vulnerability بقى؟
=============================

👀 ركّز هنا:
------------

فيه **Time Window (فترة صغيرة جدًا)** بين:

```
Check → Apply
```

💥 خلال الفترة دي:

> الكوبون لسه "مش متسجل إنه اتستخدم"

* * * * *

😈 السيناريو الحقيقي:
---------------------

1.  تبعت Request apply coupon
2.  قبل ما يتسجل إنه اتطبق
3.  تبعت Request تاني
4.  وتالت... ورابع...

💣 النتيجة:

> تستخدم الكوبون 10 مرات 😎🔥

* * * * *

⚡ نفس الفكرة في الفلوس
======================

```
Check balance → Deduct
```

💥 لو لحقت تبعت request تاني قبل الخصم:

-   تسحب فلوس زيادة
-   تعمل Double Spending

* * * * *

🧠 ليه ده بيحصل؟
================

بسبب:
-----

-   فيه خطوات متعددة (States)
-   فيه وقت بينهم (حتى لو milliseconds)
-   مفيش locking أو protection

* * * * *

⏱️ التحدي الحقيقي
=================

> ⛔ الوقت صغير جدًا (milliseconds)

💡 يعني لازم:

-   requests توصل "تقريبًا في نفس اللحظة"

* * * * *

🔥 الحل كمهاجم
==================================

🛠 تستخدم:
----------

### 1️⃣ Burp Suite Intruder

-   تبعت requests كتير بسرعة

* * * * *

### 2️⃣ Turbo Intruder 💣 (الأقوى)

-   high-speed requests
-   concurrent threads

* * * * *

### 3️⃣ Repeater (manual)

-   تضغط send بسرعة (بدائي 😅)

* * * * *

💥 مثال عملي attack:
--------------------

```
POST /apply-coupon\
coupon=FREE100
```

📌 تبعت نفس الريكوست:

-   20 مرة في نفس الوقت

💣 ممكن:

-   الخصم يتطبق 20 مرة 😎






<img width="1626" height="471" alt="image" src="https://github.com/user-attachments/assets/4910631c-f7c8-4dd9-87e4-0a1f5dbc35c5" />

<details>
   <summary>explain op answers</summary>

🟢 السؤال الأول
===============

❓ ليه الـ Money Transfer في الأول كان **2 States**؟
---------------------------------------------------

### 💡 لأننا بصّينا بشكل بسيط جدًا:

-   ❌ **Amount not sent**
-   ✅ **Amount sent**

📌 يعني:

> يا الفلوس متبعتتش\
> يا اتبعتت وخلاص

💥 مفيش تفاصيل، مفيش خطوات وسط\
👉 مجرد **بداية ونهاية**

* * * * *

🔵 السؤال التاني
================

❓ ليه بقى **3 States** بعد التحديث؟
-----------------------------------

لأننا بدأنا نفكر بطريقة واقعية شوية 👀

### بقى عندنا:

1.  ❌ Not Sent
2.  🔍 **Checking balance / validation**
3.  ✅ Sent

* * * * *

### 💣 ليه زودنا State؟

لأن:

> العملية مش instant ❌\
> فيه وقت بيتقضي في:

-   checking balance
-   verifying limits

📌 وده مهم جدًا لأنه:

> هو ده المكان اللي بيحصل فيه Race Condition 😈

* * * * *

🔴 السؤال التالت (الأهم 🔥)
===========================

❓ ليه Coupon بقى **5 States**؟
------------------------------

هنا بقى دخلنا deep شوية 👀💣

* * * * *

💡 بدل ما نقول:
---------------

-   Not applied
-   Applied

❌ ده oversimplified جدًا

* * * * *

✅ الحقيقة:
----------

### العملية بتتفكك كده:

1.  ❌ Coupon not applied
2.  🔍 Checking validity
3.  🔍 Checking constraints
4.  💰 Applying discount
5.  🔄 Recalculating total
6.  ✅ Applied

* * * * *

🤔 طب ليه الإجابة 5 مش 6؟
-------------------------

لأن:

-   أحيانًا "Applied" بتتدمج مع آخر step
-   أو بنعد بس الحالات الانتقالية الأساسية

📌 المهم:

> فيه **حوالي 5 حالات فعلية واضحة** في الـ logic

   
</details>






   
</details>








<details>
   <summary>Conditions Race Exploiting</summary>




These are the credentials for two users:

-   User1: `07799991337`
-   Password: `pass1234`

And

-   User2: `07113371111`
-   Password: `pass1234`



<img width="1902" height="778" alt="image" src="https://github.com/user-attachments/assets/d3f48474-21b8-46de-99b0-35ade1cba18c" />


## now we will send money to second user 

<img width="1919" height="712" alt="image" src="https://github.com/user-attachments/assets/9b077d84-82f0-495e-9cca-0d1b0d12e843" />

## catch the request using burp

> ## send it to repeater and click
> - `ctrl` + `R` 20 times
> - create group and put these requests in it

<img width="1919" height="674" alt="image" src="https://github.com/user-attachments/assets/2626f55d-a97a-4f15-b643-ce56d0572c0a" />

## click send group in pariall 

<img width="1818" height="522" alt="image" src="https://github.com/user-attachments/assets/9dd3ef3f-ba59-4531-8074-89aaed1c485f" />

## look at my palance now found 

<img width="1916" height="486" alt="image" src="https://github.com/user-attachments/assets/28707c8b-6bcf-4924-ab3b-219c6205844c" />

## check the other user 

<img width="1912" height="466" alt="image" src="https://github.com/user-attachments/assets/985cff94-67c1-43a9-8366-c93a5bd29b62" />


   
</details>



<details>
   <summary>Detection and Mitigation</summary>

🔍 أولًا: Detection (إزاي نكتشف Race Condition)
===============================================

🧠 من وجهة نظر الشركة (Business Owner)
--------------------------------------

الموضوع صعب جدًا 👀

📌 ليه؟

-   الـ Race Condition بيحصل في **milliseconds**
-   ممكن يحصل مرة أو مرتين بس
-   مش بيكسر السيستم بشكل واضح

* * * * *

💡 مثال:
--------

-   User استخدم coupon مرتين بدل مرة
-   أو سحب فلوس زيادة

👀 ده ممكن يعدي عادي جدًا\
لو محدش راجع logs كويس

* * * * *

⚠️ الخطر الحقيقي:
-----------------

> ❗ مش كل Race Condition بيبان

💥 ممكن:

-   يعدّي تحت الرادار
-   يفضل شغال شهور
-   حد يستغله بهدوء 😈

* * * * *

🧠 دورك كـ Pentester بقى (المهم 🔥)
===================================

🎯 1. افهم السيستم الطبيعي
--------------------------

يعني:

-   لما تعمل action → إيه اللي بيحصل؟
-   إيه الرد الطبيعي؟
-   إيه القيود؟

* * * * *

📌 أمثلة constraints:
---------------------

-   ✔️ use once
-   ✔️ vote once
-   ✔️ limit to balance
-   ✔️ مرة كل 5 دقايق

* * * * *

🎯 2. حاول تكسر القيود دي
-------------------------

👀 اسأل نفسك:

> "إزاي أخلي السيستم يصدق حاجة غلط؟"

* * * * *

🎯 3. فكّر بالـ States
----------------------

زي ما شوفنا:

```
Check → Process → Update
```

💥 فيه window بينهم

👉 هنا تضرب

* * * * *

🛠️ أدواتك:
-----------

-   Burp Repeater
-   Turbo Intruder 💣
-   Scripts (Python threads)

* * * * *

💣 مثال تفكير صح:
-----------------

لو فيه:

```
limit: use once
```

👀 تعمل:

-   تبعت 20 request في نفس اللحظة

💥 السيستم يقول:

> "كلهم valid" 😎

* * * * *

🛡️ تاني جزء: Mitigation (الحماية)
==================================

ده مهم تفهمه عشان:

> تعرف السيستم المفروض يتأمن إزاي → وبالتالي تكسره 😈

* * * * *

🔐 1. Synchronization (Locks)
-----------------------------

### الفكرة:

-   واحد بس يدخل على resource في نفس الوقت

```
lock.acquire()\
# critical code\
lock.release()
```

💡 زي:

> باب عليه قفل → واحد بس يدخل

* * * * *

💣 لو مفيش Lock:
----------------

-   threads تتخانق
-   race condition يحصل

* * * * *

⚛️ 2. Atomic Operations
-----------------------

### الفكرة:

> العملية كلها تحصل مرة واحدة بدون interruption

📌 يعني:

```
Check + Update = خطوة واحدة
```

💥 مفيش فرصة لحد يتدخل في النص

* * * * *

💰 مثال:
--------

بدل:

```
Check balance → Deduct
```

يبقى:

```
Deduct if balance >= amount (atomic)
```

* * * * *

🗄️ 3. Database Transactions
----------------------------

### الفكرة:

-   مجموعة operations = وحدة واحدة

📌 يا:

-   كلها تنجح
-   أو كلها تفشل

* * * * *

### 💡 مثال:

```
BEGIN;\
SELECT balance;\
UPDATE balance;\
COMMIT;
```

* * * * *

💥 الفايدة:
-----------

-   يمنع:
    -   inconsistent data
    -   race conditions

   
</details>


<details>
   <summary>Challenge Web App</summary>



**Name: Rasser Cond**

-   Username: `4621`
-   Password: `blueApple`

**Name: Zavodni Stav**

-   Username: `6282`
-   Password: `whiteHorse`

**Name: Warunki Wyscigu**

-   Username: `9317`
-   Password: `greenOrange`


<img width="1919" height="550" alt="image" src="https://github.com/user-attachments/assets/61166610-6580-48c8-b7c3-a5f1d34c652d" />

## choose one of them notice that there is amount of money website will have it 

<img width="993" height="623" alt="image" src="https://github.com/user-attachments/assets/294fa440-41b8-4e5f-897e-a333cd6cbb84" />

## send request to repeater and do same steps

<img width="1919" height="696" alt="image" src="https://github.com/user-attachments/assets/a8407eeb-9747-4b79-9107-276d7f4036a4" />

<img width="1919" height="688" alt="image" src="https://github.com/user-attachments/assets/1c266a7f-ad9d-4e57-b804-b5895889b255" />


## now let's check **`Zavodni Stav`** account 

<img width="845" height="233" alt="image" src="https://github.com/user-attachments/assets/4690bbe0-5bf4-453d-94e4-b86566624511" />



   
</details>
































