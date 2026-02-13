# Intro to SSRF

<img width="1904" height="377" alt="image" src="https://github.com/user-attachments/assets/70b32900-60fd-4e71-8f8f-b1814fa0ea62" />


<details>
  <summary>SSRF Examples</summary>

<img width="947" height="862" alt="image" src="https://github.com/user-attachments/assets/014896eb-2da7-424e-8735-4e8d78b41f3a" />

---

<img width="956" height="854" alt="image" src="https://github.com/user-attachments/assets/b3021c1b-ad24-4ddc-996f-7473175cf56b" />

---

<img width="939" height="829" alt="image" src="https://github.com/user-attachments/assets/2ac79f2f-2b13-4deb-84ca-92a547a85936" />

---

<img width="939" height="847" alt="image" src="https://github.com/user-attachments/assets/a8917d56-67f2-4431-a577-0baa1981f073" />

---

<img width="946" height="435" alt="image" src="https://github.com/user-attachments/assets/a72e6874-b350-4c02-a857-f27bcc5621d3" />


```
https://website.thm/item/2?server=api
#to
https://website.thm/item/2?server=server.website.thm/flag?id=9&x=api
```

```
THM{SSRF_MASTER}
```




  
</details>




<details>
  <summary>Finding an SSRF</summary>



<img width="1652" height="848" alt="image" src="https://github.com/user-attachments/assets/ffa9c923-90c8-419a-a4a4-d5f3f75c9597" />



<img width="1664" height="536" alt="image" src="https://github.com/user-attachments/assets/79724249-c1a3-45cc-8684-3450fc8892e0" />



  
</details>




<details>
  <summary>Defeating Common SSRF Defenses</summary>


More security-savvy developers aware of the risks of SSRF vulnerabilities may implement checks in their applications to make sure the requested resource meets specific rules. There are usually two approaches to this, either a deny list or an allow list.

**\
**

**Deny List**

A Deny List is where all requests are accepted apart from resources specified in a list or matching a particular pattern. A Web Application may employ a deny list to protect sensitive endpoints, IP addresses or domains from being accessed by the public while still allowing access to other locations. A specific endpoint to restrict access is the localhost, which may contain server performance data or further sensitive information, so domain names such as localhost and 127.0.0.1 would appear on a deny list. Attackers can bypass a Deny List by using alternative localhost references such as 0, 0.0.0.0, 0000, 127.1, 127.*.*.*, 2130706433, 017700000001 or subdomains that have a DNS record which resolves to the IP Address 127.0.0.1 such as 127.0.0.1.nip.io.

Also, in a cloud environment, it would be beneficial to block access to the IP address 169.254.169.254, which contains metadata for the deployed cloud server, including possibly sensitive information. An attacker can bypass this by registering a subdomain on their own domain with a DNS record that points to the IP Address 169.254.169.254.

**Allow List**

An allow list is where all requests get denied unless they appear on a list or match a particular pattern, such as a rule that an URL used in a parameter must begin with **https://website.thm.** An attacker could quickly circumvent this rule by creating a subdomain on an attacker's domain name, such as https://website.thm.attackers-domain.thm. The application logic would now allow this input and let an attacker control the internal HTTP request.

**Open Redirect**

If the above bypasses do not work, there is one more trick up the attacker's sleeve, the open redirect. An open redirect is an endpoint on the server where the website visitor gets automatically redirected to another website address. Take, for example, the link https://website.thm/link?url=https://tryhackme.com. This endpoint was created to record the number of times visitors have clicked on this link for advertising/marketing purposes. But imagine there was a potential SSRF vulnerability with stringent rules which only allowed URLs beginning with https://website.thm/. An attacker could utilise the above feature to redirect the internal HTTP request to a domain of the attacker's choice.


<img width="1651" height="592" alt="image" src="https://github.com/user-attachments/assets/571c0c64-4b4e-48d8-898c-9cefcd9d2e5d" />









  
</details>






<details>
  <summary>SSRF Practical</summary>


## visit this endpoint to change account photo

```
https://10-67-138-41.reverse-proxy.cell-prod-us-east-1d.vm.tryhackme.com/customers/new-account-page
```


<img width="1556" height="469" alt="image" src="https://github.com/user-attachments/assets/c9e1254e-59e8-48c2-9533-8a5d6225dd44" />

```
/assets/avatars/3.png
```

## after set photo :

<img width="1650" height="734" alt="image" src="https://github.com/user-attachments/assets/811424a7-23e0-4b41-844c-f6c5e8ca791c" />


This is called **Data URI scheme**

- Server:

    1. Scroll the image

    2. Converted to Base64

    3. Return it to the browser

Meaning:

> Anything the server can read → it will return to you Base64 😈


## now change value in input :

```
<input type="radio" name="avatar" value="x/../private">
```


<img width="1630" height="299" alt="image" src="https://github.com/user-attachments/assets/456cc308-d6e8-4b78-8837-f07c0886f702" />

## it return the flag 

<img width="1578" height="406" alt="image" src="https://github.com/user-attachments/assets/84428a0c-f74a-4d50-9f14-563dbc317380" />



<img width="1014" height="570" alt="image" src="https://github.com/user-attachments/assets/798cea5c-b808-4d95-a064-de469bcbdf32" />


```
THM{YOU_WORKED_OUT_THE_SSRF}
```














  
</details>































