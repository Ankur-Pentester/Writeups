---
description: Only For Zonia !!
---

# MySQL

## Installing MySQL

***

## What is MySQL workbench?

MySQL Workbench is a visual tool for database architects, developers, and DBAs. It\
provides data modeling, SQL development, and comprehensive administration\
tools for server configuration, user administration, backup, and much more.



## What is a Database Management System (DBMS)?

A Database Management System (DBMS) is software that interacts with end users,\
applications, and the database itself to capture and analyze data. It allows for the\
creation, retrieval, updating, and management of data in databases. If you know\
one DBMS, you can easily transition to another, as they share similar concepts and\
functionalities.



## Windows / macOS Installation

1. Download from: [https://dev.mysql.com/downloads/installer/](https://dev.mysql.com/downloads/installer/)
2. Run the installer and choose Developer Default.
3. Set a root password when prompted.
4. Install MySQL Workbench (optional but helpful GUI).



## Linux (Ubuntu) Installation

Follow these steps to install MySQL and create a user:



Step 1: Update Package Index

```
sudo apt update
```

Step 2: Install MySQL Server

```
sudo apt install mysql-server
```

Step 3: Secure the Installation

```
sudo mysql_secure_installation
```

Choose your options (yes to most).

Step 4: Create a User `'hacker'@'localhost'`\
Log into MySQL:

```sql
sudo mysql
```

Run the following SQL commands:

```sql
CREATE USER 'hacker'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'harry'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;
```

Step 5: Test Login

```sql
mysql -u hacker -p
```

Enter the password: `password`\
Make sure to replace 'password' with a secure password of your choice in\
production environments

***

## Getting Started with MySQL

## What is a Database?

A database is a container that stores related data in an organized way. In MySQL, a\
database holds one or more tables.

Think of it like:

* Folder analogy:

&#x20;      •  A database is like a folder.\
&#x20;      •  Each table is a file inside that folder.\
&#x20;      •  The rows in the table are like the content inside each file.<br>

* Excel analogy:

&#x20;      •  A database is like an Excel workbook.\
&#x20;      •  Each table is a separate sheet inside that workbook.\
&#x20;      •  Each row in the table is like a row in Excel<br>

***

Step 1: Create a Database

```sql
CREATE DATABASE startersql;
```

After creating the database, either:

&#x20;  •  Right-click it in MySQL Workbench and select “Set as Default Schema”, or\
&#x20;  •  Use this SQL command:

```sql
USE startersql;
```



Step 2: Create a Table

Now we’ll create a simple `users`
