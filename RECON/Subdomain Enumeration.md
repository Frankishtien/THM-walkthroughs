# Subdomain Enumeration


<img width="1905" height="377" alt="image" src="https://github.com/user-attachments/assets/0eadfe92-cc60-4819-8c6b-723e2198494d" />





<details>
  <summary>Brief</summary>



<img width="1637" height="767" alt="image" src="https://github.com/user-attachments/assets/78326c1e-364d-49c0-88b5-83cf57b930e8" />



  
</details>





<details>
  <summary>OSINT - SSL/TLS Certificates</summary>


When an SSL/TLS (Secure Sockets Layer/Transport Layer Security) certificate is created for a domain by a CA (Certificate Authority), CA's take part in what's called "Certificate Transparency (CT) logs". These are publicly accessible logs of every SSL/TLS certificate created for a domain name. The purpose of Certificate Transparency logs is to stop malicious and accidentally made certificates from being used. We can use this service to our advantage to discover subdomains belonging to a domain, sites like [https://crt.sh](https://crt.sh/) offer a searchable database of certificates that shows current and historical results.

Go to [crt.sh](https://crt.sh/) and search for the domain name **tryhackme.com**, find the entry that was logged at** 2020-12-26** and enter the domain below to answer the question.





<details>
  <summary>info</summary>




🔐 الفكرة الأول (ببساطة)
------------------------

-   أي موقع بيعمل **SSL/TLS Certificate** لازم يطلع في حاجة اسمها\
    **Certificate Transparency Logs (CT Logs)**

-   اللوجز دي **عامة** ومفتوحة

-   نقدر نستخدمها علشان نطلع:

    -   Subdomains

    -   Domains قديمة

    -   Domains مخفية مش ظاهرة في DNS عادي

📌 موقع **crt.sh** بيعرض اللوجز دي.

* * * * *

🧠 ليه ده مهم في OSINT؟
-----------------------

لأن:

-   بعض الـ subdomains **مش بتظهر في tools عادية**

-   لكنها بتظهر في شهادات SSL

-   مفيد جدًا في مرحلة **Reconnaissance**

* * * * *

🛠️ خطوات الحل (Step by Step)
-----------------------------

### 1️⃣ افتح الموقع:

👉 <https://crt.sh>

* * * * *

### 2️⃣ في خانة البحث اكتب:

`tryhackme.com`

واضغط **Search**

* * * * *

### 3️⃣ هتطلعلك نتائج كتير

كل نتيجة فيها:

-   Certificate ID

-   Logged At (تاريخ)

-   Common Name

-   Matching Identities





  
</details>




<img width="1104" height="147" alt="image" src="https://github.com/user-attachments/assets/856ef836-ca81-4d02-b548-d6c7987f3081" />








  
</details>




<details>
  <summary>OSINT - Search Engines</summary>





<img width="1644" height="544" alt="image" src="https://github.com/user-attachments/assets/1c9f300e-5b03-43a8-a1c7-7a87be25151c" />




  
</details>




<details>
  <summary>DNS Bruteforce</summary>



<img width="1919" height="734" alt="image" src="https://github.com/user-attachments/assets/b580fb35-001b-4465-88ff-1c8e45d41ba8" />




  
</details>



<details>
  <summary>OSINT - Sublist3r</summary>



<img width="1919" height="610" alt="image" src="https://github.com/user-attachments/assets/9fcdfac6-e080-42a6-b20d-218a29ff348d" />



  
</details>



<details>
  <summary>Virtual Hosts</summary>




<img width="1459" height="882" alt="image" src="https://github.com/user-attachments/assets/a5f10bc6-1539-4ab5-9ff9-1ba436d850c2" />


-----


![Uploading image.png…]()






  
</details>



















