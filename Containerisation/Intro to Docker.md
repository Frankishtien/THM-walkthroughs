# Intro to Docker

<img width="1902" height="373" alt="image" src="https://github.com/user-attachments/assets/910487df-0fe2-4b16-9183-f3dd17d41403" />
 
---

<details>
  <summary>Basic Docker Syntax</summary>




Docker Pull
===========

قبل ما نشغل Container لازم ننزل Image.

عشان كده بنستخدم:

```
docker pull nginx
```

Docker بيروح للـ Registry ويجيب Image اسمها nginx.

* * * * *

Registry ده إيه؟
----------------

زي GitHub ولكن للـ Images.

أشهر Registry:

Docker Hub

لما تكتب:

```
docker pull nginx
```

Docker فعلياً بيروح لـ Docker Hub ويحمل الصورة.

* * * * *

ماذا يحدث أثناء التحميل؟
------------------------

```
docker pull nginx
```

هتشوف:

```
Pulling from library/nginx
```

معناها:

```
Docker Hub
   ↓
nginx repository
   ↓
تحميل الملفات
```

* * * * *

Tag
===

دي مهمة جداً.

كل Image لها:

```
name:tag
```

مثال:

```
ubuntu:22.04
```

-   ubuntu = اسم الصورة
-   22.04 = النسخة

* * * * *

مثال:

```
docker pull ubuntu:22.04
```

يعني:

> نزل Ubuntu إصدار 22.04

* * * * *

مثال آخر:

```
docker pull ubuntu:20.04
```

يعني:

> نزل Ubuntu إصدار 20.04

* * * * *

latest يعني إيه؟
----------------

لو كتبت:

```
docker pull ubuntu
```

Docker يفترض:

```
docker pull ubuntu:latest
```

يعني:

> نزل أحدث إصدار.

لكن في الواقع العملي ناس كتير بتتجنب latest لأن تحديث جديد ممكن يكسر التطبيق.

* * * * *

docker image
============

ده قسم إدارة الصور.

لما تكتب:

```
docker image
```

هيقولك الأوامر المتاحة.

زي:

```
ls
pull
rm
build
```

* * * * *

docker image ls
===============

حرفياً:

```
list images
```

يعني:

> اعرض كل الصور الموجودة عندي.

* * * * *

مثال:

```
docker image ls
```

الناتج:

```
REPOSITORY   TAG      IMAGE ID
ubuntu       22.04    2dc39ba059dc
nginx        latest   2b7d6430f78d
```

* * * * *

REPOSITORY
----------

اسم الصورة.

```
ubuntu
nginx
```

* * * * *

TAG
---

الإصدار.

```
22.04
latest
```

* * * * *

IMAGE ID
--------

معرف فريد للصورة.

```
2dc39ba059dc
```

كل Image لها ID خاص.

زي بصمة.

* * * * *

SIZE
----

حجم الصورة.

مثلاً:

```
77.8 MB
```

أو

```
142 MB
```

* * * * *

docker image rm
===============

لو عاوز تحذف Image.

مثال:

```
docker image rm ubuntu:22.04
```

Docker هيحذف الصورة.

* * * * *

ليه كتبنا:

```
ubuntu:22.04
```

وليس:

```
ubuntu
```

لأن ممكن يكون عندك:

```
ubuntu:18.04
ubuntu:20.04
ubuntu:22.04
```

Docker لازم يعرف أنهي نسخة يحذف.

* * * * *

بعد الحذف
---------

لو كتبت:

```
docker image ls
```

هتلاقي:

```
nginx
```

فقط.

* * * * *

ملخص سريع
=========

تحميل صورة
----------

```
docker pull nginx
```

* * * * *

تحميل إصدار معين
----------------

```
docker pull ubuntu:22.04
```

* * * * *

عرض الصور الموجودة
------------------

```
docker image ls
```

* * * * *

حذف صورة
--------

```
docker image rm ubuntu:22.04
```



  
</details>





<details>
  <summary>Running Your First Container</summary>


الصورة الكبيرة الأول
====================

إحنا اتفقنا:

```
Image = Blueprint
Container = Running Instance
```

يعني لو عندك:

```
nginx image
```

فدي مجرد ملفات وتعليمات.

لما تعمل:

```
docker run nginx
```

Docker بيقول:

> هبني Container من الـ Image دي وأشغلها.

* * * * *

docker run
==========

الصيغة العامة:

```
docker run [OPTIONS] IMAGE_NAME [COMMAND]
```

مثال:

```
docker run ubuntu
```

Docker هيعمل Container من Ubuntu Image.

* * * * *

مثال عملي
=========

```
docker run -it ubuntu /bin/bash
```

تعالى نفكها.

* * * * *

docker run
----------

يعني:

```
أنشئ وشغل Container
```

* * * * *

-it
---

دي أهم Option للمبتدئين.

مكونة من جزئين:

### i

Interactive

يعني:

```
اسمحلي أتعامل مع الـ Container
```

* * * * *

### t

TTY

يعني:

```
افتح Terminal
```

* * * * *

لذلك:

```
-it
```

=

```
افتحلي Terminal أتعامل منه مع الـ Container
```

* * * * *

ubuntu
------

اسم الـ Image.

* * * * *

/bin/bash
---------

الأمر اللي هيتنفذ جوه الـ Container.

يعني:

```
شغل Bash Shell
```

* * * * *

لذلك:

```
docker run -it ubuntu /bin/bash
```

معناها:

```
1\. ابني Container من Ubuntu
2. افتح Bash
3. اديني Terminal أتفاعل معاه
```

* * * * *

هتشوف:

```
root@30eff5ed7492:/#
```

* * * * *

هو الرقم ده إيه؟
================

```
30eff5ed7492
```

ده:

```
Container ID
```

كل Container ليه ID خاص.

زي:

```
VM ID
```

تقريبًا.

* * * * *

Option رقم 1
============

-d
--

Detached Mode

مثال:

```
docker run -d nginx
```

* * * * *

بدل ما يفتحلك Terminal

هيشتغل في الخلفية.

زي:

```
Windows Service
```

تقريبًا.

* * * * *

مفيد جدًا مع:

```
Web Servers
Databases
APIs
```

* * * * *

Option رقم 2
============

--name
------

افترض عملت:

```
docker run nginx
```

Docker هيطلع اسم غريب:

```
happy_panda
angry_cat
```

أسماء عشوائية.

* * * * *

بدل كده:

```
docker run --name myweb nginx
```

هيبقى الاسم:

```
myweb
```

* * * * *

أريح بكتير.

* * * * *

Option رقم 3
============

--rm
----

مهمة جدًا.

مثال:

```
docker run --rm ubuntu
```

* * * * *

معناها:

```
لما الـ Container يقفل
امسحه تلقائياً
```

* * * * *

بدونها:

```
Container
↓
يقفل
↓
يفضل موجود
```

* * * * *

معها:

```
Container
↓
يقفل
↓
يتحذف
```

* * * * *

Option رقم 4
============

-p
--

دي مهمة جدًا.

* * * * *

تخيل:

جوه الـ Container

في Web Server شغال على:

```
Port 80
```

لكن جهازك مش شايفه.

* * * * *

لازم نربط Port من جهازك بPort من الـ Container.

* * * * *

مثال:

```
docker run -p 8080:80 nginx
```

معناها:

```
Host Port = 8080
Container Port = 80
```

* * * * *

الرسم:

```
Browser
   ↓
localhost:8080
   ↓
Docker
   ↓
Container:80
```

* * * * *

لذلك:

لو فتحت:

```
http://localhost:8080
```

هتشوف موقع Nginx.

* * * * *

ليه مكتوبة كده؟
---------------

```
8080:80
```

الصيغة:

```
Host:Container
```

* * * * *

يعني:

```
9999:80
```

=

```
Host Port 9999
→
Container Port 80
```

* * * * *

Option رقم 5
============

-v
--

Volume

دي من أهم الحاجات في Docker.

* * * * *

تخيل عندك ملف:

```
/home/user/index.html
```

وعايز الـ Container يشوفه.

* * * * *

تعمل:

```
docker run -v /home/user:/app nginx
```

* * * * *

المعنى:

```
Host
/home/user

↓

Container
/app
```

* * * * *

أي ملف تحطه هنا:

```
/home/user
```

هيظهر هنا:

```
/app
```

* * * * *

كأنك عملت:

```
Shared Folder
```

بين الجهاز والـ Container.

* * * * *

وده مهم جدًا مع:

```
Logs
Databases
Configs
Source Code
```

* * * * *

docker ps
=========

بعد ما تشغل Container

عايز تعرف إيه اللي شغال.

* * * * *

تكتب:

```
docker ps
```

* * * * *

مثال:

```
CONTAINER ID   IMAGE
a913a8f6e30f   nginx
```

* * * * *

يعني:

```
في Container شغال
```

* * * * *

المعلومات المهمة
================

CONTAINER ID
------------

```
a913a8f6e30f
```

رقم مميز للـ Container.

* * * * *

IMAGE
-----

```
nginx
```

اتبنى من أي Image.

* * * * *

STATUS
------

مثلاً:

```
Up 3 days
```

يعني:

```
شغال من 3 أيام
```

* * * * *

PORTS
-----

مثلاً:

```
0.0.0.0:8000->8000/tcp
```

يعني:

```
Host 8000
↓
Container 8000
```

* * * * *

NAMES
-----

اسم الـ Container.

* * * * *

docker ps -a
============

فرق مهم جدًا.

* * * * *

docker ps
---------

يعرض:

```
Running Containers Only
```

* * * * *

docker ps -a
------------

يعرض:

```
Running + Stopped Containers
```

* * * * *

مثال:

```
Container A (Running)
Container B (Stopped)
Container C (Stopped)
```

* * * * *

```
docker ps
```

هيظهر:

```
A
```

بس.

* * * * *

```
docker ps -a
```

هيظهر:

```
A
B
C
```


----

| الأمر                                | وظيفته                   |
| ------------------------------------ | ------------------------ |
| `docker run image`                   | تشغيل Container          |
| `docker run -it image /bin/bash`     | فتح Shell داخل Container |
| `docker run -d image`                | تشغيل بالخلفية           |
| `docker run --name myapp image`      | تسمية Container          |
| `docker run --rm image`              | حذف تلقائي بعد الإغلاق   |
| `docker run -p 8080:80 image`        | ربط Ports                |
| `docker run -v host:container image` | مشاركة ملفات             |
| `docker ps`                          | عرض الشغال فقط           |
| `docker ps -a`                       | عرض الكل                 |


----

```
docker run --rm
        ↓
يحذف Container فقط

docker image rm
        ↓
يحذف Image
```




طب لو عاوز أشغل Container قديم؟

أولًا اعرضه:

```
docker ps -a
```

مثلاً:

```
CONTAINER ID   NAMES
111111111111   myubuntu
```

شغله بـ:

```
docker start myubuntu
```

أو:

```
docker start 111111111111
```



  
</details>











<details>
  <summary>Intro to Dockerfiles</summary>


المشكلة اللي Dockerfile بيحلها
==============================

تخيل كل مرة عاوز تعمل Ubuntu Container لازم:

```
docker run -it ubuntu /bin/bash
apt update
apt install apache2
touch test.txt
mkdir app
...
```

كل مرة تعيد نفس الخطوات 😑

Docker قال:

> اكتب الخطوات مرة واحدة في ملف اسمه Dockerfile وأنا هبني Image جاهزة.

* * * * *

Dockerfile عبارة عن إيه؟
========================

ملف نصي عادي:

```
FROM ubuntu:22.04

RUN apt update

RUN apt install apache2 -y
```

كأنك بتكتب Recipe.

* * * * *

الفرق بين Dockerfile و Image
============================

```
Dockerfile
      ↓
docker build
      ↓
Image
      ↓
docker run
      ↓
Container
```

احفظ السلسلة دي لأنها قلب Docker كله.

* * * * *

أول Instruction
===============

FROM
----

لازم أي Dockerfile يبدأ بيها.

مثال:

```
FROM ubuntu:22.04
```

معناها:

```
ابدأ من Ubuntu 22.04
```

يعني أساس الـ Image هيبقى Ubuntu.

* * * * *

مثال:

```
FROM nginx
```

يبقى الأساس:

```
Nginx Image
```

* * * * *

RUN
===

شغل أمر أثناء بناء الـ Image.

مثال:

```
RUN whoami
```

أو

```
RUN apt update
```

أو

```
RUN touch test.txt
```

* * * * *

يعني Docker أثناء البناء ينفذ الأمر.

* * * * *

WORKDIR
=======

زي:

```
cd
```

في Linux.

مثال:

```
WORKDIR /
```

معناها:

```
روح للـ Root Directory
```

* * * * *

مثال:

```
WORKDIR /app
```

كأنك عملت:

```
cd /app
```

* * * * *

COPY
====

نسخ ملفات من جهازك للـ Container.

مثال:

```
COPY index.html /var/www/html
```

معناها:

```
Host
index.html

↓

Container
/var/www/html/index.html
```

* * * * *

CMD
===

دي مهمة جدًا.

بتحدد:

> لما الـ Container يشتغل يعمل إيه؟

* * * * *

مثال:

```
CMD ["bash"]
```

لما الـ Container يبدأ:

```
يشغل bash
```

* * * * *

مثال:

```
CMD ["apache2ctl","-D","FOREGROUND"]
```

يعني:

```
شغل Apache
```

* * * * *

EXPOSE
======

دي مش بتفتح البورت.

ركز في النقطة دي.

ناس كتير بتتلخبط.

* * * * *

```
EXPOSE 80
```

معناها:

```
هذا التطبيق يستخدم Port 80
```

بس.

* * * * *

لسه لازم تعمل:

```
docker run -p 80:80 image
```

عشان تقدر توصله.

* * * * *

أول Dockerfile
==============

```
FROM ubuntu:22.04

WORKDIR /

RUN touch helloworld.txt
```

* * * * *

تعالى نمشي سطر سطر.

* * * * *

السطر الأول
-----------

```
FROM ubuntu:22.04
```

جيب Ubuntu.

* * * * *

السطر الثاني
------------

```
WORKDIR /
```

ادخل Root Directory.

* * * * *

السطر الثالث
------------

```
RUN touch helloworld.txt
```

أنشئ ملف:

```
/helloworld.txt
```

* * * * *

النتيجة؟

Image جديدة فيها Ubuntu ومعاها ملف:

```
helloworld.txt
```

* * * * *

docker build
============

بعد ما كتبنا Dockerfile.

عاوزين نحوله Image.

* * * * *

الأمر:

```
docker build -t helloworld .
```

* * * * *

تعالى نفكها.

* * * * *

docker build
------------

ابني Image.

* * * * *

-t
--

Tag

يعني:

```
سمي الـ Image
```

* * * * *

helloworld
----------

اسم الـ Image.

* * * * *

.

دي مهمة جدًا.

معناها:

```
ابحث عن Dockerfile هنا
```

في الـ Current Directory.

* * * * *

ماذا يحدث أثناء البناء؟
=======================

Docker يقرأ الملف.

* * * * *

```
FROM ubuntu
```

ينزل Ubuntu.

* * * * *

```
WORKDIR /
```

يضبط الـ Directory.

* * * * *

```
RUN touch helloworld.txt
```

ينشئ الملف.

* * * * *

ثم ينتج Image.

* * * * *

ليه ظهر Ubuntu و Helloworld؟
============================

لأن:

```
helloworld
```

مبنية فوق:

```
ubuntu
```

* * * * *

الرسم:

```
ubuntu image
      ↓
add helloworld.txt
      ↓
helloworld image
```

* * * * *

Layer Concept
=============

دي أهم فكرة في Docker.

* * * * *

كل Instruction تقريبًا:

```
RUN
COPY
WORKDIR
```

بتنشئ Layer.

* * * * *

مثال:

```
FROM ubuntu
RUN apt update
RUN apt install apache2
RUN apt install net-tools
```

يبقى:

```
Layer 1
FROM

Layer 2
apt update

Layer 3
apache2

Layer 4
net-tools
```

* * * * *

كل Layer:

```
وقت
مساحة
حجم
```

* * * * *

تحسين الأداء
============

بدل:

```
RUN apt update
RUN apt install apache2
RUN apt install net-tools
```

نعمل:

```
RUN apt update &&\
    apt install apache2 -y &&\
    apt install net-tools -y
```

* * * * *

كده بقى:

```
Layer واحدة
```

بدل:

```
3 Layers
```

* * * * *

أسرع وأصغر.

* * * * *

مثال Apache Web Server
======================

```
FROM ubuntu:22.04

RUN apt-get update -y

RUN apt-get install apache2 -y

EXPOSE 80

CMD ["apache2ctl","-D","FOREGROUND"]
```

* * * * *

ترجمتها بالعربي:

```
1\. هات Ubuntu
2. اعمل Update
3. ثبت Apache
4. التطبيق بيستخدم Port 80
5. لما Container يشتغل شغل Apache
```

* * * * *

نبنيها:

```
docker build -t webserver .
```

* * * * *

نشغلها:

```
docker run -d --name webserver -p 80:80 webserver
```

* * * * *

تعالى نفك الأمر ده.

```
-d
```

خلفية.

* * * * *

```
--name webserver
```

اسم الـ Container.

* * * * *

```
-p 80:80
```

اربط:

```
Host 80
↓
Container 80
```

* * * * *

```
webserver
```

اسم الـ Image.

* * * * *

الرسم النهائي:

```
Dockerfile
      ↓
docker build
      ↓
webserver Image
      ↓
docker run
      ↓
webserver Container
      ↓
Apache Running
      ↓
Browser
```


  
</details>









<details>
  <summary>Intro to Docker Compose</summary>

لحد دلوقتي كنت شغال على **Container واحد** في كل مرة.

مثلاً:

```
docker run nginx
```

أو

```
docker run mysql
```

لكن في الواقع العملي التطبيقات مش بتبقى Container واحد.

* * * * *

المشكلة
=======

تخيل عندك موقع E-commerce.

الموقع محتاج:

```
Apache Web Server
+
MySQL Database
```

يعني محتاج Container للويب:

```
webserver
```

و Container للداتابيز:

```
database
```

* * * * *

الرسم:

```
Browser
    ↓
Apache Container
    ↓
MySQL Container
```

* * * * *

بدون Docker Compose
===================

لازم تعمل كل حاجة بنفسك:

إنشاء Network:

```
docker network create ecommerce
```

* * * * *

تشغيل Web Server:

```
docker run -d\
--name webserver\
--net ecommerce\
-p 80:80\
webserver
```

* * * * *

تشغيل Database:

```
docker run -d\
--name database\
--net ecommerce\
mysql
```

* * * * *

كل مرة تعيد نفس الكلام.

لو عندك:

```
Web
Database
Redis
Nginx
PHP
```

هتتعب 😅

* * * * *

الحل؟
=====

Docker Compose

* * * * *

فكر فيها كأنها:

```
docker run
docker run
docker run
docker run
```

كلهم متجمعين في ملف واحد.

* * * * *

يعني بدل:

```
docker run ...
docker run ...
docker run ...
```

تكتب:

```
docker-compose up
```

وخلاص.

* * * * *

Docker Compose عبارة عن إيه؟
============================

ملف:

```
docker-compose.yml
```

بيوصف:

```
كل الـ Containers
الـ Networks
الـ Volumes
الـ Ports
```

اللي مشروعك محتاجها.

* * * * *

مثال بسيط
=========

```
version: '3.3'

services:
  web:
    image: nginx

  db:
    image: mysql
```

* * * * *

معناها:

```
شغل Container اسمها web
من nginx image

وشغل Container اسمها db
من mysql image
```

* * * * *

أوامر Docker Compose
====================

* * * * *

تشغيل كل الخدمات
----------------

```
docker-compose up
```

* * * * *

بيعمل:

```
Build
Create
Start
```

لكل Containers.

* * * * *

إيقاف مؤقت
----------

```
docker-compose stop
```

* * * * *

معناها:

```
Containers موجودة
لكن متوقفة
```

* * * * *

تشغيل بعد التوقف
----------------

```
docker-compose start
```

* * * * *

معناها:

```
شغل الموجود
متبنيش جديد
```

* * * * *

حذف كل حاجة
-----------

```
docker-compose down
```

* * * * *

معناها:

```
Stop
Delete Containers
Delete Network
```

* * * * *

Build فقط
---------

```
docker-compose build
```

* * * * *

هيبني Images فقط.

* * * * *

نبدأ نفهم ملف docker-compose.yml
================================

* * * * *

version
-------

```
version: '3.3'
```

* * * * *

معناها:

```
صيغة Docker Compose المستخدمة
```

* * * * *

غالباً مش هتشغل بالك بيها.

* * * * *

services
========

دي أهم كلمة.

```
services:
```

معناها:

```
الخدمات اللي هتشتغل
```

* * * * *

بعدها نعرف كل Container.

* * * * *

مثال:

```
services:
  web:
```

* * * * *

يعني:

```
Container اسمها web
```

* * * * *

build
=====

مثال:

```
build: ./web
```

* * * * *

معناها:

```
Dockerfile موجود هنا
```

```
./web/Dockerfile
```

* * * * *

Docker يعمل:

```
docker build
```

تلقائياً.

* * * * *

image
=====

بديل لـ build.

مثال:

```
image: mysql:latest
```

* * * * *

معناها:

```
استخدم Image جاهزة
```

بدل ما تبنيها.

* * * * *

يعني:

```
web:
  build: ./web
```

=

```
ابني Image
```

* * * * *

أما:

```
database:
  image: mysql:latest
```

=

```
استخدم Image جاهزة
```

* * * * *

ports
=====

مثال:

```
ports:
  - '80:80'
```

* * * * *

زي:

```
docker run -p 80:80
```

* * * * *

المعنى:

```
Host Port 80
↓
Container Port 80
```

* * * * *

networks
========

مثال:

```
networks:
  - ecommerce
```

* * * * *

معناها:

```
الـ Container دي عضو في Network اسمها ecommerce
```

* * * * *

وده يسمح للـ Containers تكلم بعض.

* * * * *

الرسم:

```
ecommerce network

web
 ↓↑
database
```

* * * * *

بدون Network:

```
web  X  database
```

* * * * *

مش هيعرفوا يوصلوا لبعض.

* * * * *

environment
===========

دي متغيرات بيئة.

مثال:

```
environment:
  - MYSQL_DATABASE=ecommerce
```

* * * * *

معناها:

```
اسم قاعدة البيانات
ecommerce
```

* * * * *

مثال:

```
- MYSQL_ROOT_PASSWORD=helloworld
```

* * * * *

معناها:

```
باسورد root
```

* * * * *

الملف كامل
==========

```
version: '3.3'

services:

  web:
    build: ./web

    networks:
      - ecommerce

    ports:
      - '80:80'

  database:
    image: mysql:latest

    networks:
      - ecommerce

    environment:
      - MYSQL_DATABASE=ecommerce
      - MYSQL_USERNAME=root
      - MYSQL_ROOT_PASSWORD=helloworld

networks:
  ecommerce:
```

  
</details>



<details>
  <summary>Intro to the Docker Socket</summary>



أول حاجة
========

لما تثبت Docker

أنت مش بتثبت برنامج واحد.

أنت بتثبت:

```
Docker Client
+
Docker Server (Docker Daemon)
```

* * * * *

Docker Client
-------------

ده اللي أنت بتتعامل معاه.

لما تكتب:

```
docker ps
```

أو

```
docker run nginx
```

أنت بتكلم:

```
Docker Client
```

* * * * *

Docker Server
-------------

اسمه غالبًا:

```
dockerd
```

وده اللي بينفذ الشغل الحقيقي.

* * * * *

الرسم:

```
أنت
 ↓
Docker Client
 ↓
Docker Server (dockerd)
 ↓
Containers
Images
Networks
Volumes
```

* * * * *

طب الاتنين بيكلموا بعض إزاي؟
============================

عن طريق:

```
Socket
```

* * * * *

يعني إيه Socket؟
----------------

اعتبرها قناة اتصال.

زي ماسورة بين برنامجين.

* * * * *

مثال بسيط:

```
Chrome
   ↓
Socket
   ↓
Web Server
```

* * * * *

أو:

```
WhatsApp
   ↓
Socket
   ↓
WhatsApp Server
```

* * * * *

في Linux هتلاقي Socket عبارة عن File.

* * * * *

Docker Socket
=============

غالبًا موجود هنا:

```
/var/run/docker.sock
```

* * * * *

لو عملت:

```
ls -la /var/run/docker.sock
```

هتشوف حاجة شبه:

```
srw-rw----
```

الحرف:

```
s
```

معناه:

```
Socket
```

* * * * *

ماذا يحدث عند:
==============

```
docker run nginx
```

؟

* * * * *

أنت فعليًا مش بتكلم الـ Container.

* * * * *

العملية:

```
docker run nginx
      ↓
Docker Client
      ↓
docker.sock
      ↓
Docker Server
      ↓
Create Container
      ↓
Run Container
```

* * * * *

يعني الـ Client مجرد رسول.

* * * * *

والـ Server هو اللي بينفذ.

* * * * *

ليه بيقول Docker Server عبارة عن API؟
=====================================

لأن الـ Server فاتح API.

زي:

```
GET
POST
DELETE
```

* * * * *

لما تعمل:

```
docker ps
```

فالـ Client فعليًا بيبعت Request للـ API.

* * * * *

والـ Server يرد:

```
Container 1
Container 2
Container 3
```

* * * * *

عشان كده ممكن تستخدم curl
=========================

بدل:

```
docker ps
```

ممكن تكلم الـ API مباشرة.

مثلاً:

```
curl --unix-socket /var/run/docker.sock\
http://localhost/images/json
```

* * * * *

وده هيجيب:

```
كل الـ Images
```

* * * * *

يعني:

```
Docker CLI
```

مش الطريقة الوحيدة للتعامل مع Docker.

* * * * *

ممكن:

```
curl
Postman
Python Requests
أي HTTP Client
```

* * * * *

ليه ده مهم أمنيًا؟
==================

هنا الجزء الممتع 😈

* * * * *

لو حد قدر يوصل لـ:

```
/var/run/docker.sock
```

فهو تقريبًا بقى عنده تحكم كامل في Docker.

* * * * *

يعني يقدر:

```
List Containers
Start Containers
Stop Containers
Delete Containers
Create Containers
Mount Filesystems
```

* * * * *

ليه ده خطير جدًا؟
=================

تخيل Container فيها:

```
/var/run/docker.sock
```

ومتعملها Mount بالغلط:

```
docker run\
-v /var/run/docker.sock:/var/run/docker.sock
```

* * * * *

لو مهاجم دخل الـ Container.

فهو يقدر يكلم Docker API.

* * * * *

ويعمل Container جديدة بصلاحيات عالية.

* * * * *

مثال مشهور جدًا:

```
docker run -it\
-v /:/host\
ubuntu
```

* * * * *

المعنى:

```
اربط Root Filesystem بتاع الجهاز
داخل Container
```

* * * * *

بعدها المهاجم يقدر يشوف ملفات الجهاز نفسه.

* * * * *

عشان كده في الـ Pentest

لو لقيت:

```
/var/run/docker.sock
```

موجودة ومكشوفة.

بتبدأ تفكر فورًا في:

```
Docker Escape
Privilege Escalation
Host Compromise
```

* * * * *

نقطة أخيرة
==========

التاسك بيقول:

ممكن Docker Server يسمع على الشبكة.

* * * * *

بدل:

```
docker.sock
```

يبقى:

```
0.0.0.0:2375
```

مثلاً.

* * * * *

يعني أي جهاز يقدر يكلمه.

* * * * *

لو مش مؤمن كويس:

```
أي حد على الشبكة
↓
Docker API
↓
السيطرة على السيرفر
```

😬

عشان كده هتلاقي في الـ Shodan أحيانًا سيرفرات فاتحة:

```
Docker Remote API
```

ودي من الحاجات اللي الأمنيين بيقلقوا منها جدًا.


  
</details>




<details>
  <summary>Practical</summary>


```
docker ps 
```

<img width="1024" height="168" alt="image" src="https://github.com/user-attachments/assets/05d7c33b-a3b2-4c55-a44b-e9cc1832c23d" />


```
cat dockerfile
```


<img width="993" height="273" alt="image" src="https://github.com/user-attachments/assets/ddbd4488-5aeb-4668-b2dc-2abf60fad511" />



```
docker run -d --name webserver-container -p 80:80 webserver
```

<img width="1024" height="288" alt="image" src="https://github.com/user-attachments/assets/873109c6-dd26-4471-ac79-909193bcd383" />


## now visit 

```
https://10-114-133-58.reverse-proxy.cell-prod-eu-central-1c.vm.tryhackme.com/
```


<img width="1392" height="206" alt="image" src="https://github.com/user-attachments/assets/5f887045-9d71-4bc1-9b26-619ade0d878f" />


  
</details>













