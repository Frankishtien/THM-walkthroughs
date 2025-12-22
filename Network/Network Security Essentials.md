# Network Security Essentials

<img width="1908" height="361" alt="image" src="https://github.com/user-attachments/assets/c3f26f52-2114-4b70-a28b-01e1d64e12a5" />


---


[Network Security Essentials](https://tryhackme.com/room/networksecurityessentials)


<details>
  <summary>A Network - Overview</summary>


يعني إيه Network أصلاً؟
-----------------------

الشبكة مش مجرد أجهزة متوصلة ببعض،\
دي **منظومة متكاملة من أصول (Assets)** كل واحد ليه وظيفة، ولو واحد اتكسر → الباقي يتأثر.

من منظور **الأمن السيبراني**:

> لازم تفهم كل Component بيعمل إيه علشان تعرف\
> *الهجوم جاي منين* و *يتوقف إزاي*.

* * * * *

1️⃣ User Workstations (Endpoints)
---------------------------------

### إيه هي؟

-   أجهزة الموظفين (PCs -- Laptops)

-   أكتر نقطة بيتدخل منها المهاجم

### سيناريو هجوم

📧 Phishing Email\
→ موظف يفتح Attachment\
→ Malware يشتغل\
→ attacker دخل الشبكة

### ليه خطيرة؟

-   أقل مراقبة من السيرفرات

-   الموظف ممكن يكون Admin على جهازه

-   بداية **Lateral Movement**

### كـ Security بنبص على:

-   Endpoint Logs (Processes -- Services)

-   Network Traffic:

    -   اتصالات غريبة (C2 Server)

    -   IPs خارجية مش معروفة

* * * * *

2️⃣ File & Database Servers
---------------------------

### إيه وظيفتهم؟

-   File Server: ملفات الشركة

-   Database Server: بيانات حساسة (عملاء -- فلوس -- HR)

### ليه Target مهم؟

-   كنز الداتا 🏴‍☠️

-   Ransomware بيستهدفهم علطول

-   Data Exfiltration (سحب البيانات بهدوء)

### كـ Security نراقب:

-   Access Logs (مين فتح إيه وإمتى)

-   Unusual Downloads

-   Connections لبرا الشبكة

* * * * *

3️⃣ Application Servers (Web / Email / VPN)
-------------------------------------------

### أمثلة:

-   Web Server → موقع الشركة

-   Email Server → التواصل

-   VPN → دخول الموظفين عن بُعد

### ليه أخطرهم؟

-   **Externally Facing** (مكشوفين للإنترنت)

-   أي ثغرة = دخول مباشر

### هجمات شائعة:

-   SQL Injection على Web App

-   Brute Force على VPN / Email

-   Exploits بسبب Version قديم

### كـ Security نراقب:

-   Application Logs

-   Firewall Alerts

-   IDS/IPS Events

* * * * *

4️⃣ Active Directory (AD)
-------------------------

### هو إيه؟

-   عقل الشبكة 🧠

-   إدارة Users -- Groups -- Permissions

### ليه أهم حاجة؟

> لو AD وقع → الشبكة كلها وقعت

### أهداف المهاجم:

-   Privilege Escalation

-   Lateral Movement

-   Domain Admin

### إشارات خطر:

-   Failed Logins كتير (Password Spraying)

-   Login في وقت غريب

-   User بيaccess Systems مش بتاعته

* * * * *

5️⃣ Routers & Switches
----------------------

### وظيفتهم؟

-   Router: يوصّل الشبكة بالإنترنت

-   Switch: يوصّل الأجهزة ببعض

### ليه خطرهم كبير؟

لو اتاخدوا:

-   Man-in-the-Middle

-   Traffic Manipulation

-   Backdoors خفية

### غالبًا:

-   مش exposed

-   لكن لو misconfigured → كارثة

* * * * *

6️⃣ Firewalls (Perimeter Devices)
---------------------------------

### إيه دوره؟

-   الحارس على باب الشبكة 🛡️

-   يسمح أو يمنع Traffic

### ليه مهم أوي؟

-   أول خط دفاع

-   أول مكان يبان فيه الهجوم

### Logs بتكشف:

-   Port Scans

-   Brute Force

-   Exploit Attempts




  
</details>




<details>
  <summary>Network Visibility</summary>





**Network Visibility** = إنك تشوف وتفهم اللي بيحصل جوه الشبكة\
ولو مش شايف → مش هتعرف تحمي → **You can't defend what you can't see**

الرؤية دي بتيجي من **نوعين لوجز**:

1.  **Host-Centric Logs** → جوه الجهاز نفسه

2.  **Network-Centric Logs** → بين الأجهزة وبعضها

والشغل الصح دايمًا = **Correlation بين الاتنين**

* * * * *




### 1️⃣ Host-Centric Logs (من جوه الجهاز)

بتجاوب على سؤال:

> **إيه اللي حصل على الجهاز نفسه؟**

#### أمثلة مهمة:

-   User عمل Login إمتى؟

-   Process اتشغّل (cmd / powershell / malware)

-   File اتعدل أو اتشال

-   Service اتضاف

-   Antivirus قال إيه؟

#### في الهجوم:

-   تعرف **المهاجم نفّذ إيه**

-   استخدم أدوات إيه

-   سرق إيه

لكن ❌ **ما تعرفش:**

-   جه منين؟

-   كلم مين؟

-   خرج داتا ولا لأ؟

* * * * *

### 2️⃣ Network-Centric Logs (حركة الشبكة)

بتجاوب على:

> **مين كلم مين؟ إمتى؟ إزاي؟**

#### أمثلة:

-   Firewall: اتصال اتسمح له أو اترفض

-   IDS/IPS: محاولة استغلال

-   Proxy: يوزر دخل موقع مشبوه

-   VPN: دخول من دولة غريبة

-   NetFlow: جهاز فجأة بعت 5GB برا الشركة

* * * * *



🧩 ليه لازم الاتنين مع بعض؟
---------------------------

### مثال هجوم كامل :

#### 1️⃣ Network Logs تقول:

-   IP خارجي حاول يدخل على VPN

-   نجح الساعة 3 الفجر

#### 2️⃣ Host Logs تقول:

-   User عمل Login

-   PowerShell اتشغّل

-   File مشبوه نزل

#### 3️⃣ Network Logs:

-   الجهاز بدأ يكلم IP برا الشركة (C2)

-   بورت غريب

-   Traffic متكرر

#### 4️⃣ Host Logs:

-   Process بيشفر ملفات

-   Services اتعدلت

👉 **كده بنيت Timeline كامل للهجوم**

* * * * *

تركّز على إيه؟

### 🚨 Indicators مهمة:

-   Login في وقت غريب

-   IP من دولة مش متوقعة

-   Process مش طبيعي

-   Outbound traffic كبير

-   Repeated failed logins

-   Connection لبورت غير شائع








  
</details>



<details>
  <summary>Network Perimeter</summary>


🧱 يعني إيه Network Perimeter ببساطة؟
-------------------------------------

الـ **Network Perimeter** هو **الحد الفاصل** بين:

-   ✅ الشبكة الداخلية (موظفين -- سيرفرات -- AD -- داتا)

-   ❌ الإنترنت (أي حد -- أي تهديد)

تخيّلها:

> **باب العمارة + البوابة + الأمن**

أي حاجة داخلة أو طالعة **لازم تعدّي من هنا**.

* * * * *

🧠 ليه مهم أوي؟
---------------

لأن:

-   أول مكان المهاجمين بيجسّوا فيه

-   أول مكان الـ SOC يشوف فيه الهجوم

-   أي غلطة هنا = الشبكة كلها في خطر

* * * * *

🧩 مكونات الـ Network Perimeter (واحدة واحدة)
---------------------------------------------

### 1️⃣ Firewall 🔥 (الحارس الأساسي)

#### وظيفته:

-   يسمح أو يمنع الترافيك

-   يراقب الداخل والخارج

-   يسجّل كل حاجة في Logs

#### مثال:

-   Allow: HTTPS للـ Web Server

-   Block: RDP من الإنترنت

#### من منظور أمني:

-   Port Scans

-   Brute Force

-   Connections من IPs مشبوهة

📌 **أول Logs تبص عليها في أي Incident**

* * * * *

### 2️⃣ Routers / Gateways 🌐

#### وظيفتها:

-   توجّه الترافيك

-   تربط الشبكة بالإنترنت

-   أحيانًا تطبّق ACLs

#### الخطر لو اتاخدت:

-   MITM

-   Rerouting

-   Backdoors

* * * * *

### 3️⃣ DMZ (Demilitarized Zone) 🛑

#### يعني إيه؟

شبكة وسط:

> لا داخل قوي ولا برا قوي

#### بتحط فيها:

-   Web Server

-   Mail Server

-   VPN Gateway

#### ليه؟

لو الـ Web Server اتاخد:\
❌ المهاجم **ما يدخلش مباشرة على الشبكة الداخلية**

📌 DMZ = **damage control**

* * * * *

### 4️⃣ VPN Gateways 🔐

#### وظيفتها:

-   دخول الموظفين من برا

-   تشفير الاتصال

#### الهجمات الشائعة:

-   Brute Force

-   Stolen Credentials

-   Login من دول غريبة

📌 VPN Logs = كنز للمحلل الأمني

* * * * *

🧨 إيه اللي بيحصل لو الـ Perimeter ضعيف؟
----------------------------------------

المهاجم يعمل:

1.  Scan على IPs

2.  يلاقي RDP / SSH مفتوح

3.  Brute Force

4.  يدخل

5.  Pivot لجوه الشبكة

6.  يسرّب داتا

كل ده كان ممكن يتقفّل من عند **البوابة**

* * * * *

🧪 سيناريو عملي (CTF / روم):
----------------------------

### 🔍 Firewall Logs:

-   IP بيجرب بورتات كتير

-   Blocked connections متكررة

### 🔐 VPN Logs:

-   Failed logins

-   Successful login الساعة 4 فجراً

### 📤 Outbound Traffic:

-   جهاز داخلي بيكلم IP خارجي غريب

-   Beaconing كل 60 ثانية (C2)

💡 **كده تقول:**

> ده اختراق بدأ من الـ perimeter





  
</details>




<details>
  <summary>Network Perimeters: Monitoring and Protecting</summary>





🔭 يعني إيه Monitoring the Perimeter عمليًا؟
--------------------------------------------

مش مجرد إن فيه Firewall شغال...\
لا، ده معناه:

-   📜 **تقرأ Logs**

-   🔎 **تدور على Patterns**

-   🧠 **تفهم السلوك الطبيعي**

-   🚨 **تلقط الغلط بسرعة**

> المهاجم دايمًا يبدأ من البيريميتر\
> وإنت دايمًا تبدأ التحقيق من هنا

* * * * *

🎯 أهداف مراقبة الـ Perimeter
-----------------------------

كمحلل أمن، إنت بتراقب عشان:

1️⃣ تكتشف الهجوم بدري\
2️⃣ تعرف فيه Service مكشوفة بالغلط\
3️⃣ تمسك C2 أو Data Exfiltration\
4️⃣ تمنع المهاجم قبل ما يدخل جوه

* * * * *

🧪 السيناريوهات (واحد واحد)
===========================

* * * * *

🧨 Scenario 1: Port Scanning
----------------------------

### 🔍 اللوج بيقول إيه؟

`203.0.113.10 ->  10.0.0.20:21  203.0.113.10 ->  10.0.0.20:22  203.0.113.10 ->  10.0.0.20:23  203.0.113.10 ->  10.0.0.20:25  203.0.113.10 ->  10.0.0.20:53  `

### 🧠 التحليل:

-   نفس الـ IP

-   بورتات مختلفة

-   في وقت قصير

-   أغلبها BLOCK

### 🧾 Verdict:

✅ **Port Scan**

📌 المهاجم بيقول:

> "خليني أشوف إيه فاتح"

📌 إنت كمحلل:

> الحمد لله الـ Firewall شغال\
> بس الـ IP ده لازم يتحط Watch / Blocklist

* * * * *

🌐 Scenario 2: Web Server Attack (WAF)
--------------------------------------

### 🔍 اللوج:

`attack_type="XSS"  attack_type="Directory Traversal"  attack_type="SQL Injection"  `

### 🧠 التحليل:

-   ده مش Traffic عادي

-   ده هجوم Web واضح

-   أدوات أو Manual Testing

### الفرق هنا؟

🔥 **WAF ذكي**

-   مش بس Block

-   بيقولك *ليه* اتمنع

### 🧾 Verdict:

🚨 **Active Web Attack**

📌 ده مش False Positive\
📌 ده حد بيهاجم موقعك فعليًا

* * * * *

🔐 Scenario 3: VPN Brute Force
------------------------------

### 🔍 اللوج:

`FAILED_AUTH user  'admin' FAILED_AUTH user  'guest' FAILED_AUTH user  'user'  `

### 🧠 التحليل:

-   يوزرات عامة

-   محاولات كتير

-   وقت قصير

### الحاجة المهمة:

فيه SUCCESS_AUTH\
بس:

-   دي Users حقيقيين

-   IPs مختلفة

-   طبيعي

### 🧾 Verdict:

🚨 **Brute Force Attack**

📌 الحل:

-   Block IP

-   Rate Limiting

-   MFA

-   Alert عالي الخطورة




| Pattern                  | معناه             |
| ------------------------ | ----------------- |
| نفس IP → بورتات كتير     | Port Scan         |
| نفس IP → نفس الخدمة      | Brute Force       |
| Traffic كل 30 / 60 ثانية | Malware Beaconing |
| Data كبيرة طالعة برا     | Data Exfiltration |


---
----
----








```ruby
cat firewall_logs.txt | grep BLOCK
```

<img width="1105" height="569" alt="image" src="https://github.com/user-attachments/assets/64166a1d-38e9-42a3-8cb6-bf29e1ba299b" />

<img width="1605" height="149" alt="image" src="https://github.com/user-attachments/assets/c8621918-840a-464c-b74e-e652db3b6d14" />

---

<img width="1581" height="140" alt="image" src="https://github.com/user-attachments/assets/ceec3555-ddf4-41e2-a9e4-241b6d22e4ff" />


```
head waf_logs.txt | grep BLOCK
```

<img width="1615" height="476" alt="image" src="https://github.com/user-attachments/assets/382d9c5a-c7ff-4d20-9df8-e0e14804b504" />

---

<img width="1569" height="141" alt="image" src="https://github.com/user-attachments/assets/16226873-4059-4a4f-bec6-6f4c4eec04d1" />


```
cat vpn_logs.txt | grep FAILED
grep -c "FAILED" vpn_logs.txt
```

<img width="1267" height="601" alt="image" src="https://github.com/user-attachments/assets/c9ca78ab-8313-4ba8-8248-ca4f1c55ec10" />

---

<img width="1581" height="145" alt="image" src="https://github.com/user-attachments/assets/3397d1a0-1c07-49c8-9cc6-986e31d12dd2" />







  
</details>









<details>
  <summary>Perimeter Logs: Investigating the Breach</summary>


```
cat firewall.log | grep “BLOCK” | cut -d’ ‘ -f5 | cut -d: -f1 | sort -nr | uniq -c
```

<img width="840" height="132" alt="image" src="https://github.com/user-attachments/assets/025a6841-0e38-4abd-b4eb-fde576325071" />

---

```
cat vpn_auth.log | grep 203.0.113.45
```

<img width="1358" height="237" alt="image" src="https://github.com/user-attachments/assets/6eba867c-65e4-464d-8fd8-10f971753445" />

----

<img width="1293" height="400" alt="image" src="https://github.com/user-attachments/assets/2dec27b0-320f-46bd-9bd6-ed6b1744a5ab" />



---

```
cat firewall.log | grep “203.0.113.45” | grep “ALLOW” | head
```

<img width="842" height="131" alt="image" src="https://github.com/user-attachments/assets/79dd137b-191a-49b6-8eaf-73f6c14eca55" />

---

```
cat ids_alerts.log | grep C2 | head
```


<img width="1919" height="336" alt="image" src="https://github.com/user-attachments/assets/65bd4e87-905d-4920-b7b9-9adedc762c32" />


---

```
cat ids_alerts.log | grep -n “10.0.0.60” | cut -d’ ‘ -f6,7,8,9,10,19,22,23 | head -n 15
```


<img width="528" height="122" alt="image" src="https://github.com/user-attachments/assets/77a24d66-76c9-4ac3-b35e-e29fa046b929" />


---


```
cat firewall.log | grep “198.51.100.77” | cut -d’ ‘ -f5,6,7 | uniq | sort
```


<img width="574" height="159" alt="image" src="https://github.com/user-attachments/assets/f065626f-36a5-416f-83f3-9e64921cbe2d" />



  
</details>
























































































