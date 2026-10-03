# Mobile Application Security


<img width="1827" height="375" alt="image" src="https://github.com/user-attachments/assets/ef004d7f-f08b-4271-9069-2ee11684e336" />


----

<details>
  <summary>How Mobile Applications Work</summary>




## 📱 1. الـ APK هو Package

الـ Android App مش ملف كود واحد، هو **Package** فيه كل حاجة التطبيق محتاجها:

```text
APK
├── AndroidManifest.xml  → Configuration
├── classes.dex          → Compiled code
├── res/                 → Resources
├── assets/              → Assets
├── lib/                 → Native libraries
└── ...
```

كمختبر، أول ما تمسك APK تقدر **تفكّه وتحلله** حتى قبل ما تشغله.

---

## 📋 2. AndroidManifest.xml

ده أهم ملف تقريبًا في البداية.

بيحدد حاجات زي:

- اسم التطبيق وإصداره
    
- الـ Permissions
    
- الـ Activities
    
- Services
    
- Broadcast Receivers
    
- Content Providers
    
- مين منهم `exported`
    

مثلاً لو لقيت:

```xml
<activity
    android:name=".AdminActivity"
    android:exported="true">
```

ده معناه إن تطبيقات تانية ممكن تحاول تتعامل مع الـ `AdminActivity`.

فـ **Manifest = خريطة الـ Attack Surface بتاعة التطبيق**.

---

## 🔒 3. Sandbox

كل App في Android ليه بيئة معزولة:

```text
App A  🔒
App B  🔒
App C  🔒
```

بشكل افتراضي:

```text
App A ❌ → ملفات App B
App A ❌ → Processes بتاعة App B
```

وده بيقلل الضرر لو App اتخترق.

كمختبر، لو لقيت طريقة تتجاوز العزل ده → دي مشكلة أمنية مهمة.

---

## 🧩 4. Application Components

الـ App بيتكون من Components:

```text
Activity
Service
Broadcast Receiver
Content Provider
```

والنقطة المهمة جدًا هنا:

### Internal

```text
App → Component
```

الـ Component متاح للتطبيق نفسه.

### Exported

```text
Other App
    ↓
Component
```

تطبيق تاني ممكن يتعامل معاه.

وده بيعمل **Attack Surface**.

مثلاً:

```text
Exported Activity
        ↓
Attacker sends Intent
        ↓
Sensitive functionality
```

---



  
</details>







<details>
  <summary>The Mobile Pentesting Methodology</summary>





## 🔥 الأربع مراحل

### 1️⃣ Reconnaissance

نجمع معلومات قبل ما نهاجم:

```text
App
├── بيعمل إيه؟
├── مين بيستخدمه؟
├── Backend إيه؟
├── API endpoints؟
└── APK أجيبه منين؟
```

**الهدف:** أفهم الـ Attack Surface.

---

### 2️⃣ Static Analysis 🔍

نحلل الـ APK **من غير ما نشغله**:

```text
APK
 ↓
Unpack
 ↓
Manifest
 ↓
Code
 ↓
Strings
 ↓
URLs / API Keys / Secrets
 ↓
Vulnerabilities
```

يعني بنبص جوه التطبيق ونشوف فيه إيه.

---

### 3️⃣ Dynamic Analysis 🧪

نشغل التطبيق ونراقبه أثناء التشغيل:

```text
App Running
    ↓
Network Traffic → Burp
    ↓
Runtime Behaviour
    ↓
Components / Intents
    ↓
Test vulnerabilities
```

مثلاً ممكن vulnerability مش باينة في الـ APK نفسه، لكن تظهر لما التطبيق يتفاعل مع الـ API.

---

### 4️⃣ Reporting 📝

لو لقينا vulnerability نوثق:

```text
Description
↓
Steps to Reproduce
↓
Impact
↓
Evidence / PoC
↓
Remediation
```

يعني التقرير لازم يخلي الـ developer يعرف **المشكلة + تأثيرها + يصلحها إزاي**.

---

# 🛡️ OWASP Mobile Top 10

 **Checklist** للـ Mobile Security.

مش مطلوب تحفظه دلوقتي.

هنستخدمه بعدين عشان نصنف الـ vulnerabilities اللي نلاقيها.

---

# 🎯 أنواع الـ Testing

نفس فكرة Web Pentesting:

|النوع|اللي معاك|
|---|---|
|**Black Box**|تقريبًا مفيش معلومات|
|**Grey Box**|معلومات جزئية / credentials / جزء من source|
|**White Box**|Source code + architecture + documentation|

وغالبًا **Grey Box** شائع في الـ professional engagements.

---




  
</details>






<details>
  <summary>Static Analysis</summary>



## يعني إيه Static Analysis؟

ببساطة:

> **أحلل الـ APK من غير ما أشغل التطبيق.**

يعني:

```text
APK
 ↓
فك الـ APK
 ↓
Manifest
 ↓
Code + Resources
 ↓
Secrets / Misconfigurations
 ↓
Findings
```

---

## 1️⃣ نجيب الـ APK ونفكه

الـ APK هو الملف اللي هنشتغل عليه.

بنستخدم أدوات زي **JADX / Apktool** عشان نفكّه ونشوف الكود والملفات بشكل مقروء.

مش هيطلعلك الـ source code الأصلي 100%، لكن غالبًا هتقدر تفهم:

- Logic
    
- API calls
    
- URLs
    
- Strings
    
- طريقة التعامل مع البيانات
    

---

## 2️⃣ أول حاجة نبص عليها: Manifest

في Android:

```text
AndroidManifest.xml
```

وبندور على 3 حاجات أساسية:

### 🔴 Permissions زيادة

مثلاً التطبيق مجرد Calculator وبيطلب:

```text
Camera
Microphone
Contacts
Location
```

نسأل:

> هو محتاج كل ده ليه؟

كل Permission زيادة ممكن تزود الـ attack surface.

---

### 🔴 Exported Components

مثلاً:

```xml
android:exported="true"
```

مع Activity حساسة.

ده معناه إن **تطبيق تاني ممكن يحاول يتفاعل معاها**.

وده ممكن يعمل attack surface.

---

### 🔴 Insecure Configurations

إعدادات في الـ Manifest ممكن تضعف الـ security.

ودي من الحاجات اللي بندور عليها أثناء الـ review.

---

# 🍎 3️⃣ iOS

في Android عندنا:

```text
AndroidManifest.xml
```

وفي iOS عندنا:

```text
Info.plist
```

نفس الفكرة تقريبًا: configuration + permissions + behavior.

مثال مهم:

```text
NSAllowsArbitraryLoads = true
```

ده ممكن يسمح للتطبيق يعمل HTTP بدل HTTPS، وده يضعف حماية الاتصال.

---

# 🔑 4️⃣ Hardcoded Secrets

دي من أهم الحاجات في Static Analysis.

ندور على:

```text
API Keys
Passwords
Tokens
Encryption Keys
Internal URLs
Database credentials
```

ممكن تلاقي حاجة زي:

```java
String API_KEY = "sk-xxxxxxxx";
```

أو:

```text
https://internal-api.company.local
```

المشكلة إن:

> **الـ APK موجود عند المستخدم، وبالتالي الـ attacker يقدر يحلله ويستخرج الحاجات دي.**

مش معنى إن القيمة مخفية في الكود إنها Secret آمن.

---

# 🤖 5️⃣ MobSF

بدل ما تعمل كل حاجة يدويًا، عندك:

**MobSF — Mobile Security Framework**

تديله APK وهو يعمل automated static analysis ويطلعلك حاجات زي:

```text
Permissions
Exported Components
Hardcoded Secrets
Insecure Configurations
URLs
Security Issues
```

لكن مهم جدًا:

> **MobSF مش بديل عنك.**

هو بيساعدك تلاقي الحاجات الواضحة بسرعة، لكن مش هيكتشف كل Business Logic flaws مثلًا.

---

```text
APK
 ↓
Manifest
 ├── Permissions
 ├── Exported Components
 └── Configurations
 ↓
Code
 ├── API Keys
 ├── Tokens
 ├── Passwords
 └── URLs
 ↓
Resources / Config / DB
 ↓
MobSF
 ↓
Manual Analysis
```









  
</details>







<details>
  <summary>Dynamic Analysis</summary>



##  الفرق في ثانية

```text
Static Analysis
→ التطبيق مش شغال
→ بنقرأ الكود والـ Manifest

Dynamic Analysis
→ التطبيق شغال
→ بنراقب سلوكه ونجرب عليه
```

---

## 1️⃣ Traffic Interception 🌐

نشغل التطبيق ونخلي الـ traffic يعدي من خلال **Burp Suite** مثلًا:

```text
Mobile App
    ↓
   Burp
    ↓
 Backend/API
```

وبنشوف:

- بيبعت إيه؟
    
- الـ API endpoints إيه؟
    
- الـ authentication token بيتبعت إزاي؟
    
- HTTPS ولا HTTP؟
    
- هل في بيانات حساسة في الـ requests؟
    

مثلاً:

```http
POST /api/login
Authorization: Bearer eyJ...
```

هنا نقدر نحلل طريقة الـ authentication والـ API.

ولو لقيت:

```text
HTTP
username=ahmed
password=123456
```

دي مشكلة **Insecure Communication → M5**.

---

# 🔐 2️⃣ SSL Pinning

هنا فيه security control اسمه:

**SSL Pinning**

التطبيق بدل ما يقول:

> "هثق في أي Certificate موثوقة."

يقول:

> "أنا هثق في Certificate معينة أنا عارفها."

فلو حطيت Burp في النص:

```text
App
 ↓
Burp Certificate
 ↓
Server
```

التطبيق ممكن يقول:

```text
❌ Certificate doesn't match
```

وبالتالي مش هتشوف الـ traffic.

**مهم جدًا:**  
SSL Pinning **مش vulnerability**، ده protection mechanism.

في الـ pentest بنحتاج نعمل bypass ليه عشان نقدر نختبر الـ API والـ traffic.

أدوات زي:

```text
Objection
Frida
```

ممكن تساعدنا في الـ bypass.

---

# 🧪 3️⃣ Runtime Instrumentation

دي من أهم الحاجات في Mobile Pentesting.

**Frida** بتخليك تتدخل في التطبيق وهو شغال.

تقدر مثلًا تعمل:

```text
App
 ↓
Function
 ↓
Frida Hook
 ↓
راقب البيانات
```

مثلاً التطبيق عنده:

```java
checkPassword(password)
```

ممكن تعمل hook للـ function وتشوف:

```text
password = "..."
```

أو تغير الـ behavior أثناء التشغيل.

### Objection؟

فكر فيها كده:

```text
Frida
  ↓
Low-level powerful framework

Objection
  ↓
Tools/commands جاهزة مبنية فوق Frida
```

يعني لما Objection مش موفر command جاهز للحاجة اللي عايزها، ممكن تكتب **Frida script** بنفسك.

---

# 📜 4️⃣ Insecure Logging

التطبيق ممكن يكتب حاجات في Android logs أثناء التشغيل:

```text
username=ahmed
token=eyJ...
password=123456
```

وده خطر جدًا.

إحنا أثناء الـ Dynamic Analysis بنراقب الـ logs ونشوف هل التطبيق بيسرب:

```text
Passwords
Tokens
PII
API responses
Sensitive data
```

وده في الـ room بيتربط بـ:

**M9: Insecure Data Storage**

---



  
</details>






















<details>
  <summary>Common Mobile Vulnerabilities</summary>




## 1️⃣ Insecure Data Storage — M9

المشكلة إن التطبيق يخزن بيانات حساسة على الموبايل بطريقة سهلة القراءة.

مثلاً:

```text
Shared Storage
Local Database
Config Files
Cache
Logs
```

وتلاقي فيها:

```text
username
password
session token
API key
personal data
```

مثال سيئ:

```text
token = "eyJhbGci..."
```

من غير حماية مناسبة.

**كمختبر:** نشوف التطبيق بيكتب إيه على الجهاز وفين.

---

## 2️⃣ Improper Platform Usage

يعني التطبيق **بيستخدم Android features بطريقة غلط**.

أشهر مثال: Permissions زيادة عن الحاجة.

تطبيق Notes مثلًا محتاج:

```text
Storage
```

لكن طالب:

```text
Camera
Microphone
Contacts
Location
```

ليه؟ 🤨

المشكلة إن لو التطبيق اتم اختراقه، الـ attacker ممكن يستفيد من **الصلاحيات الكتيرة اللي التطبيق واخدها**.

وكمان ممكن يكون فيه استخدام غير آمن للـ:

- Intents
    
- Permissions
    
- Components
    
- Platform APIs
    

---

## 3️⃣ Insecure Authentication & Session Management

هنا هتلاقي حاجات شبه الـ Web Pentesting اللي أنت متعود عليها.

مثلاً:

```text
Session Token
     ↓
لا ينتهي
     ↓
Token stolen
     ↓
Account access
```

ندور على:

- Tokens لا تنتهي
    
- Tokens متخزنة بشكل غير آمن
    
- Sensitive actions بدون إعادة Authentication
    
- Weak authentication
    
- Biometric bypass
    

### مثال الـ Biometric

التطبيق يعمل:

```text
Fingerprint
   ↓
OS says "Accepted"
   ↓
App opens sensitive page
```

لو التطبيق **بيثق في نتيجة الـ biometric محليًا فقط** ومفيش حماية إضافية مناسبة، ممكن أثناء الـ runtime نحاول نتلاعب بالـ flow باستخدام instrumentation.

وده قريب جدًا من فكرة إن:

> **Client-side security check مش لازم تعتبره Trust Boundary.**

---

# 4️⃣ Exposed Application Components

دي مهمة جدًا في Android.

مثلاً عندنا:

```text
AccountActivity
   ↓
exported=true
```

وأي App تاني على الجهاز يقدر يحاول يفتحها.

لو الـ Activity بتعرض بيانات حساسة ومفيش authorization مناسب:

```text
Malicious App
     ↓
AccountActivity
     ↓
Sensitive Data
```

يبقى عندنا vulnerability.

وده سبب إننا في الـ Static Analysis كنا بنبص على:

```text
exported components
```

---

# 5️⃣ Insufficient Binary Protections — M7

دي مختلفة شوية.

المقصود إن التطبيق **مش محمي كويس ضد Reverse Engineering / Tampering**.

بنبص على:

### Obfuscation

بدل:

```java
checkAdminAccess()
```

الكود بعد obfuscation يبقى أصعب في الفهم.

---

### Tamper Detection

هل التطبيق يكتشف إن حد:

```text
APK
 ↓
Modified
 ↓
Repackaged
 ↓
Run
```

ولا يشتغل عادي؟

---

### Root / Jailbreak Detection

هل التطبيق يعرف إن الجهاز:

```text
Android → Rooted
iOS → Jailbroken
```

ولا لأ؟

التطبيقات الحساسة ممكن تعمل:

```text
Root detected
     ↓
Warning / Block
```

غياب الحماية دي ممكن يكون finding حسب **سياق التطبيق وتهديداته**، مش مجرد "أي تطبيق مش بيعمل root detection = ثغرة خطيرة".

---




  
</details>









<details>
  <summary>Practical Challenge: Leaky Package</summary>




<img width="1724" height="740" alt="image" src="https://github.com/user-attachments/assets/d4030571-8b76-45e3-a692-1a995357f4c2" />



  
</details>


























