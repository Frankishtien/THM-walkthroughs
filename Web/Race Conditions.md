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




  
</details>


















