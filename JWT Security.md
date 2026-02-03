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




















  
</details>












































