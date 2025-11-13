# **`NoSQL Injection`**


<details>
  <summary>What is NoSQL</summary>


## 1) Quick summary


- MongoDB stores data in the form of **documents** similar to JSON (key-value pairs).

- A group of similar documents collected in **collection** (like a table in an RDBMS).

- The collections group remains in **database**.

Queries in MongoDB are **objects/arrays** (JSON-like filters) instead of SQL statements.


![image](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679544440.png)


---


## 2) Basic concepts (in simple language)


- **Document**: One record --- Example:\
   ```js
   {"_id" : ObjectId("5f077332de2cdf808d26cd74"), "username" : "lphillips", "first_name" : "Logan", "last_name" : "Phillips", "age" : "65", "email" : "lphillips@example.com" }
   ```
- **Collection**: A collection of similar documents --- such as the `people` table.

- **Database**: Collection of collections.

- **Filter / Query**: An object that specifies conditions for retrieving documents --- like WHERE in SQL but in JSON form.

- **Operators**: Characters starting with `$` help construct complex conditions such as `$lt`, `$gt`, `$ne`, `$regex`, `$in`, `$or`, `$and`.


![image](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679596244.png)


---


## 3) Practical examples --- Simple filter → Expected results

Suppose you have a collection called `people` that contains different documents.

- A query that returns documents for which `last_name` = "Sandler":

    - Mongo shell:

    ```js
    db.people.find({ last_name: "Sandler" })
    ```

    - PHP (associative array style):

    ```js
    ['last_name' => 'Sandler']
    ```

- Query filters gender="male" and `last_name`="Phillips":

    ```js
    db.people.find({ gender: "male", last_name: "Phillips" })
    ```

- >  It will return the document in which both conditions are true (implicit AND).


- Inquiry for people under 50:

    ```js
    db.people.find({ age: { $lt: 50 } })
    ```

- > Note here that age can be a string or a number — the type of value is important! If stored as string the comparison behaves differently.


---



## 4) Common Operators with simplified explanation

- `$lt`, `$lte` --- less than/less or equal to.\
    `{ age: { $lt: 50 } }`

- `$gt`, `$gte` --- greater than/greater than or equal to.

- `$ne` --- not equal.\
    `{ username: { $ne: null } }`

- `$regex` --- Match with regular expression.\
    `{ email: { $regex: ".*@example.com$" } }`

- `$in` --- within a set of values.\
    `{ role: { $in: ["admin","moderator"] } }`

- `$or`, `$and` --- Complex logical conditions:


```js
{ $or: [ { age: { $lt: 20 } }, { role: "intern" } ] }
```

- `$where` --- Allows JavaScript to run within the query (high security risk, often disabled or should be disabled).


---


## 5) Important point---Data types matter very much


- If you store `age` as a string `30` instead of the number `30`, numerical comparison operations will not work as you expect.

- Also, if the server receives an **object** from the user and inserts it into the query without checking, the attacker can inject Operators (`{ username: { "$ne": null } }`) and change the query logic --- this is a NoSQLi gateway.



![image](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679617512.png)


<img width="1809" height="517" alt="image" src="https://github.com/user-attachments/assets/1154494f-7e0a-42a6-8487-90f573564a36" />




  
</details>



















