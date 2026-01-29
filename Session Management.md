# **Session Management**


<details>
  <summary>What is Session Management?</summary>



Before we discuss everything that can go wrong, we need to learn about session management. As mentioned in the previous task, you don't send your username and password with each request. However, the HTTP protocol is inherently stateless. Sessions are, therefore, used to track users throughout their use of a web application. Session management is the process of managing these sessions and ensuring that they remain secure.

Session Management Lifecycle

The best way to learn about session management is to use the session management lifecycle, as shown in the animation below.



<img width="1415" height="535" alt="image" src="https://github.com/user-attachments/assets/e9bbe3d9-d5bd-483d-90cb-d5796f89debe" />


**Session Creation**

You might think this first step in the lifecycle occurs only after you provide your credentials, such as a username and password. However, on many web applications, the initial session is already created when you visit the application. This is because some applications want to track your actions even before authentication. However, our main focus for this room will be on authenticated sessions. Once you provide your username and password, you receive a session value that is then sent with each new request. How these session values are generated, used, and stored is crucial in securing session creation.

**Session Tracking**

Once you receive your session value, this is submitted with each new request. This allows the web application to track your actions even though the HTTP protocol is stateless in nature. With each request made, the web application can recover the session value from the request and perform a server-side lookup to understand who the session belongs to and what permissions they have. In the event that there are issues in the session tracking process, it may allow a threat actor to hijack a session or impersonate one.

**Session Expiry**

Because the HTTP protocol is stateless, it may happen that a user of the web application all of a sudden stops using it. For example, you might close the tab or your entire browser. Since the protocol is stateless, the web application has no method to know that this action has occurred. This is where session expiry comes into play. Your session value itself should have a lifetime attached to it. If the lifetime expires and you submit an old session value to the web application, it should be denied as the session should have been expired. Instead, you should be redirected to the login page to authenticate again and start the session management lifecycle all over again!

**Session Termination**

However, in some cases, the user might forcibly perform a logout action. In the event that this occurs, the web application should terminate the user's session. While this is similar to session expiry, it is unique in the sense that even if the session's lifetime is still valid, the session itself should be terminated. Issues in this termination process could allow a threat actor to gain persistent access to an account.





<img width="1659" height="593" alt="image" src="https://github.com/user-attachments/assets/a4488930-e930-48f4-8045-642105eaf116" />






  
</details>



<details>
  <summary>Authentication vs Authorisation</summary>


<img width="1572" height="367" alt="image" src="https://github.com/user-attachments/assets/2f0ba870-c0f2-4d5f-b8a1-c25a636f3780" />



### **`Identification`**

Identification is the process of verifying who the user is. This starts with the user claiming to be a specific identity. In most web applications, this is performed by submitting your username. You are claiming that you are the person associated with the specific username. Some applications use uniquely created usernames, whereas others will take your email address as the username.

### **`Authentication`**

Authentication is the process of ensuring that the user is who they say they are. Where in identification, you provide a username, for authentication, you provide proof that you are who you say you are. For example, you can supply the password associated with the claimed username. The web application can confirm this information if it is valid; this is the point where session creation would kick in.

### **`Authorisation`**

Authorisation is the process of ensuring that the specific user has the rights required to perform the action requested. For example, while all users may view data, only a select few may modify it. In the session management lifecycle, session tracking plays a critical role in authorisation.

### **`Accountability`**

Accountability is the process of creating a record of the actions performed by users. We should track the user's session and log all actions performed using the specific session. This information plays a critical role in the event of a security incident to piece together what has happened.

### **`IAAA and Session Management`**

Now that you understand the differences between authentication and authorisation let's bring this back to session management. Authentication plays a role in how sessions are created. Authorisation becomes important to verify that the user associated with a specific session has the permission to perform the action they are requesting. Accountability is crucial for us to piece together what actually occurred in an incident, which means it is important that requests are logged and that the session associated with each request is also logged.






<img width="1689" height="479" alt="image" src="https://github.com/user-attachments/assets/9508c81b-f03d-457a-ae7e-7b78f57886f2" />







  
</details>





<details>
  <summary>Cookies vs Tokens</summary>



The last topic to cover before diving into session management security is the type of sessions being used. The two main approaches are cookies and tokens, each with its own benefits and drawbacks.

Cookie-Based Session Management

Cookie-based session management is often called the old-school way of managing sessions. Once the web application wants to begin tracking, in a response, the Set-Cookie header value will be sent. Your browser will interpret this header to store a new cookie value. Let's take a look at such a Set-Cookie header:

`Set-Cookie: session=12345;`

Your browser will create a cookie entry for a cookie named `session` with a value of `12345` which will be valid for the domain where the cookie was received from. Several attributes can also be added to this header. If you want to learn more about all of them, please refer [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie), but some of the noteworthy ones are:

-   **Secure** - Indicates to the browser that the cookie may only be transmitted over verified HTTPS channels. If there are certificate errors or HTTP is used, the cookie value will not be transmitted.
-   **HTTPOnly** - Indicates to the browser that the cookie value may not be read by client-side JavaScript.
-   **Expire** - Indicates to the browser when a cookie value will no longer be valid and should be removed.
-   **SameSite** - Indicates to the browser whether the cookie may be transmitted in cross-site requests to help protect against CSRF attacks.

A key thing to remember with cookie-based authentication is that the browser itself will decide when a certain cookie value will be sent with a request. After reviewing the domain and the attributes of the cookie, the browser makes this decision, and the cookie is attached automatically without any additional client-side JavaScript code.

Token-Based Session Management

Token-based session management is a relatively new concept. Instead of using the browser's automatic cookie management features, it relies on client-side code for the process. After authentication, the web application provides a token within the request body. Using client-side JavaScript code, this token is then stored in the browser's LocalStorage.

When a new request is made, JavaScript code must load the token from storage and attach it as a header. One of the most common types of tokens is JSON Web Tokens (JWT), which are passed through the `Authorization: Bearer` header. However, as we are not using the browser's built-in cookie management features, it is a bit of the wild west where anything goes. Although there are standards, nothing is really forcing anything from sticking to these standards.

Benefits and Drawbacks

The benefits and drawbacks of each of these methods are directly related, so let's take a look:





|                                                                                                                                                       |                                                                                                                                                                           |
| ----------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Cookie-Session Management**                                                                                                                         | **Token-Based Session Management**                                                                                                                                        |
| Cookie is automatically sent by the browser with each request                                                                                         | Token has to be submitted as a header with each request using client-side JavaScript                                                                                      |
| Cookie attributes can be used to enhance the browser's protection of the cookie                                                                       | Tokens do not have automatic security protections enforced and should, therefore, be safeguarded against disclosures                                                      |
| Cookies can be vulnerable to conventional client-side attacks such as CSRF, where the browser is tricked into making a request on behalf of the user. | As the token is not automatically added to any request and cannot be read from LocalStorage by other domains, conventional client-side attacks such as CSRF are blocked.  |
| As cookies are locked to a specific domain, it can be difficult to use them securely in decentralised web applications.                               | Tokens work well in decentralised web applications, as they are managed through JavaScript and can often contain all the information required to verify the token itself. |



<details>
  <summary>clearfication in arabic</summary>


🔐 أنواع Session Management
===========================

عندك طريقتين أساسيتين:\
1️⃣ **Cookie-Based Sessions**\
2️⃣ **Token-Based Sessions (زي JWT)**

الاتنين هدفهم واحد:

> يخلو السيرفر يعرف إنك نفس المستخدم بين كل request والتاني

لكن الطريقة مختلفة 🔄

* * * * *

🍪 أولًا: Cookie-Based Session Management
-----------------------------------------

### 📌 الفكرة

-   السيرفر يعمل Session

-   يبعت:

```
Set-Cookie: session=12345;
```

-   المتصفح:

    -   يخزّنها

    -   ويبعتها تلقائي مع كل request

📌 أنت كمستخدم:

-   **مش بتعمل أي حاجة**

-   المتصفح كله أوتوماتيك

* * * * *

### 🔐 Cookie Attributes المهمة

| Attribute | بيعمل إيه؟ | أهميته الأمنية |
| --- | --- | --- |
| Secure | يتبعت بس مع HTTPS | يمنع MITM |
| HttpOnly | JS ما يقراهوش | يمنع سرقة بـ XSS |
| Expire | وقت انتهاء | يمنع Sessions طويلة |
| SameSite | يمنع Cross-Site | يقلل CSRF |

📌 Cookie صح:

```
Set-Cookie: session=abc;
Secure; HttpOnly; SameSite=Strict
```





### 🚨 مشاكل Cookie Sessions

-   المتصفح **بيبعت الكوكي تلقائي**

-   ده يفتح الباب لـ:

    -   **CSRF**

    -   Session Fixation

    -   XSS (لو مفيش HttpOnly)

🎯 هجوم مشهور:\
**CSRF**

> موقع خبيث يخليك تبعت request غصب عنك

* * * * *

🔑 ثانيًا: Token-Based Session Management
-----------------------------------------

### 📌 الفكرة

-   بعد login

-   السيرفر يبعت Token في body:

```
{  "token":  "eyJhbGciOi..."  }
```

-   JavaScript:

    -   يخزّنه في LocalStorage

    -   يبعتُه يدوي مع كل request

📌 مثال:

```
Authorization: Bearer <TOKEN>
```

* * * * *

### 🧠 أشهر Token

-   **JWT (JSON Web Token)**

بيحتوي جوّه:

-   user id

-   role

-   expiration

-   signature

📌 يعني:

> السيرفر أحيانًا مش محتاج يخزّن session أصلاً

* * * * *

### 🚨 مشاكل Token Sessions

-   مفيش Secure / HttpOnly

-   لو اتسرق بـ XSS → انتهى الحساب

-   تخزينه في LocalStorage خطر جدًا

🎯 هجوم:\
**XSS → Token Theft**

* * * * *

⚖️ مقارنة سريعة (تحفظها)
------------------------

| نقطة | Cookies | Tokens |
| --- | --- | --- |
| الإرسال | تلقائي | يدوي |
| CSRF | ❌ معرض | ✔️ محمي |
| XSS | ✔️ أقل لو HttpOnly | ❌ خطر |
| تحكم المتصفح | عالي | صفر |
| مناسب للـ SPA | لا | نعم |
| Decentralized Apps | صعب | ممتاز |

* * * * *

🧠 نقطة ذكية جدًا (CTF / Real World)
------------------------------------

> ❗ **مفيش حل مثالي**

### أفضل Practice حاليًا:

-   Cookie-based

-   -   Secure

-   -   HttpOnly

-   -   SameSite=Strict

-   -   Short expiration

-   -   CSRF Token

⚠️ JWT في LocalStorage:

> مريح... بس خطر

* * * * *

🧪 مثال واقعي
-------------

### Cookie سيناريو:

-   User يفتح evil.com

-   الموقع يعمل POST للبنك

-   المتصفح يبعت cookie

-   💥 CSRF

### Token سيناريو:

-   evil.com مش عارف يقرا LocalStorage

-   مفيش token

-   ✔️ الهجوم يفشل

لكن...

### XSS؟

-   JS يسرق token

-   ✔️ الحساب راح

* * * * *

🎯 الخلاصة الذهبية
------------------

> Cookies = Browser-controlled\
> Tokens = JavaScript-controlled

> Cookies تحميك من XSS\
> Tokens تحميك من CSRF

لكن الاتنين:

> **محتاجين تأمين صح**










  
</details>







<img width="1647" height="489" alt="image" src="https://github.com/user-attachments/assets/8b263a4f-7289-4137-840b-efab93b36e4e" />






  
</details>







<details>
  <summary>Securing the Session Lifecycle</summary>








## **Session Creation**

Session creation is where the most vulnerabilities can creep in. Let's dive into a couple of the common ones.

## **Weak Session Values**

It is less common to see weak session values in modern times as frameworks are consistently used. However, with the rise of LLMs and other AI code-assistant solutions, you would be surprised at how often these old-school vulnerabilities are creeping back in.

If a custom session creation mechanism has been implemented, there is a good chance that the session values may be guessable. A good example of this is a mechanism that simply base64 encodes the username as the session value. If a threat actor can reverse engineer the session creation process, they can generate or guess session values to hijack the accounts of legitimate users.

## **Controllable Session Values**

In certain tokens, such as JWTs, all the relevant information to both create and verify the JWT's validity is provided. If security measures are not enforced, such as verifying the token's signature or ensuring that the signature itself was created securely, a threat actor would be able to generate their own token. These types of attacks will be discussed in more detail in a future room.

## **Session Fixation**

Remember the web application that already gave you a session before authentication? These web applications can be vulnerable to something called session fixation. If your session value is not adequately rotated once you authenticate, a suitably positioned threat actor could record it when you are still unauthenticated and wait for you to authenticate to gain access to your session.

## **Insecure Session Transmission**

In modern environments, it is common for the authentication server and the application servers to be distinct. Think about things like Single Sign-On (SSO) solutions. One application is used for authentication to several other web applications. In order for this process to work, your session material must be transferred from the authentication server to the application server via your browser. In this transmission, however, certain issues can creep in that would expose your session information to a threat actor. The most common is an insecure redirect where the threat actor can control the URL where you will be redirected to post-authentication. This could allow the threat actor to hijack your session. This isn't just with custom implementations, Oracle's SSO solution had a [massive bug that allowed for this to happen](https://krbtgt.pw/oracle-oam-10g-session-hijacking/).

## **Session Tracking**

Session tracking is the second largest culprit of vulnerabilities. Let's take a look.

## **Authorisation Bypass**

Authorisation bypasses occur when there aren't sufficient checks being performed on whether a user is allowed to perform the action they requested. In essence, this fails to track the user's session and its associated rights correctly. It is also worth talking about the two types of authorisation bypasses:

-   Vertical bypass - You can perform an action that is reserved for a more privileged user
-   Horizontal bypass - You can perform an action you are allowed to perform, but on a dataset that you should not be allowed to perform the action on

In most applications, vertical bypasses are easy to defend against since function decorators and path-based access control configurations are used. However, with horizontal bypasses, the user is performing an action that they should be allowed to perform. The issue is that they are performing it on someone else's data. To remedy this, actual code is required to verify who the user is (extracted from their session), which data they are requesting, and if they are allowed to request or modify the dataset.

## **Insufficient Logging**

A key issue during incidents is not having sufficient information to piece together an attack. While a lot of logging will occur at an infrastructure level, application logging can be crucial to understanding what went wrong. In the event that the actions performed by a specific session and the ability to retrace that session to a user do not exist, it can leave gaps in the investigation that cannot be filled. It is also worth making sure that logs cover both accepted and rejected actions. In the event of a session hijacking attack, the actions would appear legitimate. Therefore, simply logging rejected actions is not sufficient to paint the picture.

## **Session Expiry**

Session expiry only has a single vulnerability, which is when the expiry time for sessions are excessive. A session should be seen as a ticket to a movie. Each night, the same movie is shown, but we don't want someone to be able to use the same ticket to watch the movie again. The same counts for sessions, we need to make sure that our session expiry time takes into consideration our specific application's use case. A banking application should have a shorter session lifetime than your webmail client.

Furthermore, in the event of long-life sessions, such as those for a webmail client, the session itself should attest to the location where it is used. If this location changes (which could be an indication of session hijacking), the session should be terminated.

## **Session Termination**

For session termination, the key issue is when sessions are not properly terminated server-side when the logout action is performed. Suppose a threat actor were to hijack a user's session. In that case, even if the user became aware of the issue, without the ability to invalidate the session server-side, there isn't a method for the user to flush the access of the threat actor. However, this can be quite an issue for tokens where the lifetime of the token is embedded in the token itself. In these cases, the token can be added to a blocklist to be verified against. Some applications also take this further where all the sessions of the user can be viewed and terminated. Furthermore, upon a successful password reset, it is also recommended that all sessions are terminated to allow a user to regain full control of their account.
















<img width="1687" height="594" alt="image" src="https://github.com/user-attachments/assets/b4113e66-52a2-4cb4-8c8b-3c7c6d37d201" />



  
</details>





<details>
  <summary>Exploiting Insecure Session Management</summary>

> ### login with any username and password as student  


<img width="1731" height="701" alt="image" src="https://github.com/user-attachments/assets/79bbafe8-754e-4ea0-8ccc-e2941a45a28d" />


> ## if you go to local storage you will find 

-   `user`

    -   `userRole`

    -   `id`

    -   `username`


> ## try to change role form **`student`** to **`lecturer`**


<img width="1919" height="691" alt="image" src="https://github.com/user-attachments/assets/9c895360-9cbe-42f3-b2f2-6f3e09c66b08" />


<img width="1118" height="792" alt="image" src="https://github.com/user-attachments/assets/e1cfc834-6a6b-41a2-9def-8c929a955e27" />



<img width="1673" height="837" alt="image" src="https://github.com/user-attachments/assets/118e811a-524c-4f52-9ba1-13b65cc04657" />




  
</details>



<details>
  <summary>summary</summary>




Defences

To defend against these attacks, it is important to implement a secure session management lifecycle. While several items were touched on in this room, let's take a look at a recap:

-   The session's values must be stored securely, regardless of being a cookie or a token.
-   The session values themselves must be either sufficiently random and non-guessable or use a signing mechanism to ensure that they cannot be tampered with.
-   Sessions should be used to track user actions and perform authorisation checks to ensure the user can perform the requested action.
-   Sessions should expire after a set amount of time to prevent them from being used for persistent access.
-   If the logout button is pressed, the session should be removed client-side and invalidated server-side. Otherwise, a user would be unable to destroy their session if it was compromised.



  
</details>
















































