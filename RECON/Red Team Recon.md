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


































