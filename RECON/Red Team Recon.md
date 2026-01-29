# Red Team Recon


<img width="1906" height="380" alt="image" src="https://github.com/user-attachments/assets/b4cccdc8-4be0-445d-ad24-91579a27dec6" />




<details>
  <summary>Taxonomy of Reconnaissance</summary>


Reconnaissance (recon) can be classified into two parts:

1.  **Passive Recon**: can be carried out by watching passively
2.  **Active Recon**: requires interacting with the target to provoke it in order to observe its response.

Passive recon doesn't require interacting with the target. In other words, you aren't sending any packets or requests to the target or the systems your target owns. Instead, passive recon relies on publicly available information that is collected and maintained by a third party. Open Source Intelligence (OSINT) is used to collect information about the target and can be as simple as viewing a target's publicly available social media profile. Example information that we might collect includes domain names, IP address blocks, email addresses, employee names, and job posts. In the upcoming task, we'll see how to query DNS records and expand on the topics from the [Passive Reconnaissance](https://tryhackme.com/room/passiverecon) room and introduce advanced tooling to aid in your recon.

Active recon requires interacting with the target by sending requests and packets and observing if and how it responds. The responses collected - or lack of responses - would enable us to expand on the picture we started developing using passive recon. An example of active reconnaissance is using Nmap to scan target subnets and live hosts. Other examples can be found in the [Active Reconnaissance](https://tryhackme.com/room/activerecon) room. Some information that we would want to discover include live hosts, running servers, listening services, and version numbers.

Active recon can be classified as:

1.  **External Recon**: Conducted outside the target's network and focuses on the externally facing assets assessable from the Internet. One example is running Nikto from outside the company network.
2.  **Internal Recon**: Conducted from within the target company's network. In other words, the pentester or red teamer might be physically located inside the company building. In this scenario, they might be using an exploited host on the target's network. An example would be using Nessus to scan the internal network using one of the target's computers.

  
</details>





<details>
  <summary>Built-in Tools</summary>



This task focuses on:

-   `whois`
-   `dig`, `nslookup`, `host`
-   `traceroute`/`tracert`




<img width="906" height="735" alt="image" src="https://github.com/user-attachments/assets/532fefae-07f5-47d4-8b66-ad42f34c680b" />




<img width="594" height="346" alt="image" src="https://github.com/user-attachments/assets/a779c916-b99e-4ff8-b587-51056e183bc6" />


<img width="805" height="474" alt="image" src="https://github.com/user-attachments/assets/2718b472-9c4c-40f2-9d67-908cf9596b16" />


<img width="663" height="181" alt="image" src="https://github.com/user-attachments/assets/4d9640f2-479e-412c-a82b-5f1413493207" />

<img width="1221" height="665" alt="image" src="https://github.com/user-attachments/assets/8017f4c3-9b3e-4a24-b689-ca39c82c907f" />


<img width="1242" height="501" alt="image" src="https://github.com/user-attachments/assets/21d1c0c1-4a2b-48a1-945b-5ff1bf23e3cd" />




  
</details>





<details>
  <summary>Advanced Searching</summary>



Being able to use a search engine efficiently is a crucial skill. The following table shows some popular search modifiers that work with many popular search engines.

| Symbol / Syntax | Function |
| --- | --- |
| `"search phrase"` | Find results with exact search phrase |
| `OSINT filetype:pdf` | Find files of type `PDF` related to a certain term. |
| `salary site:blog.tryhackme.com` | Limit search results to a specific site. |
| `pentest -site:example.com` | Exclude a specific site from results |
| `walkthrough intitle:TryHackMe` | Find pages with a specific term in the page title. |
| `challenge inurl:tryhackme` | Find pages with a specific term in the page URL. |

Note: In addition to `pdf`, other filetypes to consider are: `doc`, `docx`, `ppt`, `pptx`, `xls` and `xlsx`.

Each search engine might have a slightly varied set of rules and syntax. To learn about the specific syntax for the different search engines, you will need to visit their respective help pages. Some search engines, such as Google, provide a web interface for advanced searches: [Google Advanced Search](https://www.google.com/advanced_search). Other times, it is best to learn the syntax by heart, such as [Google Refine Web Searches](https://support.google.com/websearch/answer/2466433), [DuckDuckGo Search Syntax](https://help.duckduckgo.com/duckduckgo-help-pages/results/syntax/), and [Bing Advanced Search Options](https://help.bing.microsoft.com/apex/index/18/en-US/10002).

Search engines crawl the world wide web day and night to index new web pages and files. Sometimes this can lead to indexing confidential information. Examples of confidential information include:

-   Documents for internal company use
-   Confidential spreadsheets with usernames, email addresses, and even passwords
-   Files containing usernames
-   Sensitive directories
-   Service version number (some of which might be vulnerable and unpatched)
-   Error messages

Combining advanced Google searches with specific terms, documents containing sensitive information or vulnerable web servers can be found. Websites such as [Google Hacking Database](https://www.exploit-db.com/google-hacking-database) (GHDB) collect such search terms and are publicly available. Let's take a look at some of the GHDB queries to see if our client has any confidential information exposed via search engines. GHDB contains queries under the following categories:

-   **Footholds**\
    Consider [GHDB-ID: 6364](https://www.exploit-db.com/ghdb/6364) as it uses the query `intitle:"index of" "nginx.log"` to discover Nginx logs and might reveal server misconfigurations that can be exploited.
-   **Files Containing Usernames**\
    For example, [GHDB-ID: 7047](https://www.exploit-db.com/ghdb/7047) uses the search term `intitle:"index of" "contacts.txt"` to discover files that leak juicy information.
-   **Sensitive Directories**\
    For example, consider [GHDB-ID: 6768](https://www.exploit-db.com/ghdb/6768), which uses the search term `inurl:/certs/server.key` to find out if a private RSA key is exposed.
-   **Web Server Detection**\
    Consider [GHDB-ID: 6876](https://www.exploit-db.com/ghdb/6876), which detects GlassFish Server information using the query `intitle:"GlassFish Server - Server Running"`.
-   **Vulnerable Files**\
    For example, we can try to locate PHP files using the query `intitle:"index of" "*.php"`, as provided by [GHDB-ID: 7786](https://www.exploit-db.com/ghdb/7786).
-   **Vulnerable Servers**\
    For instance, to discover SolarWinds Orion web consoles, [GHDB-ID: 6728](https://www.exploit-db.com/ghdb/6728) uses the query `intext:"user name" intext:"orion core" -solarwinds.com`.
-   **Error Messages**\
    Plenty of useful information can be extracted from error messages. One example is [GHDB-ID: 5963](https://www.exploit-db.com/ghdb/5963), which uses the query `intitle:"index of" errors.log` to find log files related to errors.

You might need to adapt these Google queries to fit your needs as the queries will return results from all web servers that fit the criteria and were indexed. To avoid legal issues, it is best to refrain from accessing any files outside the scope of your legal agreement.

We recommend you join the [Google Dorking](https://tryhackme.com/room/googledorking) room for more in-depth information.

Now we'll explore two additional sources that can provide valuable information without interacting with our target:

-   Social Media
-   Job ads

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/f94dadbbcf2c644230d6eb310e159ed5.png)

### Social Media

Social media websites have become very popular for not only personal use but also for corporate use. Some social media platforms can reveal tons of information about the target. This is especially true as many users tend to overshare details about themselves and their work. To name a few, it's worthwhile checking the following:

-   LinkedIn
-   Twitter
-   Facebook
-   Instagram

Social media websites make it easy to collect the names of a given company's employees; moreover, in certain instances, you might learn specific pieces of information that can reveal answers to password recovery questions or gain ideas to include in a targeted wordlist. Posts from technical staff might reveal details about a company's systems and vendors. For example, a network engineer who was recently issued Juniper certifications may allude to Juniper networking infrastructure being used in their employer's environment.

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/cf84f21108b6aae75e1fa73018bf12db.png)

### Job Ads

Job advertisements can also tell you a lot about a company. In addition to revealing names and email addresses, job posts for technical positions could give insight into the target company's systems and infrastructure. The popular job posts might vary from one country to another. Make sure to check job listing sites in the countries where your client would post their ads. Moreover, it is always worth checking their website for any job opening and seeing if this can leak any interesting information.

Note that the [Wayback Machine](https://archive.org/web/) can be helpful to retrieve previous versions of a job opening page on your client's site.





<details>
  <summary>examples in arabic</summary>



🪜 Footholds
------------

`intitle:"index of" "nginx.log"`

📌 Logs مفتوحة → misconfig → exploit

* * * * *

👤 Files Containing Usernames
-----------------------------

`intitle:"index of" "contacts.txt"`

📌 كنز معلومات:

-   Emails

-   Names

-   أحيانًا passwords

* * * * *

📁 Sensitive Directories
------------------------

`inurl:/certs/server.key`

🚨 كارثة لو RSA key مكشوف

* * * * *

🖥️ Web Server Detection
------------------------

`intitle:"GlassFish Server - Server Running"`

📌 تعرف نوع السيرفر وإصداره

* * * * *

🐞 Vulnerable Files
-------------------

`intitle:"index of" "*.php"`

📌 ملفات قديمة / misconfigured

* * * * *

🌐 Vulnerable Servers
---------------------

`intext:"user name" intext:"orion core"`

📌 SolarWinds console مكشوف

* * * * *

❌ Error Messages
----------------

`intitle:"index of" errors.log`

📌 Error = معلومات:

-   paths

-   usernames

-   versions




| مصدر         | ليه مهم         |
| ------------ | --------------- |
| Google Dorks | أسرع تسريب      |
| GHDB         | كنز جاهز        |
| Social Media | Human weakness  |
| Job Ads      | Tech stack رسمي |
| Wayback      | تاريخ منسي      |






  
</details>















<img width="1346" height="347" alt="image" src="https://github.com/user-attachments/assets/7d5b691c-64fc-4c7c-b935-faf0fc11b5dd" />











  
</details>












<details>
  <summary>Specialized Search Engines</summary>







### WHOIS and DNS Related

Beyond the standard WHOIS and DNS query tools that we covered in Task 3, there are third parties that offer paid services for historical WHOIS data. One example is WHOIS history, which provides a history of WHOIS data and can come in handy if the domain registrant didn't use WHOIS privacy when they registered the domain.

There are a handful of websites that offer advanced DNS services that are free to use. Some of these websites offer rich functionality and could have a complete room dedicated to exploring one domain. For now, we'll focus on key DNS related aspects. We will consider the following:

-   [ViewDNS.info](https://viewdns.info/)
-   [Threat Intelligence Platform](https://threatintelligenceplatform.com/)

### ViewDNS.info

[ViewDNS.info](https://viewdns.info/) offers *Reverse IP Lookup*. Initially, each web server would use one or more IP addresses; however, today, it is common to come across shared hosting servers. With shared hosting, one IP address is shared among many different web servers with different domain names. With reverse IP lookup, starting from a domain name or an IP address, you can find the other domain names using a specific IP address(es).

In the figure below, we used reverse IP lookup to find other servers sharing the same IP addresses used by `cafe.thmredteam.com`. Therefore, it is important to note that knowing the IP address does not necessarily lead to a single website.


<img width="754" height="729" alt="image" src="https://github.com/user-attachments/assets/d52b51e5-a1f3-4e8b-add9-7b593e272e80" />



### Threat Intelligence Platform

[Threat Intelligence Platform](https://threatintelligenceplatform.com/) requires you to provide a domain name or an IP address, and it will launch a series of tests from malware checks to WHOIS and DNS queries. The WHOIS and DNS results are similar to the results we would get using `whois` and `dig`, but Threat Intelligence Platform presents them in a more readable and visually appealing way. There is extra information that we get with our report. For instance, after we look up `thmredteam.com`, we see that Name Server (NS) records were resolved to their respective IPv4 and IPv6 addresses, as shown in the figure below.



<img width="777" height="440" alt="image" src="https://github.com/user-attachments/assets/374d9a08-b4fb-4696-94ec-6813e910d16e" />



On the other hand, when we searched for cafe.thmredteam.com, we could also get a list of other domains on the same IP address. The result we see in the figure below is similar to the results we obtained using ViewDNS.info.


<img width="780" height="473" alt="image" src="https://github.com/user-attachments/assets/e8805f46-1869-47ef-9afe-3cc1f89fed79" />



### Specialized Search Engines

#### Censys

[Censys Search](https://search.censys.io/) can provide a lot of information about IP addresses and domains. In this example, we look up one of the IP addresses that `cafe.thmredteam.com` resolves to. We can easily infer that the IP address we looked up belongs to Cloudflare. We can see information related to ports 80 and 443, among others; however, it's clear that this IP address is used to server websites other than `cafe.thmredteam.com`. In other words, this IP address belongs to a company other than our client, [Organic Cafe](https://cafe.thmredteam.com/). It's critical to make this distinction so that we don't probe systems outside the scope of our contract.



<img width="779" height="544" alt="image" src="https://github.com/user-attachments/assets/6974581b-6b15-4850-bdcc-ec3ba280a61f" />



#### Shodan

You might remember using [Shodan](https://www.shodan.io/) in the [Passive Reconnaissance](https://tryhackme.com/room/passiverecon) room. In this section, we will demonstrate how to use Shodan from the command line.

To use Shodan from the command-line properly, you need to create an account with [Shodan](https://www.shodan.io/), then configure `shodan` to use your API key using the command, `shodan init API_KEY`.

You can use different filters depending on the [type of your Shodan account](https://account.shodan.io/billing). To learn more about what you can do with `shodan`, we suggest that you check out [Shodan CLI](https://cli.shodan.io/). Let's demonstrate a simple example of looking up information about one of the IP addresses we got from `nslookup cafe.thmredteam.com`. Using `shodan host IP_ADDRESS`, we can get the geographical location of the IP address and the open ports, as shown below.

```bash
pentester@TryHackMe$ shodan host 172.67.212.249

172.67.212.249
City:                    San Francisco
Country:                 United States
Organisation:            Cloudflare, Inc.
Updated:                 2021-11-22T05:55:54.787113
Number of open ports:    5

Ports:
     80/tcp  
    443/tcp  
	|-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2, TLSv1.3
   2086/tcp  
   2087/tcp  
   8080/tcp 
```












<img width="1673" height="217" alt="image" src="https://github.com/user-attachments/assets/06fb86ec-485f-42bd-98d5-61a3ffa967f1" />


  
</details>





















<details>
  <summary>Recon-ng</summary>




<details>
  <summary>summary in arabic</summary>




Recon-ng ببساطة
---------------

**Recon-ng** هو:

-   Framework

-   بيشتغل بـ **Modules**

-   هدفه: **أتمتة الـ OSINT**

-   كل حاجة بتطلع **بتتخزن تلقائي في Database**

يعني:

> بدل ما تجمع معلومات وتضيعها...\
> Recon-ng بيبني Knowledge Base عن الهدف 🔥

* * * * *

ليه Recon-ng مهم كـ Pentester / Red Teamer؟
-------------------------------------------

تقدر من خلاله:

-   تجيب Domains / Subdomains

-   Emails (للـ phishing)

-   Hosts (للـ port scanning لاحقًا)

-   Profiles (LinkedIn / GitHub)

-   APIs خارجية (Google, Shodan, HaveIBeenPwned...)

📌 **كل معلومة بتغذي اللي بعدها**

* * * * *

1️⃣ تشغيل Recon-ng
------------------

من التيرمينال:

`recon-ng`

هيظهرلك:

`[recon-ng][default] > `

ده معناه:

-   انت داخل

-   Workspace اسمه `default`

* * * * *

2️⃣ Workspaces (مهم جدًا)
-------------------------

الـ **Workspace** = مشروع مستقل

ليه؟

-   كل هدف له Database لوحده

-   مفيش لخبطه بين عملاء

### إنشاء Workspace جديد:

`workspaces create thmredteam`

### تشغيل Recon-ng بالـ Workspace ده:

`recon-ng -w thmredteam`

هتشوف:

`[recon-ng][thmredteam] > `

* * * * *

3️⃣ Database جوه Recon-ng
-------------------------

Recon-ng عنده Database داخلية (زي SQLite)

### تشوف الجداول الموجودة:

`db schema`

هتلاقي Tables زي:

-   domains

-   hosts

-   contacts

-   credentials

-   companies

-   vulnerabilities

* * * * *

4️⃣ Seeding the Database (زرع أول معلومة 🌱)
--------------------------------------------

الـ Recon دايمًا بيبدأ بـ **Seed**\
يعني:

> معلومة أولية → توسّع منها

في المثال بتاعنا:

-   إحنا عارفين:

`thmredteam.com`

وده **Domain**

### إدخاله في جدول domains:

`db insert domains`

هيطلب منك قيمة:

`domain (TEXT) > thmredteam.com`

كده انت:\
✅ زرعت أول معلومة\
✅ جاهز تبدأ تستخدم Modules تعتمد عليها

* * * * *

5️⃣ البحث عن Modules (Marketplace)
----------------------------------

قبل ما تستخدم أي Module:

### دور عليه:

`marketplace search domain`

### تعرف بيعمل إيه:

`marketplace info MODULE_NAME`

### تثبيته:

`marketplace install MODULE_NAME`

* * * * *

6️⃣ تحميل Module
----------------

بعد التثبيت:

`modules load MODULE_NAME`

مثال:

`modules load recon/domains-hosts/bing_domain_web`

* * * * *

7️⃣ تشغيل Module
----------------

بعد التحميل:

`run`

📌 الـ Module:

-   بياخد Input من الـ DB

-   يطلع Output

-   يخزنه تلقائي في Tables تانية

يعني:

> Domain → Subdomains → Hosts → IPs 🔥

* * * * *

الصورة الكبيرة 🧠
-----------------

Recon-ng شغال كده:

`Seed (Domain)
   ↓
Modules
   ↓ Database ↓
More Modules
   ↓
Attack Surface `









  
</details>

















```
recon-ng
workspaces create thmredteam
recon-ng -w thmredteam
db schema

```


<img width="1309" height="761" alt="image" src="https://github.com/user-attachments/assets/332ac84a-87ea-4a1e-8a2c-b645ec78530a" />




```
db insert domains
> thmredteam.com

```


<img width="880" height="505" alt="image" src="https://github.com/user-attachments/assets/17134cd8-a00c-47b1-b336-aa9990d2dab2" />





```
marketplace search domain
marketplace info MODULE_NAME
marketplace install MODULE_NAME
modules load MODULE_NAME

```

<img width="1115" height="667" alt="image" src="https://github.com/user-attachments/assets/d29f3bd6-578f-4244-9d77-92ff4c58898a" />




---


<img width="883" height="255" alt="image" src="https://github.com/user-attachments/assets/f4b43655-4828-496b-8af1-ffdb1bb1c70b" />


<img width="867" height="256" alt="image" src="https://github.com/user-attachments/assets/e9222c2e-8266-4a5d-90a2-0a49f8e5fca6" />


<img width="1288" height="497" alt="image" src="https://github.com/user-attachments/assets/b508bf37-113a-430a-8dc8-51cb66806335" />




  
</details>




<details>
  <summary>Maltego</summary>







<details>
  <summary>summary in arabic</summary>







Maltego يعني إيه؟
-----------------

**Maltego** =\
🧠 **Mind-Mapping** + 🔍 **OSINT**

يعني:

-   بدل ما تشوف معلومات مبعثرة

-   بتشوفها **Graph مرسومة**

-   العلاقات بين الناس، الدومينات، الإيميلات، الـ IPs واضحة قدامك

* * * * *

بتبدأ بإيه؟
-----------

أي **معلومة واحدة**:

-   Domain

-   Company name

-   Person name

-   Email

دي اسمها **Entity (كيان)**

📦 كل مربع في Maltego = **Entity**

* * * * *

Entity = إيه؟
-------------

الـ Entity:

-   حاجة موجودة في الحقيقة (Domain, Email, IP, Person...)

-   ليها **Value** (قيمة)

مثال:

`DNS Name → cafe.thmredteam.com `

* * * * *

Transform = القوة الحقيقية ⚡
----------------------------

**Transform** =\
كود بيعمل:

> ياخد Entity → يسأل API / Database → يطلع Entities جديدة

📌 في Maltego:

-   Right click

-   اختر Transform

-   النتيجة تظهر تلقائيًا في الجراف

* * * * *

مثال عملي (مهم جدًا)
--------------------

### البداية:

Entity:

`DNS Name:  cafe.thmredteam.com  `

### Transform:

`Standard Transforms
→ Resolve to IP
→ To IP Address (DNS) `

### النتيجة:

`IP Address: 104.xxx.xxx.xxx `

* * * * *

نكمّل السلسلة 🔗
----------------

بعد ما جبت IP:

### Transform جديد:

`DNS from IP
→ To DNS Name  from passive DNS (Robtex) `

### النتيجة:

-   Domains تانية على نفس الـ IP

-   Shared hosting

-   Infrastructure أكبر

📌 هنا بتفهم:

> الهدف مش لوحده\
> في شبكة كاملة حوالينه

* * * * *

Passive vs Active Recon ⚠️
--------------------------

نقطة مهمة جدًا:

🔵 **Passive Transforms**

-   WHOIS

-   Passive DNS

-   APIs

-   مفيش اتصال مباشر بالهدف

🔴 **Active Transforms**

-   بتعمل اتصال مباشر

-   ممكن تسيب Log

-   خطر لو خارج Scope

👉 لازم تعرف الـ Transform بيعمل إيه قبل ما تشغّله

* * * * *

ليه Maltego قوي؟
----------------

لأنه:

-   بينظم الداتا تلقائي

-   بيطلع علاقات مش واضحة

-   بيغنيك عن:

    -   عشر مواقع

    -   نسخ ولصق

    -   لخبطة

وكل ده:\
👉 **بضغطات زرار**

* * * * *

Maltego و WHOIS / nslookup
--------------------------

انت جربت:

-   whois

-   nslookup

Maltego:

-   جاب نفس المعلومات

-   **رتّبها**

-   **ربطها ببعض**

-   **رسمها بصريًا**

حتى الإيميلات اللي طالعة من WHOIS:

-   Maltego استخرجها

-   حطها في مكانها الصح

* * * * *

Transform Hub 🧩
----------------

هنا بقى السوبر باور 💥

Transforms متقسمة:

-   Free

-   Community

-   Paid

Categories:

-   DNS

-   Social Media

-   Breaches

-   Threat Intel

-   Crypto

-   Infrastructure

📌 Community Edition:

-   تكفي جدًا للتعلم

-   ممتازة للـ OSINT

* * * * *

استخدام Maltego في الهجوم (بشكل قانوني 😉)
------------------------------------------

اللي بتجمعه ممكن يُستخدم في:

-   Phishing واقعي جدًا

-   Targeted wordlists

-   Mapping البنية التحتية

-   Pre-engagement Recon

* * * * *

الصورة الذهنية الصح 🧠
----------------------

`Entity
  ↓ Transform ↓
New Entities
  ↓
More Transforms
  ↓
Big Picture  `





  
</details>















[Maltego](https://www.maltego.com/) is an application that blends mind-mapping with OSINT. In general, you would start with a domain name, company name, person's name, email address, etc. Then you can let this piece of information go through various transforms.

The information collected in Maltego can be used for later stages. For instance, company information, contact names, and email addresses collected can be used to create very legitimate-looking phishing emails.

Think of each block on a Maltego graph as an entity. An entity can have values to describe it. In Maltego's terminology, a **transform** is a piece of code that would query an API to retrieve information related to a specific entity. The logic is shown in the figure below. *Information* related to an entity goes via a *transform* to return zero or more entities.




<img width="1453" height="526" alt="image" src="https://github.com/user-attachments/assets/6a957329-f5cf-40d1-9516-b06651d7b1c5" />



It is crucial to mention that some of the transforms available in Maltego might actively connect to the target system. Therefore, it is better to know how the transform works before using it if you want to limit yourself to passive reconnaissance.

Every transform might lead to several new values. For instance, if we start from the "DNS Name" `cafe.thmredteam.com`, we expect to get new kinds of entities based on the transform we use. For instance, "To IP Address" is expected to return IP addresses as shown next.




<img width="1448" height="540" alt="image" src="https://github.com/user-attachments/assets/b03ac59b-d3ad-4a70-acd8-8edcffccd463" />




One way to achieve this on Maltego is to right-click on the "DNS Name" `cafe.thmredteam.com` and choose:

1.  Standard Transforms
2.  Resolve to IP
3.  To IP Address (DNS)

After executing this transform, we would get one or more IP addresses, as shown below.

<img width="588" height="622" alt="image" src="https://github.com/user-attachments/assets/99fd5b02-dfc9-4be4-85dc-72a4c86edf38" />



Then we can choose to apply another transform for one of the IP addresses. Consider the following transform:

1.  DNS from IP
2.  To DNS Name from passive DNS (Robtex)

This transform will populate our graph with new DNS names. With a couple more clicks, you can get the location of the IP address, and so on. The result might be similar to the image below.





<img width="784" height="399" alt="image" src="https://github.com/user-attachments/assets/b7226d0d-f38c-4b61-b806-37d77a125a66" />



The above two examples should give you an idea of the workflow using Maltego. You can observe that all the work is based on transforms, and Maltego will help you keep your graph organized. You would get the same results by querying the different online websites and databases; however, Maltego helps you get all the information you need with a few clicks.

We experimented with `whois` and `nslookup` in a previous task. You get plenty of information, from names and email addresses to IP addresses. The results of `whois` and `nslookup` are shown visually in the following Maltego graph. Interestingly, Maltego transforms were able to extract and arrange the information returned from the WHOIS database. Although the returned email addresses are not helpful due to privacy protection, it is worth seeing how Maltego can extract such information and how it's presented.



<img width="1445" height="835" alt="image" src="https://github.com/user-attachments/assets/95373fed-3e02-457b-80e6-bbbfacb475a2" />



Now that we have learned how Maltego’s power stems from its transforms, the only logical thing is to make Maltego more powerful by adding new Transforms. Transforms are usually grouped into different categories based on data type, pricing, and target audience. Although many transforms can be used using Maltego Community Edition and free transforms, other transforms require a paid subscription. A screenshot is shown below to give a clearer idea.



<img width="1385" height="851" alt="image" src="https://github.com/user-attachments/assets/9418a395-0a9b-47dc-9518-f742be879788" />



---
---





<img width="1648" height="343" alt="image" src="https://github.com/user-attachments/assets/009bff37-b868-43a8-b1b7-d5ecb9686207" />










  
</details>























































