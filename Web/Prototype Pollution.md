# Prototype Pollution


<img width="1904" height="353" alt="image" src="https://github.com/user-attachments/assets/38d409ad-f38f-4b4e-a6e3-11d746add56b" />


---


<details>
   <summary>Essential Recap</summary>


🧱 أولاً: Objects (الكائنات)
============================

في JavaScript\
الـ **Object** هو علبة بتحط فيها بيانات ليها علاقة ببعض.

مثال:
```
let user = {\
  name: 'Ben S',\
  age: 25,\
  followers: 200,\
  DoB: '1/1/1990'\
};
```
ده كأنك عامل "بروفايل مستخدم".

كل حاجة جوه اسمها:

-   Key → name

-   Value → 'Ben S'

تقدر توصل للقيم كده:
```
console.log(user.name);
```
💡 الفكرة:\
Objects بتنظم البيانات وده أساس أي Web Application.

* * * * *

🏗️ ثانياً: Classes (الكلاسات)
==============================

الـ Class هو Blueprint (مخطط)\
يعني بدل ما تعمل object كل مرة بإيدك، تعمل قالب وتطلع منه نسخ.
```
class UserProfile {\
  constructor(name, age, followers, dob) {\
    this.name = name;\
    this.age = age;\
    this.followers = followers;\
    this.dob = dob;\
  }\
}
```
ولما تيجي تعمل مستخدم جديد:

let user1 = new UserProfile('Ben', 25, 1000, '1/1/1990');

🧠 الفكرة هنا:\
كل objects اللي طالعة من نفس الـ class ليها نفس التركيبة.

* * * * *

🧬 ثالثاً: Prototype (أهم نقطة 👀🔥)
====================================

هنا بقى لب الموضوع.

في JavaScript:\
كل Object مربوط بـ Prototype.

يعني في Object تاني وراه في الخلفية.

لو الخاصية مش موجودة في الـ object نفسه\
JavaScript يروح يدور عليها في الـ prototype.

مثال:
```
let userPrototype = {\
  greet: function() {\
    return `Hello, ${this.name}!`;\
  }\
};

function User(name) {\
  let obj = Object.create(userPrototype);\
  obj.name = name;\
  return obj;\
}

let user1 = User("Ben");\
console.log(user1.greet());
```
هنا:

-   user1 معندوش greet

-   JS راح جابها من prototype

ده اسمه:\
👉 Prototype Chain

* * * * *

🧬 Inheritance (الوراثة)
========================

ممكن Object ياخد صفات من Object تاني.
```
let user = {\
  name: "Ben",\
  followers: 1000\
};

let contentCreator = Object.create(user);\
contentCreator.posts = 50;
```
النتيجة:

-   contentCreator عنده posts

-   وكمان ورث name و followers

JS بيدور كده:

1.  يدور في object نفسه

2.  لو مش لاقي → يطلع فوق في prototype

3.  يكمل لحد Object.prototype

* * * * *

🧠 الفرق بين Class و Prototype
==============================

| Class | Prototype |
| --- | --- |
| شكل منظم | مرن وديناميكي |
| سهل القراءة | أقرب لطبيعة JS الأصلية |
| Syntax حديث | هو الأساس الحقيقي |

🔥 أهم نقطة:\
Classes في JavaScript مجرد "سكر نحوي"\
لكن تحت الكواليس كله Prototype.

* * * * *

💣 طيب ليه كل ده مهم لـ Prototype Pollution؟
============================================

عشان:

لو قدرت تعدّل في Prototype نفسه\
أي Object جديد هيتأثر 👀

مثال خطير:
```
Object.prototype.isAdmin = true;
```
كده أي object في السيستم هيبقى عنده isAdmin = true

حتى لو هو مش Admin أصلاً 😈

ودي هي الكارثة.

* * * * *

🎯 الصورة الكبيرة
=================

JS بتشتغل كده:

```
Object\
   ↓\
Prototype\
   ↓\
Object.prototype\
   ↓\
null
```
لو حد قدر يحقن بيانات في prototype chain\
ممكن يغير سلوك التطبيق كله.







<img width="1640" height="488" alt="image" src="https://github.com/user-attachments/assets/606a5b9e-182f-42f3-80d5-84a15e35d6b0" />


<img width="545" height="221" alt="image" src="https://github.com/user-attachments/assets/a8fdd815-5d09-41b5-a491-28f0f7703ae4" />






  
</details>




<details>
  <summary>How it works</summary>


💥 يعني إيه Prototype Pollution؟
================================

ببساطة:

> المهاجم بيعدّل الـ **Prototype** نفسه\
> فـ كل الـ objects اللي طالعة منه بتتأثر.

يعني بدل ما يهاجم object واحد...\
بيهاجم "الأصل" اللي كلهم واخدين منه 👀

* * * * *

🧠 نفهم المثال خطوة خطوة
========================

1️⃣ عندنا Prototype عادي
------------------------

```
let personPrototype = {\
  introduce: function() {\
    return `Hi, I'm ${this.name}.`;\
  }\
};
```

ده prototype فيه function اسمها introduce.

* * * * *

2️⃣ بنعمل Constructor
---------------------

```
function Person(name) {\
  let person = Object.create(personPrototype);\
  person.name = name;\
  return person;\
}
```
هنا:

-   كل شخص جديد بيبقى مربوط بـ personPrototype

-   يعني لو مش لاقي حاجة في نفسه → يروح يدور في prototype

* * * * *

3️⃣ نعمل instance
-----------------

```
let ben = Person('Ben');
```
لو كتبنا:
```
ben.introduce()
```
JS هيعمل كده:

1.  يدور في ben → مش لاقي introduce

2.  يطلع فوق في prototype

3.  يلاقي introduce هناك

4.  ينفذها

النتيجة:
```
Hi, I'm Ben.
```
لحد هنا كله تمام 💚

* * * * *

💣 الهجوم بيحصل فين؟
====================

المهاجم عمل كده:
```
ben.__proto__.introduce = function() {\
  console.log("You've been hacked, I'm Bob");\
}
```
هنا بقى حصلت المصيبة 👇

إيه اللي حصل فعليًا؟
--------------------

`ben.__proto__`\
دي بترجع الـ prototype نفسه.

يعني هو عدّل:
```
personPrototype.introduce
```

مش عدّل ben بس ❌\
عدّل الأصل نفسه ✅

* * * * *

🔥 التأثير الخطير
=================

بعد التعديل:

```
ben.introduce()
```

هتطبع:
```
You've been hacked, I'm Bob
```

طيب لو عملنا شخص جديد؟
```
let ali = Person('Ali');\
ali.introduce();
```
هتطبع نفس الرسالة المخترقة 😈

ليه؟

عشان كلهم بياخدوا من نفس prototype.

* * * * *

🧬 الفكرة العميقة
=================

سلسلة البحث في JS بتمشي كده:

```
ben\
  ↓\
personPrototype\
  ↓\
Object.prototype\
  ↓\
null
```

المهاجم عدّل في الطبقة التانية 👀\
فكل اللي تحتها اتأثر.

* * * * *

⚠️ ليه ده خطر في تطبيق حقيقي؟
=============================

تخيل إن عندك كود زي ده:

```
if (user.isAdmin) {\
  // show admin panel\
}
```

لو المهاجم عمل:

Object.prototype.isAdmin = true;

كده أي user هيتعامل إنه admin 💀

* * * * *

💀 ليه لوحدها مش دايمًا خطيرة؟
==============================

Prototype Pollution لوحدها:\
ممكن تبقى بس تعديل سلوك.

لكن لما تتحد مع:

-   XSS

-   CSRF

-   Template Injection

-   Access Control Bugs

هنا تبقى كارثة 🔥🔥🔥

مثال:\
لو التطبيق بيستخدم property معينة عشان يتحكم في صلاحيات\
وأنت حقنتها في prototype\
ممكن تاخد صلاحيات أعلى.

* * * * *

🧠تركز على إيه؟
=============================

دور على:

-   JSON input

-   merge functions (lodash.merge)

-   Object.assign()

-   deep copy libraries

-   أي حاجة بتاخد input وتحطه جوه object

لو لقيت:
```
__proto__\
constructor\
prototype
```
اعرف إنك قربت 💣







<img width="1630" height="329" alt="image" src="https://github.com/user-attachments/assets/6e561ac0-7b53-4314-bad0-ab3d40b6a73e" />



  
</details>






<details>
  <summary>Exploitation - XSS</summary>


🎯 أولاً: الفكرة العامة (Standard Approach)
===========================================

في JavaScript في خصائص حساسة جدًا موجودة في أي Object:

-   `__proto__`

-   `constructor`

-   `prototype`

دول بوابة الدخول 👀

لو المهاجم قدر يتحكم في:
```
Person[x][y] = val
```



<details>
  <summary>explain</summary>


🧱 أول حاجة: إزاي نوصل لقيمة جوه Object؟
========================================

لو عندك:
```
let user = {\
  name: "Ben",\
  age: 25\
};
```
تقدر توصل للقيمة بطريقتين:

الطريقة العادية:
----------------
```
user.name
```
طريقة الأقواس:
--------------
```
user["name"]
```
الاتنين نفس الحاجة 💡

* * * * *

🧠 طيب ليه نستخدم الأقواس؟
==========================

لأن أحيانًا اسم الخاصية بيبقى جاي من متغير 👀

مثال:
```
let key = "name";\
console.log(user[key]);
```
هنا:

-   `key` متغير

-   قيمته "name"

-   فـ user[key] = user["name"]

* * * * *

🔥 طيب نرجع لـ object[x][y]
===========================

خلينا نعمل مثال عملي:
```
let person = {\
  profile: {\
    age: 25\
  }\
};
```
دلوقتي:
```
person.profile.age
```
دي معناها:

-   روح لـ person

-   ادخل على profile

-   بعدين age

نقدر نكتبها بالأقواس:
```
person["profile"]["age"]
```
نفس المعنى بالظبط.

* * * * *

💥 يبقى object[x][y] معناها إيه؟
================================

معناها:

1.  ادخل على object

2.  روح للخاصية اللي اسمها x

3.  بعدين روح للخاصية اللي اسمها y جواها

* * * * *

🧪 مثال واضح جدًا
=================

```
let data = {\
  settings: {\
    theme: "dark"\
  }\
};

let x = "settings";\
let y = "theme";

console.log(data[x][y]);
```
ده يطبع:
```
dark
```
لأن:

```
data[x] = data["settings"]\
data[x][y] = data["settings"]["theme"]
```
* * * * *




  
</details>



وسابوه يتحكم في `x`

ممكن يخلي:
```
x = "__proto__"
```
ويبقى كده:
```
Person["__proto__"][y] = val
```
وده معناه:

🔥 هو عدّل في الـ prototype نفسه\
مش في object عادي

* * * * *

🏆 الـ Golden Rule (أهم قاعدة في الروم دي)
==========================================

لو المهاجم يقدر يتحكم في:
```
object[x][y] = value
```
وخلّى:
```
x = "__proto__"
```
يبقى:

أي property اسمها `y`\
هتتضاف لكل الـ objects.

* * * * *

🧨 السيناريو الأخطر
===================

لو يقدر يتحكم في:
```
object[x][y][z] = value
```
ويخلي:
```
x = "constructor"\
y = "prototype"
```
يبقى:
```
object.constructor.prototype[z] = value
```
وده نفس تأثير:
```
Object.prototype[z] = value
```
💀 يعني كل حاجة في التطبيق اتلوّثت.

* * * * *

⚠️ طيب بيحصل ده إزاي في التطبيق؟
================================

خلينا ندخل على المثال العملي.

* * * * *

🏗️ عندنا Object اسمه friends
=============================

```
let friends = [\
  {\
    id: 1,\
    name: "Sabalenka",\
    reviews: [],\
    albums: [{}],\
    password: "xxx"\
  }\
];
```
* * * * *

💣 النقطة الخطيرة في الكود
==========================

السيرفر بيعمل:
```
const input = JSON.parse(reviewContent);\
_.set(friend, input.path, payload.value);
```
لاحظ:

بيستخدم lodash function اسمها:

_.set(object, path, value)

ودي خطيرة جدًا لو path جاي من المستخدم 👀

* * * * *

🧠 يعني إيه Property Definition by Path؟
========================================

لو المستخدم بعت:
```
{\
  "path": "reviews[0].content",\
  "value": "<script>alert('Hacked')</script>"\
}
```
lodash هيعمل:
```
friend.reviews[0].content = "<script>alert('Hacked')</script>"
```
لو مفيش reviews[0]\
هيخلقها 👀

* * * * *

🔥 أول استغلال: Stored XSS
==========================

هو زرع:
```
<script>alert('Hacked')</script>
```
داخل review.

ولأن التطبيق مش بيعمل sanitization\
السكريبت بيتنفذ لما حد يفتح البروفايل.

ده Stored XSS 💀

* * * * *

😈 طيب فين Prototype Pollution؟
===============================

دلوقتي ركز بقى 👇

لو بدل ما يكتب:
```
{\
  "path": "reviews[0].content",\
  "value": "<script>alert('Hacked')</script>"\
}
```
كتب:
```
{\
  "path": "__proto__.isAdmin",\
  "value": true\
}
```
lodash هتعمل:
```
friend.__proto__.isAdmin = true;
```
وده معناه:

🔥 عدّل في prototype

النتيجة؟

أي object في التطبيق عنده:

isAdmin = true

* * * * *

💀 تأثير ده إيه؟
================

لو التطبيق فيه:
```
if (user.isAdmin) {\
  showAdminPanel();\
}
```
أي مستخدم عادي بقى Admin 😈

* * * * *

🧬 ليه lodash خطيرة؟
====================

لأنها:

-   بتقبل path كسلسلة نصية

-   بتقسمها

-   وبتنشئ properties تلقائيًا

من غير ما تتحقق إنك بتلمس:

-   **proto**

-   constructor

-   prototype




  
</details>






<details>
   <summary>Exploitation - Property Injection</summary>



🧠 أولاً: Recursive Merge يعني إيه؟
===================================

بص على الفنكشن دي:
```
function recursiveMerge(target, source) {\
    for (let key in source) {\
        if (source[key] instanceof Object) {\
            if (!target[key]) target[key] = {};\
            recursiveMerge(target[key], source[key]);\
        } else {\
            target[key] = source[key];\
        }\
    }\
}
```
دي بتعمل إيه؟

-   بتلف على كل property في `source`

-   لو القيمة object → تدخل جوه وتكمّل merge

-   لو مش object → تنسخها مباشرة

يعني بتعمل **نسخ عميق (Deep Merge)**.

* * * * *

💣 فين المشكلة؟
===============

السطر ده:
```
for (let key in source)
```
بيمشي على كل المفاتيح...\
من غير ما يسأل:

> هل المفتاح ده آمن أصلاً؟ 👀

* * * * *

😈 الهجوم
=========

المهاجم يبعت:
```
{ "__proto__": { "newProperty": "value" } }
```
الفنكشن هتعمل:
```
target["__proto__"] = { newProperty: "value" }
```
لكن في JavaScript:
```
obj["__proto__"]
```
مش property عادية ❌\
ده reference للـ prototype نفسه 💀

فاللي حصل فعليًا:
```
Object.prototype.newProperty = "value"
```
🔥 كده كل objects في التطبيق بقى عندها newProperty.

* * * * *

🎯 تخيل التأثير
===============

لو عندك:
```
if (user.isAdmin) {\
   showAdminPanel();\
}
```
والمهاجم عمل:
```
{ "__proto__": { "isAdmin": true } }
```
أي user بقى Admin 😈

* * * * *

🧬 تاني نقطة: Object Clone + Merge
==================================

بص على الكود ده:
```
let clonedAlbum = { ...albumToClone };\
merge(clonedAlbum, payload);
```
هو بيعمل:

1.  Clone للألبوم

2.  يعمل merge مع payload

* * * * *

الفنكشن الخطيرة:
----------------
```
function merge(to, from) {\
  for (let key in from) {\
    if (typeof to[key] == "object" && typeof from[key] == "object") {\
      merge(to[key], from[key]);\
    } else {\
      to[key] = from[key];\
    }\
  }\
  return to;\
}
```
مفيش فلترة\
مفيش منع لـ `__proto__`\
مفيش منع لـ `constructor`

يعني لو بعت:
```
{"__proto__": {"newProperty": "hacked"}}
```
هيعمل:
```
clonedAlbum.__proto__.newProperty = "hacked";
```
* * * * *

💥 ليه ده بيأثر على كل friend objects؟
======================================

لأن:

كل friends متخلقين من نفس الـ template\
فبيشتركوا في نفس prototype.

لما تعدّل prototype\
كلهم يتأثروا.

* * * * *

👀 طب ليه property مش باينة مباشرة؟
===================================

لو طبعت object:
```
console.log(friend);
```
مش هتشوف newProperty.

لكن لو عملت:
```
console.log(friend.newProperty);
```
هتلاقيها 👀

ليه؟

عشان JS بتدور:

1.  في object نفسه

2.  لو مش لاقي → تطلع prototype

3.  تلاقي newProperty هناك

* * * * *

🔥 ليه ظهرت في الشاشة؟
======================

في الـ EJS template:
```
<% for (let key in friend) { %>
```
`for...in`

بتلف على:

✔ properties بتاعة object\
✔ properties الموروثة من prototype

عشان كده ظهرت على الشاشة 😈

* * * * *

🧠 الصورة الكبيرة
=================

Prototype Pollution مش دايمًا بتكسر التطبيق فورًا.

لكن تقدر:

-   تضيف isAdmin

-   تضيف isLoggedIn

-   تضيف XSS payload

-   تغيّر behavior

ولما يتجمع مع XSS أو Access Control\
يبقى كارثة 🔥🔥🔥








```json
{"__proto__": {"isBanned":true}}
```

<img width="804" height="356" alt="image" src="https://github.com/user-attachments/assets/252f7e04-45aa-4186-bd84-4825e4f228aa" />

<img width="1274" height="518" alt="image" src="https://github.com/user-attachments/assets/d98db229-6eb7-4a7e-903b-86983a440776" />



   
</details>






<details>
   <summary>Denial of Service</summary>


💥 الفكرة الأساسية: Prototype Pollution → DoS
=============================================

في JavaScript كل Object بيورّث من:
```
Object.prototype
```
لو قدرت تلوّث (pollute) الـ prototype ده\
يبقى أي Object في التطبيق كله هيتأثر.

* * * * *

⚠️ المشكلة هنا إيه؟
===================

الميثود:
```
Object.prototype.toString()
```
دي بتتستخدم في كل حتة تقريبًا في JavaScript\
وأي تحويل لقيمة لـ string بيستدعيها تلقائيًا.

لو غيرناها ❌\
كل التطبيق يبدأ ينهار.

* * * * *

🧨 إزاي حصل الهجوم؟
===================

في الفورم دي:
```
<form action="/clone-album/1" method="post">
```
السيرفر بيعمل:

```
merge(object1, object2)
```
والميرج ده مش محمي ضد `__proto__`.

* * * * *

🔥 الباي لود المستخدم
=====================


```
{"__proto__": {"toString": "Just crash the server"}}
```
إحنا هنا بنقول:

> حط في البروتوتايب خاصية toString بقيمة نص عادي

يعني بدل ما تبقى function\
بقت string ❗

* * * * *

🧠 إيه اللي بيحصل داخليًا؟
==========================

1.  السيرفر يستقبل الـ JSON

2.  يعمل parse

3.  يعمل merge

4.  `__proto__` تتضاف

5.  `Object.prototype.toString` تتغير

بعدها أول ما التطبيق ينادي:
```
Object.prototype.toString.call(...)
```
يحصل:
```
TypeError: Object.prototype.toString.call is not a function
```
لأننا مسحنا الفانكشن وخليّناها String.

* * * * *

💣 النتيجة
==========

-   التطبيق ينهار

-   السيرفر يكرش

-   يحصل Denial of Service

-   كل المستخدمين يتمنعوا من الخدمة

* * * * *

🚨 ليه دي DoS؟
==============

لأن:

-   مفيش RCE

-   مفيش Data leak

-   بس السيرفر بقى مش شغال

وده كافي إنه يبقى هجوم DoS.

* * * * *

🎯 نقطة مهمة
============

مش أي فانكشن تغيرها هتكرش السيرفر.

ممكن تغيّر:

-   toJSON

-   valueOf

-   constructor

بس التأثير بيعتمد على استخدام التطبيق ليها.

* * * * *

🔄 لو السيرفر وقع
=================

ادخل على:
```
http://10.112.154.103:8080
```
عشان تعيد تشغيله.



---

```
{"__proto__": {"toString": "Just crash the server"}}
```

<img width="1159" height="496" alt="image" src="https://github.com/user-attachments/assets/bfaae769-f405-4837-b6b4-ca1712ae69e2" />

```
{"__proto__": {"toLocaleString": "Just crash the server"}}
```

<img width="1489" height="479" alt="image" src="https://github.com/user-attachments/assets/38601aea-1c44-4018-bfa4-afb537290641" />


   
</details>







<details>
   <summary>Automating the Process</summary>



🔎 ليه اكتشاف Prototype Pollution صعب؟
======================================

المشكلة الأساسية إن **JavaScript** نظامه مختلف عن لغات كتير.

فيه حاجة اسمها:
```
Object.prototype
```
أي Object في التطبيق بيورّث منه.

فلو حصل تلاعب فيه → كل Objects تتأثر.

* * * * *

🧠 ليه الأدوات مش بتلقطها بسهولة؟
---------------------------------

على عكس:

-   SQL Injection (في patterns واضحة)

-   XSS (في `<script>` مثلاً)

-   Command Injection (في `;`, `&&`)

Prototype Pollution:

❌ مفيهاش Pattern ثابت\
❌ ممكن تحصل في merge عادي جدًا\
❌ بتعتمد على منطق الكود (Business Logic)

يعني لازم تفهم:

-   الكود بيعمل merge إزاي؟

-   هل بيستخدم lodash؟

-   هل فيه deep clone؟

-   هل بياخد JSON من المستخدم؟

مش مجرد فحص سريع وخلاص.

* * * * *

🎯 إزاي البنتستر يكتشفها؟
=========================

لازم يدور على:

-   أي مكان بيعمل:

    -   `merge()`

    -   `Object.assign()`

    -   deep cloning

    -   JSON parsing من user input

ولو المستخدم يقدر يتحكم في مفاتيح زي:
```
{\
  "__proto__": {}\
}
```
أو:
```
{\
  "constructor": {\
    "prototype": {}\
  }\
}
```
يبقى في خطر 🔥

* * * * *

🛠 أدوات بتساعد في الاكتشاف
===========================

1️⃣ NodeJsScan
--------------

أداة Static Analysis لتطبيقات Node.js\
بتفحص الكود وتدور على vulnerabilities ومنها prototype pollution.

مفيدة في:

-   مرحلة التطوير

-   CI/CD

* * * * *

2️⃣ Prototype Pollution Scanner
-------------------------------

أداة مخصصة تدور على patterns اللي ممكن تسبب pollution\
بتحلل الكود وتقولك فين احتمالية المشكلة.

* * * * *

3️⃣ PPFuzz
----------

دي Fuzzer 🔥

يعني:

-   تبعت inputs مختلفة عشوائية

-   تحاول تحقن `__proto__`

-   تشوف هل التطبيق يتأثر

مفيدة جدًا في Black-box testing.

* * * * *

4️⃣ BlackFan (Client-side detection)
------------------------------------

بيركز على:

-   Prototype Pollution في المتصفح

-   إزاي تتحول لـ XSS

مهم جدًا لأن أحيانًا المشكلة تكون في Frontend مش Backend.

* * * * *

⚠️ أهم حاجة البنتستر يركز عليها
===============================

يسأل نفسه:

> هل المستخدم يقدر يتحكم في اسم property؟

مثال خطر:

```
merge(userInput, safeObject)
```
لو userInput فيه:

```
{\
  "__proto__": {\
    "isAdmin": true\
  }\
}
```
ممكن كل users يبقوا admin 💀




   
</details>









<details>
   <summary>Mitigation Measures</summary>





🛡 أولًا: دور الـ Pentester
===========================

هدفك إنك تكتشف لو التطبيق قابل لـ Prototype Pollution.

1️⃣ Input Fuzzing
-----------------

جرب تحقن Payloads زي:
```
{"__proto__":{"isAdmin":true}}
```
أو:
```
{"constructor":{"prototype":{"polluted":true}}}
```
لو لاحظت سلوك غريب → فيه مشكلة 🔥

* * * * *

2️⃣ تحليل السياق (Context Analysis)
-----------------------------------

افتح الكود وشوف:

-   هل فيه `merge()` ؟

-   هل فيه `Object.assign()` ؟

-   هل فيه deep clone؟

-   هل بيعمل parse لـ JSON من المستخدم؟

لو user يقدر يتحكم في اسم الـ key → خطر.

* * * * *

3️⃣ اختبار CSP
--------------

لو حصل Pollution في Client-side:

-   هل CSP يمنع تنفيذ سكريبت خبيث؟

-   هل تقدر تتجاوز CSP؟

ده مهم لو الهجوم هيتحول لـ XSS.

* * * * *

4️⃣ تحليل الـ Dependencies
--------------------------

مهم جدًا 👇

كتير من الثغرات بتيجي من:

-   lodash (إصدارات قديمة)

-   hoek

-   jQuery plugins

لو مكتبة قديمة → ممكن تكون أصل المشكلة.

* * * * *

5️⃣ Static Code Analysis
------------------------

استخدم أدوات تساعدك تكشف patterns خطيرة\
بس متعتمدش عليها 100%.

* * * * *

👨‍💻 ثانيًا: دور الـ Developer (الحماية)
=========================================

هنا بقى بنقفل الباب خالص 🚪

* * * * *

🚫 1️⃣ تجنب **proto**
---------------------

بلاش تستخدم:
```
obj.__proto__
```
واستخدم بدلها:
```
Object.getPrototypeOf(obj)
```
أأمن وأوضح.

* * * * *

🧱 2️⃣ Immutable Objects
------------------------

خلي الـ Objects غير قابلة للتعديل لما ينفع:
```
Object.freeze(obj)
```
ده يمنع أي تعديل على الخصائص.

* * * * *

🔒 3️⃣ Encapsulation
--------------------

متعرضش كل حاجة للمستخدم.\
خلي التعديل في نطاق محدود.

* * * * *

✅ 4️⃣ Safe Defaults
-------------------

لما تنشئ Object:

-   متاخدش prototype من user input

-   اعمل initialize ثابت وآمن

* * * * *

🧹 5️⃣ Input Sanitisation (الأهم)
---------------------------------

اعمل Block لأي مفاتيح زي:

-   `__proto__`

-   `constructor`

-   `prototype`

مثال:
```
if (key === "__proto__") reject();
```
* * * * *

📦 6️⃣ Dependency Management
----------------------------

-   حدّث المكتبات باستمرار

-   راقب CVEs

-   استخدم نسخ مستقرة

* * * * *

🛡 7️⃣ Security Headers
-----------------------

استخدم:

-   Content Security Policy (CSP)

-   X-Content-Type-Options

-   X-Frame-Options

مش هتمنع الـ Pollution نفسها\
بس تقلل تأثيرها لو اتحولت لـ XSS.





   
</details>






































