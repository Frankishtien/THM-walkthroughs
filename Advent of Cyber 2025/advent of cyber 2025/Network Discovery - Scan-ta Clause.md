# Network Discovery - Scan-ta Clause



```ruby
nmap 10.82.148.116
```


<img width="918" height="430" alt="image" src="https://github.com/user-attachments/assets/dc4b617b-0653-4611-8dff-d7472b065a7b" />


<img width="1732" height="683" alt="image" src="https://github.com/user-attachments/assets/72d00c02-0ec6-4cb4-94da-9c684e8ad7eb" />


```ruby
nmap -p- --script=banner 10.82.148.116
```

<img width="1219" height="622" alt="image" src="https://github.com/user-attachments/assets/d5146de0-6068-49fe-b407-cdb3733ef620" />




```ruby
ftp 10.82.148.116 21212
```


<img width="1351" height="655" alt="image" src="https://github.com/user-attachments/assets/104a73fa-ac41-433c-9b38-3a8931bc7372" />


> ## so first key is **`3aster_`**



```
nc -v 10.82.148.116 25251
```

<img width="982" height="457" alt="image" src="https://github.com/user-attachments/assets/9404e6cc-aa6d-4059-a066-a40bba6063af" />


> ## SECOND KEY IS **`15_th3_`**



### UDP Scan

```ruby
nmap -sU 10.82.148.116
```

<img width="1192" height="446" alt="image" src="https://github.com/user-attachments/assets/b153741b-ad53-4155-bc98-0f9b92539e4f" />


> ## found open port 53 associated with DNS 

```ruby
dig @10.82.148.116 TXT key3.tbfc.local +short
```

<img width="895" height="95" alt="image" src="https://github.com/user-attachments/assets/7be7020a-4da0-4574-b826-4ac3cf532ee6" />

> ## and third key is **`n3w_xm45`**

> # full key is 

```ruby
3aster_15_th3_n3w_xm45
```

<img width="1704" height="571" alt="image" src="https://github.com/user-attachments/assets/81cbb5c0-4e9f-4b41-9de2-67b922535e4c" />


<img width="940" height="309" alt="image" src="https://github.com/user-attachments/assets/3b5054d3-7e9b-4add-adfd-25cb58bac56c" />

> ## now let's see all ports that work on server

```ruby
ss -tunlp
```

<img width="1371" height="562" alt="image" src="https://github.com/user-attachments/assets/74f1dc0a-f806-4a2c-920b-2d34bdbacf52" />


> ## connect to database that work by default on port `3306`

```ruby
mysql -D tbfcqa01 -e "show tables;"   
```

<img width="581" height="138" alt="image" src="https://github.com/user-attachments/assets/a4614805-86cc-40f7-937d-66cb4162a2ad" />


> ## see what in flags table

```
mysql -D tbfcqa01 -e "select * from flags;"    
```






<img width="640" height="131" alt="image" src="https://github.com/user-attachments/assets/507a2b63-27f5-4470-bf20-cc54f50e743e" />







