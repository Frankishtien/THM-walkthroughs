#  Wireless Security

<img width="1899" height="374" alt="image" src="https://github.com/user-attachments/assets/034bf0fd-f629-4a18-9542-b2a92a1246b5" />

---



<details>
  <summary>Wi-Fi Network Fundamentals</summary>



## 📡 مكونات الـ Wi-Fi

عندنا حاجتين أساسيين:

### Access Point — AP

ده الجهاز اللي بيطلع الـ Wi-Fi.

```
Internet / LAN
      ↓
     AP
   ↙   ↘
Phone   Laptop
```

الـ Home Router مثلًا بيكون فيه Access Point.

### Clients

الأجهزة اللي بتتصل بالـ AP:

- Laptop
- Phone
- Printer
- Smart devices

وكل جهاز عنده Wireless NIC عشان يبعت ويستقبل Radio signals.

---

# 🏷️ SSID vs BSSID

دي مهمة جدًا.

### SSID

ده **اسم الشبكة** اللي بتشوفه:

```
Office_WiFi
Home_5G
CoffeeShop
```

يعني:

> **SSID = اسم الشبكة**

### BSSID

ده الـ identifier الخاص بالـ Access Point، وغالبًا بيكون **MAC Address** للـ wireless interface:

```
00:1A:2B:3C:4D:5E
```

يعني:

> **BSSID = هوية الـ AP نفسه**

فاحفظها:

```
SSID  → اسم الشبكة
BSSID → MAC بتاع الـ AP
```

---

# 📶 2.4 GHz vs 5 GHz

### 2.4 GHz

```
Range       ⭐⭐⭐⭐⭐
Speed       ⭐⭐⭐
Wall        ⭐⭐⭐⭐⭐
Congestion  ⭐⭐
```

- مدى أكبر
- بيعدي الحواجز أحسن
- أبطأ نسبيًا
- زحمة جدًا بسبب Bluetooth وغيره

### 5 GHz

```
Range       ⭐⭐⭐
Speed       ⭐⭐⭐⭐⭐
Wall        ⭐⭐
Congestion  ⭐⭐⭐⭐
```

- أسرع
- أقل زحمة
- مداه أقل
- الحوائط بتأثر عليه أكتر

يعني ببساطة:

> **2.4 GHz = أبعد لكن أبطأ**  
> **5 GHz = أسرع لكن أقصر مدى**

---

# 🔐 لما الجهاز يتصل بالـ Wi-Fi

بشكل مبسط:

```
Device
  ↓
Scan
  ↓
Choose AP
  ↓
Authentication / Association
  ↓
Key negotiation
  ↓
Encrypted communication
```

وهنا فيه نقطة مهمة جدًا:

## WPA2-Personal

الـ Wi-Fi بيستخدم **Pre-Shared Key (PSK)**، وبعد الـ association بيحصل **4-way handshake** عشان الطرفين يثبتوا إنهم عارفين الـ key ويشتقوا مفاتيح التشفير.

```
Client ↔ AP
       ↓
4-Way Handshake
       ↓
Encrypted Traffic
```

## WPA2-Enterprise

هنا الموضوع مختلف:

```
Client
  ↓
AP
  ↓
RADIUS / Authentication Server
  ↓
Credentials validated
```

يعني بدل password واحدة مشتركة بين كل الناس، كل مستخدم ممكن يكون له authentication خاص به.

---

# 📡 إيه اللي بيأثر على الـ Signal؟

### 1. Physical barriers

حوائط، خرسانة، معدن...

كل ده يضعف الإشارة.

### 2. Distance

كل ما تبعد عن الـ AP:

```
Distance ↑
Signal ↓
```

### 3. Interference

أجهزة أو شبكات تانية بتستخدم نفس التردد.

### 4. Channel Congestion

لو 20 شبكة في نفس المكان بتستخدم channels متداخلة، الدنيا بتبقى زحمة.

### 5. Signal Leakage ⚠️

ودي مهمة جدًا للـ Pentesting.

ممكن الـ Wi-Fi بتاع الشركة يوصل لحد:

```
Office
  ↓
Parking
  ↓
Street
```

يعني حد **بره المبنى** ممكن يقدر يشوف الشبكة ويحاول يتفاعل معاها.

---






  
</details>



<details>
  <summary>Wi-Fi Security</summary>





## 1️⃣ IEEE 802.11 Wi-Fi Standards

ببساطة:

**Wi-Fi = طريقة الاتصال اللاسلكي**  
والـ **IEEE 802.11 = مجموعة المعايير اللي بتحدد Wi-Fi بيشتغل إزاي.**

يعني تخيل إن IEEE حطت "قواعد اللعبة" للـ Wi-Fi.

مع الوقت ظهرت أجيال مختلفة:

|الجيل|الاسم|الفكرة الأساسية|
|---|---|---|
|Wi-Fi 4|802.11n|سرعة أفضل + 2.4/5 GHz|
|Wi-Fi 5|802.11ac|سرعة أعلى + 5 GHz|
|Wi-Fi 6|802.11ax|سرعة وكفاءة أفضل خصوصًا مع أجهزة كتير|
|Wi-Fi 6E|802.11ax|Wi-Fi 6 + نطاق 6 GHz|

 أثناء الـ Wireless Assessment ممكن تقابل:

**Wi-Fi 5 / Wi-Fi 6**

وده لأنهم منتشرين في الشركات والأجهزة الحديثة.

---

# 2️⃣ Wi-Fi Security Protocols

هنا عندنا حاجتين مختلفتين:

### 🔐 Authentication

يعني:

> **مين مسموح له يدخل الشبكة؟**

مثلاً لما تدخل باسورد الـ Wi-Fi، الشبكة بتتحقق إنك عندك الـ credentials المطلوبة.

### 🔒 Encryption

يعني:

> **بعد ما دخلت، إزاي نحمي البيانات اللي بتتنقل؟**

مثلاً:

```
Laptop  ───── encrypted data ─────>  Access Point
```

حتى لو حد قدر يلتقط الـ Wireless Traffic، المفروض مايعرفش يقرأ البيانات بسهولة.

---

## أشهر الـ Security Protocols

الصورة التانية غالبًا هتقابلك فيها التسلسل ده:

```
WEP
 ↓
WPA
 ↓
WPA2
 ↓
WPA3
```

### 🔴 WEP

قديم جدًا وضعيف.

فيه مشاكل cryptographic تسمح للمهاجم باستغلال الـ traffic واسترجاع الـ key.

لذلك:

> **WEP = Outdated / Insecure**

---

### 🟠 WPA

جاء كحل أفضل من WEP، لكنه أصبح قديمًا حاليًا.

---

### 🟢 WPA2

ده من أشهر المعايير اللي هتقابلها.

وفيه أنماط مهمة:

**WPA2-Personal**

```
Wi-Fi Password
      ↓
   Users
```

مناسب للـ home/small networks.

**WPA2-Enterprise**

```
Client
  ↓
802.1X
  ↓
Authentication Server
```

بيستخدم في الشركات، وغالبًا مع **RADIUS**.

---

### 🔵 WPA3

الأحدث والأقوى من WPA2، مع تحسينات في عملية الـ authentication والحماية من بعض هجمات password guessing.

---

# ⚠️ Common Wi-Fi Misconfigurations

دي بقى مهمة جدًا في الـ Pentesting.

الشبكة ممكن تكون شغالة، لكن إعداداتها غلط:

### 1. استخدام WEP

```
WEP ❌
WPA2/WPA3 ✅
```

---

### 2. Weak Password

مثلاً:

```
Company123
Password123
12345678
```

حتى لو الشبكة WPA2، **الباسورد الضعيف يفضل نقطة ضعف**.

---

### 3. WPS Enabled

WPS بيسمح بطرق بديلة للاتصال، وأشهرها الـ **8-digit PIN**.

لو مش محتاجينه:

> Disable WPS.

---

### 4. Default Admin Credentials

مثلاً الـ Access Point لسه بالـ default:

```
admin : admin
```

المشكلة هنا مش Wi-Fi password نفسها، لكن **إدارة الـ Access Point**.

---

# ⚔️ Common Wi-Fi Attacks

### 1️⃣ Password Attack

المهاجم يحاول يخمن أو يكسر الـ Wi-Fi password.

```
Captured authentication data
          ↓
   Password guessing
          ↓
      Password?
```

---

### 2️⃣ Rogue Access Point

ده **Access Point حقيقي متوصل بالشبكة الداخلية بدون تصريح**.

مثلاً موظف جاب Router من البيت:

```
Company Network
       |
       AP
       |
 Employee Router ❌
```

المشكلة إن الراوتر ده ممكن يفتح طريق غير مصرح به للشبكة.

---

### 3️⃣ Evil Twin

هنا الفرق المهم جدًا:

**Rogue AP** → متوصل فعليًا بالـ target network.

**Evil Twin** → Access Point مزيف مستقل، بيقلد الشبكة الأصلية.

مثلاً الشبكة الحقيقية:

```
Company_WiFi
```

المهاجم يعمل:

```
Company_WiFi  ← Fake AP
```

ويحاول يخلي المستخدم يتصل بيه.

الفكرة:

```
Victim
   ↓
Fake AP
   ↓
Attacker
```

وده ممكن يُستخدم لاعتراض traffic أو خداع المستخدم لإدخال credentials.

---

### 4️⃣ Deauthentication Attack

ببساطة:

> إجبار جهاز متصل بالـ Wi-Fi إنه يفصل من الشبكة.

يعني:

```
Victim ─── Wi-Fi ─── AP
   ↓
Deauth
   ↓
❌ Disconnected
```

ودي نقطة مهمة جدًا في الـ Wireless Pentesting.

---

### 5️⃣ Traffic Interception

المهاجم يحاول يلتقط الـ wireless traffic.

لكن القدرة على قراءة البيانات تعتمد على **نوع وقوة التشفير وطريقة الاتصال**.

---

# 🛡️ حماية الشبكة

الـ room بيلخصها في:

```
WPA2 / WPA3
      +
Strong Password
      +
Disable WEP
      +
Disable WPS if unnecessary
      +
Change Default Admin Password
      +
Network Segmentation
      +
Firmware Updates
```

وأهم فكرة هنا:

### Network Segmentation

بدل ما كل الأجهزة تبقى في نفس الشبكة:

```
Corporate Network
      |
   Employees

Guest Network
      |
   Visitors
```

فالـ Guest مش المفروض يبقى عنده وصول مباشر للـ internal systems.

---









  
</details>
















































