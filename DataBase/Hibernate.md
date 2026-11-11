# Hibernate Notes

## Week 1

- ORM Basics
- Hibernate Architecture
- SessionFactory
- Session
- Transaction
- CRUD

## Week 2

- Entity Mapping
- Entity Lifecycle States
- `get()` vs `load()`
- `FetchType.LAZY` vs `FetchType.EAGER`
- Relationships
- Fetch Types
- Cascading

## Week 3

- HQL
- Criteria API
- Transactions
- Locking

## Week 4

- Caching
- Performance Tuning
- N+1 Problem
- Batch Processing
- Spring Data JPA Integration

---

# Must-Know for Senior Java Interviews

- Entity Lifecycle
- `FetchType.LAZY` vs `EAGER`
- Cascade Types
- N+1 Query Problem
- First & Second Level Cache
- Dirty Checking
- Optimistic Locking, Pessimistic Locking
- `flush()`, `commit()`
- `merge()` vs `update()`
- `get()` vs `load()`
- Hibernate Performance Tuning

---

# ORM Basics

Let’s first understand ORM basics using a real-life example.

## The Problem Hibernate Solves

Suppose you have a database table:

```sql
EMPLOYEE
--------------------------------
ID    NAME       SALARY
--------------------------------
1     Nandini    50000
2     Ravi       60000
```

Now in Java, you want employee data.

### Without Hibernate (using JDBC)

```java
Connection con = DriverManager.getConnection(...);
PreparedStatement ps = con.prepareStatement(
    "SELECT * FROM EMPLOYEE WHERE ID=?"
);
ps.setInt(1, 1);

ResultSet rs = ps.executeQuery();
while (rs.next()) {
    System.out.println(rs.getString("NAME"));
}
```

This is a lot of code just to get one employee.

## How We Think as Java Developers

As Java developers, we like objects:

```java
Employee emp = new Employee();
emp.setId(1L);
emp.setName("Nandini");
emp.setSalary(50000.0);
```

We think in terms of:

- Classes
- Objects
- Methods

Not in terms of:

- Tables
- Rows
- Columns

There is a mismatch between Java and the database.

### Database

```sql
EMPLOYEE
ID, NAME, SALARY
```

### Java

```java
public class Employee {
    private Long id;
    private String name;
    private Double salary;
}
```

The key question is:

- How does a Java object become a database row?
- How does a database row become a Java object?

That is exactly where ORM comes in.

---

# What is ORM?

**ORM = Object Relational Mapping**

Let’s break this term down:

- **Object** = Java Object
- **Relational** = Relational Database
- **Mapping** = Connecting both

Example:

```text
Java Object   <----->   Database Table
Employee      <----->   EMPLOYEE
id            <----->   ID
name          <----->   NAME
salary        <----->   SALARY
```

ORM automatically converts:

- `Employee` to `EMPLOYEE`
- `EMPLOYEE` to `Employee`

This mapping is called **ORM**.

---

# What is Hibernate?

**Hibernate** is a framework that performs ORM.

Instead of writing SQL like this:

```sql
SELECT * FROM EMPLOYEE WHERE ID = 1;
```

You write:

```java
Employee emp = session.get(Employee.class, 1L);
```

Hibernate internally generates the SQL and talks to the database.

## Real-Life Example: Translator

```text
Java Developer
      |
      v
   Hibernate
      |
      v
   Database
```

You work with Java objects.

Hibernate works with SQL.

---

# Why Companies Use Hibernate

Without Hibernate:

- Lots of JDBC code
- Manual `ResultSet` handling
- Manual SQL creation
- Manual object conversion

With Hibernate: one line

```java
Employee emp = session.get(Employee.class, 1L);
```

Hibernate handles:

- SQL generation
- Object creation
- Data mapping
- Transactions
- Caching

Hibernate is an ORM framework for Java that maps Java objects to relational database tables and eliminates most of the JDBC boilerplate code.

---

# Why Can’t Java Objects Directly Talk to Database Tables?

Because Java objects and database tables are completely different things.

- A Java object lives in memory (RAM)
- A database table lives in a database system
- They do not understand each other’s structure directly

The database has no idea what an `Employee` class is.

Similarly, Java has no idea what an `EMPLOYEE` table is.

## Without Hibernate

You must manually translate between the two worlds.

### Convert Object → SQL

```java
Employee emp = new Employee();
String sql = "INSERT INTO EMPLOYEE(ID, NAME, SALARY) VALUES(?,?,?)";
```

You manually take data from the object and put it into SQL.

### Convert SQL Result → Object

```java
ResultSet rs = ps.executeQuery();

Employee emp = new Employee();
emp.setId(rs.getLong("ID"));
emp.setName(rs.getString("NAME"));
emp.setSalary(rs.getDouble("SALARY"));
```

Again, this is manual conversion.

---

# What Hibernate Does

Hibernate performs this translation automatically.

You give:

```java
Employee emp = new Employee();
emp.setName("Nandini");
```

Hibernate generates:

```sql
INSERT INTO EMPLOYEE(NAME) VALUES('Nandini');
```

Similarly:

```java
Employee emp = session.get(Employee.class, 1L);
```

Hibernate runs:

```sql
SELECT * FROM EMPLOYEE WHERE ID = 1;
```

and creates the `Employee` object for you.

---

# Quick Question

If you have:

```java
Employee emp = new Employee();
emp.setName("Nandini");
```

What must happen before this data can be stored in the database?

**Answer:** The Java object must be converted into an SQL `INSERT` statement (or equivalent database operation). That is exactly the job of ORM/Hibernate.

---

# `@Entity`

## What is an Entity?

An **entity** is simply a Java class that Hibernate maps to a database table.

Suppose you already have this table:

```sql
EMPLOYEE
------------------------
ID
NAME
SALARY
```

To represent this table in Java, you create a class:

```java
public class Employee {
    private Long id;
    private String name;
    private Double salary;
}
```

At this point, Hibernate does not know this class is linked to a database table.

So we tell Hibernate:

```java
@Entity
public class Employee {
    private Long id;
    private String name;
    private Double salary;
}
```

`@Entity` tells Hibernate:

> “This class should be managed by Hibernate and mapped to a database table.”

## What Does `@Entity` Really Do?

### Without `@Entity`

```java
public class Employee {
}
```

Hibernate sees it as a normal Java class.

### With `@Entity`

```java
@Entity
public class Employee {
}
```

Hibernate says:

> “Okay, I will treat this class as a database entity.”

---

# `@Table`

Suppose your class name is:

```java
Employee
```

and your table name is also:

```sql
EMPLOYEE
```

Hibernate can usually figure this out automatically.

So this works:

```java
@Entity
public class Employee {
}
```

But what if the table name is:

```sql
EMPLOYEE_MASTER
```

Then you explicitly tell Hibernate:

```java
@Entity
@Table(name = "EMPLOYEE_MASTER")
public class Employee {
}
```

This means:

```text
Employee class  ->  EMPLOYEE_MASTER table
```

## What Happens If We Don’t Use `@Table`?

Hibernate assumes the table name is based on the class name.

Example:

```java
@Entity
public class Employee {
}
```

Hibernate looks for:

```sql
EMPLOYEE
```

(or a naming-strategy equivalent)

If your actual table is `EMPLOYEE_MASTER`, Hibernate cannot find the expected table and you get an error.

---

# `@Id`

Every table should have a primary key.

Example:

```sql
EMPLOYEE
-----------------------
ID  NAME
-----------------------
1   Nandini
2   Ravi
```

Here, `ID` is the primary key.

In Java:

```java
@Entity
@Table(name = "EMPLOYEE")
public class Employee {
    @Id
    private Long id;

    private String name;
}
```

`@Id` tells Hibernate:

> “This field uniquely identifies each row.”

## Why Is `@Id` Mandatory?

Imagine:

```sql
ID  NAME
------------
1   Nandini
2   Nandini
```

If Hibernate wants to load a specific record:

```java
session.get(Employee.class, ?)
```

How does it identify the row?

Using the **primary key**.

That is why every entity must have an `@Id`.

---

# Complete Example

```java
@Entity
@Table(name = "EMPLOYEE")
public class Employee {
    @Id
    private Long id;

    private String name;
    private Double salary;
}
```

### Mapping

```text
Java Class   ->   Database
Employee     ->   EMPLOYEE
id           ->   ID
name         ->   NAME
salary       ->   SALARY
```

---

# If Field Name and Column Name Differ

Suppose the database has a column named `EMPLOYEE_NAME`, but the Java field is:

```java
private String name;
```

Then use `@Column`:

```java
@Entity
@Table(name = "EMPLOYEE")
public class Employee {
    @Id
    private Long id;

    @Column(name = "EMPLOYEE_NAME")
    private String name;
}
```

### Mapping

```text
name field   ->   EMPLOYEE_NAME column
```

---

# Real-World Entity

```java
@Entity
@Table(name = "EMPLOYEE")
public class Employee {
    @Id
    private Long id;

    @Column(name = "EMPLOYEE_NAME")
    private String name;

    @Column(name = "SALARY")
    private Double salary;

    // getters and setters
}
```

This is the kind of code you will see in almost every Spring Boot/Hibernate application.

---

# Annotation Summary

## `@Entity`

Marks a class as a Hibernate entity.

## `@Table`

```java
@Table(name = "EMPLOYEE")
```

Maps an entity to a specific database table.

## `@Id`

```java
@Id
private Long id;
```

Marks the primary key field.

---

# Interview Questions

## What is an Entity?

An entity is a Java class mapped to a database table and managed by Hibernate.

## Why do we use `@Entity`?

To tell Hibernate that the class should be mapped to a database table.

## Why do we use `@Table`?

To specify the exact database table name for the entity.

## Why do we use `@Id`?

To identify the primary key field that uniquely identifies each row in the table.

---

# Primary Key Generation

## Why Do We Need Primary Key Generation?

Suppose we have:

```sql
EMPLOYEE
-----------------------
ID  NAME
-----------------------
1   Nandini
2   Ravi
```

Without automatic generation, you must manually set IDs:

```java
Employee emp = new Employee();
emp.setId(3L);
emp.setName("John");
session.save(emp);
```

### Problem

What if another record already has ID `3`?

This leads to a **primary key violation**.

To avoid this, Hibernate can generate IDs automatically.

---

# `@GeneratedValue`

```java
@Entity
@Table(name = "EMPLOYEE")
public class Employee {
    @Id
    @GeneratedValue
    private Long id;

    private String name;
}
```

Now:

```java
Employee emp = new Employee();
emp.setName("Nandini");
session.save(emp);
```

Hibernate will generate the ID automatically.

---

# Generation Strategies

There are four main strategies you will encounter:

- `AUTO`
- `IDENTITY`
- `SEQUENCE`
- `TABLE`

The most important ones are:

- `AUTO`
- `IDENTITY`
- `SEQUENCE`

---

## 1. `AUTO`

```java
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;
```

### Meaning

Hibernate decides which strategy to use based on the database.

Example:

- MySQL → may use `IDENTITY`
- Oracle → may use `SEQUENCE`

You do not control it directly.

---

## 2. `IDENTITY`

Most common with MySQL.

### Database table

```sql
CREATE TABLE EMPLOYEE (
    ID BIGINT AUTO_INCREMENT,
    NAME VARCHAR(100)
);
```

### Entity

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

### Insert example

```java
Employee emp = new Employee();
emp.setName("Nandini");
session.save(emp);
```

Hibernate executes:

```sql
INSERT INTO EMPLOYEE(NAME)
VALUES('Nandini');
```

The database generates:

```text
ID = 1
```

Hibernate reads it back and populates the object.

### Think of `IDENTITY` as:

> “Hibernate: Database, please generate the ID.”
>
> “Database: Sure, here is `ID = 1`.”

---

## 3. `SEQUENCE`

Very common in Oracle and PostgreSQL.

### Database sequence

```sql
CREATE SEQUENCE EMP_SEQ
START WITH 1
INCREMENT BY 1;
```

### Entity

```java
@Id
@GeneratedValue(
    strategy = GenerationType.SEQUENCE,
    generator = "emp_seq_generator"
)
@SequenceGenerator(
    name = "emp_seq_generator",
    sequenceName = "EMP_SEQ",
    allocationSize = 1
)
private Long id;
```

### Meaning

The database sequence provides the next value automatically.

This is often preferred in Oracle because it is efficient and scalable.

---

# Final Notes

Hibernate is mainly used to:

- reduce boilerplate JDBC code
- map Java objects to database tables
- manage persistence and transactions
- support caching and optimization

For Java interviews, you should understand:

- entity creation
- `@Id` and `@GeneratedValue`
- transaction lifecycle
- fetch strategies
- lazy vs eager loading
- caching
- N+1 problem
- dirty checking
- locking

This file is intended to help you revise Hibernate concepts quickly before interviews and coding tasks.

---

## Quick Revision Checklist

- **ORM** = Object Relational Mapping
- **Hibernate** = Java ORM framework
- **Entity** = Java class mapped to a database table
- **`@Entity`** = marks entity class
- **`@Table`** = map to a specific table
- **`@Id`** = primary key
- **`@GeneratedValue`** = automatic ID generation
- **`AUTO` / `IDENTITY` / `SEQUENCE`** = strategies
- **Session** = main interface for persistence
- **Transaction** = database unit of work
- **Caching** = improves performance
- **N+1** = common performance issue

