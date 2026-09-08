# Lab: SQL injection UNION attack, determining the number of columns returned by the query

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. The first step of such an attack is to determine the number of columns that are being returned by the query. You will then use this technique in subsequent labs to construct the full attack.

To solve the lab, determine the number of columns returned by the query by performing a SQL injection UNION attack that returns an additional row containing null values.

***

Let's Start !!

First We Visit The Website !!

<figure><img src="../../../.gitbook/assets/image (688).png" alt=""><figcaption></figcaption></figure>

We Try To Inject Single Quote `'` To Break The Backend Query !!

<figure><img src="../../../.gitbook/assets/image (689).png" alt=""><figcaption></figcaption></figure>

When We Inject `'--` They Fix The Query and Give No Error ,In SQL `--` Refer To Comment Out In Code !!

<figure><img src="../../../.gitbook/assets/image (690).png" alt=""><figcaption></figcaption></figure>

Modify the `category` parameter to add an additional column containing a null value:

If They Give Error It's Mean There Is No Two Columns !!

```
' UNION SELECT NULL,NULL--
```

<figure><img src="../../../.gitbook/assets/image (691).png" alt=""><figcaption></figcaption></figure>

Now We Use This Query And Now  It's Confirm They Have Only Three  Columns !!

```
' UNION SELECT NULL,NULL,NULL--
```

<figure><img src="../../../.gitbook/assets/image (692).png" alt=""><figcaption></figcaption></figure>
