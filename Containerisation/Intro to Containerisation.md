# Intro to Containerisation

<img width="1903" height="378" alt="image" src="https://github.com/user-attachments/assets/e90bf946-235d-4de2-b53a-9ba58844b30c" />


----



<details>
  <summary>What is Containerisation</summary>


📝 ملخص Intro to Containerisation
=================================

### 1️⃣ يعني إيه Container؟

هو عبارة عن **حزمة (Package)** فيها:

-   البرنامج (Application)
-   المكتبات (Libraries)
-   الـ Packages
-   الإعدادات (Configurations)
-   أي ملفات البرنامج محتاجها

كل ده بيتحط في صندوق واحد اسمه **Container**.

* * * * *

### 2️⃣ ليه بنستخدم Containers؟

عشان نتجنب مشاكل زي:

-   اختلاف إصدارات البرامج (زي Python 3.8 و Python 3.12).
-   صعوبة تثبيت الـ Dependencies.
-   تعارض البرامج مع بعضها.
-   البرنامج يشتغل على جهاز ويعطل على جهاز تاني.

* * * * *

### 3️⃣ يعني إيه Containerisation؟

هي عملية **تجميع البرنامج وكل احتياجاته داخل Container** بحيث يشتغل بنفس الطريقة على أي جهاز فيه محرك تشغيل الـ Containers.

* * * * *

### 4️⃣ Docker هو إيه؟

Docker هو **Container Engine**.

يعني البرنامج المسؤول عن:

-   إنشاء Containers.
-   تشغيلها.
-   إيقافها.
-   إدارتها.

* * * * *

### 5️⃣ يعني إيه Isolation؟

كل Container بيشتغل في **بيئة مستقلة** عن باقي الـ Containers.

يعني:

-   كل واحد له ملفاته.
-   له المكتبات بتاعته.
-   له العمليات (Processes) الخاصة بيه.

ومش بيتعارض مع غيره.

* * * * *

### 6️⃣ Namespace يعني إيه؟

هي ميزة موجودة في **Linux Kernel**.

وظيفتها إنها تخلي كل Container يحس إنه لوحده على الجهاز، وميشوفش عمليات أو موارد الـ Containers التانية.

* * * * *

### 7️⃣ الفايدة الأمنية؟

لو برنامج داخل Container اتعرض لاختراق، ففي أغلب الحالات الضرر بيكون محصور داخل نفس الـ Container بسبب العزل (Isolation).


<img width="1198" height="960" alt="image" src="https://github.com/user-attachments/assets/15027e66-8b93-4dde-9bb6-65158bf1f46e" />


* * * * *





### 8️⃣ الفرق بين Container و Virtual Machine

| Container | Virtual Machine |
| --- | --- |
| خفيف وسريع | أثقل وأبطأ |
| بيشارك نفس الـ Kernel | لكل VM نظام تشغيل كامل |
| يستهلك RAM وCPU أقل | يستهلك موارد أكتر |
| يبدأ في ثوانٍ | قد يستغرق وقتًا أطول للإقلاع |





> ## **A Container is an isolated package that contains an application and everything it needs to run consistently on any system with a container engine like Docker.**




<img width="1661" height="336" alt="image" src="https://github.com/user-attachments/assets/26bc450b-14a3-49c1-ae14-4d059fe3fb45" />

  
</details>



<details>
  <summary>Introducing Docker</summary>


📝 Summary -- Introduction to Docker
===================================

1️⃣ Docker
----------

-   Docker هو أشهر **Containerization Platform**.
-   مفتوح المصدر (Open Source).
-   وظيفته إنشاء وتشغيل وإدارة الـ **Containers** بسهولة.

* * * * *

2️⃣ Docker Ecosystem
--------------------

هو مجموعة الأدوات الخاصة بـ Docker، مثل:

-   Docker Engine
-   Docker Hub
-   Docker Compose
-   Docker Desktop

* * * * *

3️⃣ Image vs Container ⭐ (أهم نقطة)
-----------------------------------

### Image

-   عبارة عن **قالب (Template)** أو نسخة جاهزة للتطبيق.
-   لا تعمل بمفردها.
-   يمكن تنزيلها أو مشاركتها.

### Container

-   هو **نسخة شغالة (Running Instance)** من الـ Image.
-   يحتوي على التطبيق وكل الـ Dependencies الخاصة به.

> **Image → docker run → Container**

* * * * *

4️⃣ Docker Engine
-----------------

هو **قلب Docker**.

وظيفته:

-   تشغيل الـ Containers.
-   التواصل مع نظام التشغيل.
-   إدارة الموارد مثل:
    -   CPU
    -   RAM
    -   Network
    -   Disk

* * * * *

5️⃣ Docker Pull
---------------

الأمر:

```
docker pull <image-name>
```

وظيفته:

-   تنزيل Image من الإنترنت (مثل Docker Hub).

* * * * *

6️⃣ Docker يستطيع
-----------------

-   تشغيل Containers.
-   ربط عدة Containers معًا.
-   نقل الملفات بين الجهاز والـ Container.
-   استيراد (Import) وتصدير (Export) الـ Images.

* * * * *

7️⃣ YAML
--------

لغة بسيطة لكتابة تعليمات Docker.

تُستخدم لوصف:

-   ما هي الـ Containers المطلوبة.
-   أي Images يتم استخدامها.
-   كيف تتواصل الـ Containers مع بعضها.

* * * * *

8️⃣ Portability
---------------

بفضل ملفات YAML وDocker Images:

-   نفس التطبيق يعمل بنفس الطريقة على أي جهاز عليه Docker.
-   يقلل مشاكل اختلاف بيئات التشغيل (Works on my machine).

* * * * *

9️⃣ Orchestration
-----------------

تعني إدارة وتشغيل أكثر من Container معًا.

مثال:

```
Web Server Container
        │
        │
Database Container
        │
        │
Cache Container
```

Docker يجعلهم يتواصلون ويعملون كتطبيق واحد.

* * * * *

🎯 الكلمات المفتاحية 
=====================================

| المصطلح | المعنى |
| --- | --- |
| **Docker** | منصة لإدارة وتشغيل Containers |
| **Docker Engine** | المحرك الذي يشغل الـ Containers |
| **Image** | قالب أو نسخة جاهزة للتطبيق |
| **Container** | نسخة شغالة من الـ Image |
| **Docker Pull** | تنزيل Image |
| **YAML** | لغة كتابة إعدادات Docker |
| **Orchestration** | إدارة عدة Containers معًا |
| **Docker Ecosystem** | مجموعة أدوات Docker |

* * * * *

🧠 الخريطة الذهنية 
===========================

```
Docker
│
├── Docker Engine
│      │
│      ├── Runs Containers
│      ├── Uses CPU/RAM/Disk/Network
│      └── Communicates with OS
│
├── Image
│      │
│      └── docker run
│              │
│              ▼
│         Container
│
├── YAML
│      │
│      └── Defines how Containers are built and run
│
└── Orchestration
       │
       └── Connects multiple Containers together
```


---



<img width="1665" height="363" alt="image" src="https://github.com/user-attachments/assets/7ca5a452-6e00-4808-8575-1388041472ca" />











  
</details>


<details>
  <summary>The History of Docker</summary>



1️⃣ Docker ظهر سنة **2013**.
----------------------------

-   تم تطويره بواسطة **Solomon Hykes**.

* * * * *

2️⃣ بدأ كمشروع داخلي داخل شركة:
-------------------------------

-   **dotCloud** (شركة كانت تقدم خدمات PaaS).

* * * * *

3️⃣ بعد عرضه في مؤتمر **PyCon 2013**:
-------------------------------------

-   تم إصداره كمشروع **Open Source**.
-   ومن وقتها بدأ ينتشر بسرعة.

* * * * *

4️⃣ Docker لم يخترع الـ Containerization.
-----------------------------------------

-   فكرة الـ **Containerization** موجودة من سنة **1979** في **Unix V7**.
-   لكن Docker هو اللي جعل استخدامها سهلًا وشائعًا.

* * * * *

5️⃣ سبب نجاح Docker
-------------------

لأنه:

-   سهل الاستخدام.
-   مجاني ومفتوح المصدر.
-   يعمل على أنظمة تشغيل مختلفة.
-   سهّل تشغيل ومشاركة التطبيقات داخل Containers.

* * * * *

6️⃣ Docker أصبح أشهر منصة للـ Containers.
-----------------------------------------

وأشهر مكان لتحميل الـ Images هو:

**Docker Hub**.


> ## **`Containerization started in 1979, but Docker (2013) made it simple, accessible, and popular.`**





  
</details>



<details>
  <summary>The Benefits & Features of Docker</summary>


مميزات Docker
-------------

### ✅ مجاني

-   Open Source.
-   يمكن تنزيله واستخدامه ومشاركة الـ Images مجانًا.

* * * * *

### ✅ متوافق

-   يعمل على **Linux وWindows وmacOS**.
-   أي جهاز عليه Docker Engine يقدر يشغل نفس الـ Image.

* * * * *

### ✅ كفء وخفيف

-   لا يشغل نظام تشغيل كامل لكل Container.
-   يشارك نفس الـ Kernel.
-   يستهلك **CPU وRAM وDisk** أقل من الـ Virtual Machines.

* * * * *

### ✅ سهل التعلم

-   Documentation ممتازة.
-   أوامر بسيطة.
-   مجتمع كبير يساعد في حل المشاكل.

* * * * *

### ✅ سهل المشاركة

-   التطبيقات تُحفظ كـ **Images**.
-   يمكن رفعها أو تنزيلها من **Docker Hub** أو مشاركتها مع الآخرين.

* * * * *

### ✅ أكثر أمانًا

-   الـ Containers تحتوي فقط على ما يحتاجه التطبيق.
-   تقليل البرامج غير الضرورية يقلل احتمالية وجود ثغرات أمنية.

* * * * *

### ✅ أقل تكلفة

-   يحتاج موارد أقل من الـ Virtual Machines.
-   مناسب جدًا للتشغيل على خوادم Cloud صغيرة ومنخفضة التكلفة.








  
</details>


<details>
  <summary>How does Containerisation Work?</summary>




Namespace
---------

-   تقنية في Linux لعزل الموارد.
-   تفصل:
    -   Processes
    -   Files
    -   Memory
    -   Network

* * * * *

كل Process لها:
---------------

1.  Namespace
2.  PID (Process ID)

* * * * *

PID
---

-   رقم يميز كل Process.
-   PID 1 هو أول Process في النظام.
-   غالبًا يكون `systemd` في Linux الحديث.
-   مسؤول عن تشغيل وإدارة باقي العمليات.

* * * * *

Docker والـ Namespace
---------------------

-   كل Container يعمل داخل Namespace خاصة به.
-   Processes داخل Container ترى فقط العمليات في نفس Namespace.

* * * * *

Security Concept
----------------

-   العزل بالـ Namespace يحمي الـ Containers من بعضها.
-   لو حدث خطأ وارتبط Container بالـ Host Namespace:
    -   يمكن حدوث **Container Escape**.
    -   وقد يؤدي إلى رفع الصلاحيات.







> ## **`Containers are isolated using Linux namespaces. Each process has a PID and belongs to a namespace. If a container can access the host's namespace, it may escape the container and compromise the host.`**

<img width="863" height="715" alt="image" src="https://github.com/user-attachments/assets/438c958f-2fe5-4e2a-896d-6294a2d17423" />

  
</details>




<details>
  <summary>commands</summary>


| الأمر               | الوظيفة                   |
| ------------------- | ------------------------- |
| `docker pull`       | تحميل Image               |
| `docker run`        | تشغيل Container           |
| `docker image ls`   | عرض الـ Images الموجودة   |
| `ps`                | عرض Processes في Linux    |
| `docker compose up` | تشغيل عدة Containers معًا |

  
</details>


















































