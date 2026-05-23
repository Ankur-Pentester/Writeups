# vault-door-training

<figure><img src="../../../.gitbook/assets/image (744).png" alt=""><figcaption></figcaption></figure>

***

This challenge provides a Java source file (`VaultDoorTraining.java`) that simulates a vault door asking for a password. The goal is to reverse engineer the program and determine the correct password to gain access.

<figure><img src="../../../.gitbook/assets/image (745).png" alt=""><figcaption></figcaption></figure>

### Step 1 – Inspect the Source Code

While analyzing the source code, we see that the program asks the user to enter a vault password.

```
System.out.print("Enter vault password: ");
String userInput = scanner.next();
```

### Step 2 – Understanding Input Processing

The program removes the prefix `picoCTF{` and the closing `}` from the user input before checking the password.

```
String input = userInput.substring("picoCTF{".length(), userInput.length()-1);
```

This means the program expects the input in the following format:

```
picoCTF{password}
```

Only the text inside the braces is used for verification.

### Step 3 – Locate the Password Check

Inside the `checkPassword()` function, the password is directly stored in the source code.

```
public boolean checkPassword(String password) {
    return password.equals("w4rm1ng_Up_w1tH_jAv4_0009yrGMeEp");
}
```

This means the correct password is:

```
w4rm1ng_Up_w1tH_jAv4_0009yrGMeEp
```

***

### Step 4 – Construct the Flag

Since the program expects the `picoCTF{}` format, we wrap the password inside it.

```
picoCTF{w4rm1ng_Up_w1tH_jAv4_0009yrGMeEp}
```

***

### Step 5 – Verification

Run the program and enter the flag:

```
java hack.java
```

Input:

```
picoCTF{w4rm1ng_Up_w1tH_jAv4_0009yrGMeEp}
```

Output:

```
Access granted.
```

<figure><img src="../../../.gitbook/assets/image (746).png" alt=""><figcaption></figcaption></figure>

#### Final Flag

```
picoCTF{w4rm1ng_Up_w1tH_jAv4_0009yrGMeEp}
```

