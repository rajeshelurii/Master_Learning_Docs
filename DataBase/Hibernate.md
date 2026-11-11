Week 1

ORM Basics
Hibernate Architecture
SessionFactory
Session
Transaction
CRUD

Week 2

Entity Mapping
Entity Lifecycle States 
get() vs load() 
FetchType.LAZY vs FetchType.EAGER
Relationships
Fetch Types
Cascading

Week 3

HQL
Criteria API
Transactions
Locking

Week 4

Caching
Performance Tuning
N+1 Problem
Batch Processing
Spring Data JPA Integration



Must-Know for Senior Java Interviews

Entity Lifecycle
FetchType.LAZY vs EAGER
Cascade Types
N+1 Query Problem
First & Second Level Cache
Dirty Checking
Optimistic Locking, Pessimistic Locking
flush(), commit()
merge() vs update()
get() vs load()
Hibernate Performance Tuning



ORM Basics:
Let's first understand ORM Basics using a real-life example.

The Problem Hibernate Solves

Suppose you have a database table:

EMPLOYEE

--------------------------------

ID    NAME       SALARY

--------------------------------

1     Nandini    50000

2     Ravi       60000



Now in Java, you want employee data.

Without Hibernate (using JDBC), you write:



Connection con = DriverManager.getConnection(...);

PreparedStatement ps =

    con.prepareStatement(

        "SELECT * FROM EMPLOYEE WHERE ID=?");

ps.setInt(1, 1);

ResultSet rs = ps.executeQuery();

while(rs.next()) {

    System.out.println(rs.getString("NAME"));

}



That's a lot of code just to get one employee.



How We Think as Java Developers

As Java developers, we like objects:



Employee emp = new Employee();

emp.setId(1);

emp.setName("Nandini");

emp.setSalary(50000);



We think in terms of:

Classes, Objects and Methods

Not: Tables, Rows and Columns



There is a mismatch between Java and Database.



Database: EMPLOYEE

Contains ID, NAME and SALARY

Java:

public class Employee {

    private Long id;

    private String name;

    private Double salary;

}



How does Java object become a DB row?

How does DB row become a Java object?

That's exactly where ORM comes in.



What is ORM?

ORM = Object Relational Mapping

Let's break that phrase.



Object: Java Object

Employee emp = new Employee();



Relational: Relational Database

EMPLOYEE TABLE



Mapping :Connecting both.

Java Object  <-----> Database Table



ORM automatically converts: Employee to EMPLOYEE and viceversa.



Visualize ORM

JAVA SIDE                    DATABASE SIDE

Employee Object         <-->     EMPLOYEE Table

id                               ID

name                             NAME

salary                           SALARY

This mapping is called ORM.



What is Hibernate?

Hibernate is a framework that performs ORM.



Instead of writing SQL:

SELECT * FROM EMPLOYEE WHERE ID = 1;
you write:

Employee emp =   session.get(Employee.class, 1L);


Hibernate internally generates SQL and talks to the database.

Real-Life Example: Imagine a translator.

You speak:

Employee

Database understands:

EMPLOYEE

Hibernate acts as translator.



Java Developer

       |

       v

   Hibernate

       |

       v

   Database



You work with Java objects.

Hibernate works with SQL.



Why Companies Use Hibernate

Without Hibernate:

Lots of JDBC code
Manual ResultSet handling
Manual SQL creation
Manual object conversion

With Hibernate: One line

Employee emp =  session.get(Employee.class, 1L);



Hibernate handles:

SQL generation
Object creation
Data mapping
Transactions
Caching



Hibernate is an ORM framework for Java that maps Java objects to relational database tables and eliminates most of the JDBC boilerplate code.



Why can't Java objects directly talk to database tables??



Because Java objects and database tables are completely different things.

A Java object lives in memory (RAM), while a database table lives in a database system. They don't understand each other's structure directly.



that the database has no idea what an Employee class is.

Similarly, Java has no idea what an EMPLOYEE table is.



Without Hibernate

You must manually translate between the two worlds.



Convert Object → SQL

Employee emp = new Employee();

String sql =

"INSERT INTO EMPLOYEE(ID,NAME,SALARY) VALUES(?,?,?)";



You manually take data from the object and put it into SQL.



Convert SQL Result → Object

ResultSet rs = ps.executeQuery();

 

Employee emp = new Employee();

emp.setId(rs.getLong("ID"));

emp.setName(rs.getString("NAME"));

emp.setSalary(rs.getDouble("SALARY"));



Again, manual conversion.



What Hibernate Does

Hibernate performs this translation automatically.



You give:

Employee emp = new Employee();

emp.setName("Nandini");



Hibernate generates:

INSERT INTO EMPLOYEE(NAME) VALUES('Nandini');



Similarly,

Employee emp = session.get(Employee.class, 1L);



Hibernate runs:

SELECT * FROM EMPLOYEE WHERE ID = 1;

and creates the Employee object for you.



Why can't Java objects directly talk to database tables?

Java objects and database tables have different representations. Java works with classes and objects in memory, whereas databases work with tables, rows, and SQL. Therefore, a mapping layer such as Hibernate is required to convert objects into database records and database records back into objects.



Can you answer this now?

If I have:

Employee emp = new Employee();

emp.setName("Nandini");

 

What must happen before this data can be stored in the database?

Answer: The Java object must be converted into an SQL INSERT statement (or equivalent database operation). That's exactly the job of ORM/Hibernate. 



@Entity

What is an Entity?

An Entity is simply a Java class that Hibernate maps to a database table.



Suppose you already have this table:

EMPLOYEE

------------------------

ID

NAME

SALARY



To represent this table in Java, we create a class:

public class Employee {



private Long id;

private String name;

private Double salary;

}



At this point, Hibernate doesn't know this class is linked to a database table.

So we tell Hibernate:



@Entity

public class Employee {



private Long id;

private String name;

private Double salary;

}



@Entity tells Hibernate:

"This class should be managed by Hibernate and mapped to a database table."



What Does @Entity Actually Do?

Without @Entity:



public class Employee {

}

Hibernate sees it as a normal Java class.



With @Entity:



@Entity

public class Employee {

}

Hibernate says:

"Okay, I will treat this class as a database entity."



What is @Table?

Suppose your class name is:

Employee

and your table name is also:

EMPLOYEE

Hibernate can usually figure this out automatically.



So this works:

@Entity

public class Employee {

}



But what if the table name is: EMPLOYEE_MASTER

Then you explicitly tell Hibernate:

@Entity

@Table(name = "EMPLOYEE_MASTER")

public class Employee {

}



Meaning:

Employee class

|

V

EMPLOYEE_MASTER table



What Happens If We Don't Use @Table?

Hibernate assumes the table name is based on the class name.



Example:

@Entity

public class Employee {

}



Hibernate looks for:

EMPLOYEE

(or a naming-strategy equivalent)

If your actual table is EMPLOYEE_MASTER

You'll get an error because Hibernate cannot find the table it expects.



What is @Id?

Every table should have a primary key.

Example:

EMPLOYEE

-----------------------

ID NAME

-----------------------

1 Nandini

2 Ravi

Here, ID is the Primary Key.

In Java:

@Entity

@Table(name="EMPLOYEE")

public class Employee {



@Id

private Long id;



private String name;

}

@Id tells Hibernate:

"This field uniquely identifies each row."



Why Is @Id Mandatory?

Imagine:

ID NAME

------------

1 Nandini

2 Nandini

If Hibernate wants to load a specific record: session.get(Employee.class, ?)

How can it identify the row?

Using the Primary Key.

That's why every Entity must have an @Id.



Complete Example

@Entity

@Table(name="EMPLOYEE")

public class Employee {



@Id

private Long id;

private String name;

private Double salary;

}

Mapping:

Java Class Database

Employee ---> EMPLOYEE

id ---> ID

name ---> NAME

salary ---> SALARY



What If Field Name and Column Name Differ?

Suppose database has: EMPLOYEE_NAME

but Java field is: private String name;

Use @Column.



@Entity

@Table(name="EMPLOYEE")

public class Employee {



@Id

private Long id;



@Column(name="EMPLOYEE_NAME")

private String name;

}

Mapping:

name field

|

V

EMPLOYEE_NAME column



Real-World Entity

@Entity

@Table(name="EMPLOYEE")

public class Employee {



@Id

private Long id;



@Column(name="EMPLOYEE_NAME")

private String name;



@Column(name="SALARY")

private Double salary;



// getters and setters

}

This is the kind of code you'll see in almost every Spring Boot/Hibernate application.



@Entity

Marks a class as a Hibernate entity.



@Table

@Table(name="EMPLOYEE")

Maps entity to a specific database table.



@Id

@Id

private Long id;

Marks the primary key field.



Interview Questions

What is an Entity?

An Entity is a Java class mapped to a database table and managed by Hibernate.



Why do we use @Entity?

To tell Hibernate that the class should be mapped to a database table.



Why do we use @Table?

To specify the exact database table name for the entity.



Why do we use @Id?

To identify the primary key field that uniquely identifies each row in the table.



Primary Key Generation:

Why Do We Need Primary Key Generation?
Suppose we have:

EMPLOYEE

-----------------------

ID NAME

-----------------------

1 Nandini

2 Ravi



Without automatic generation, you must manually set IDs:

Employee emp = new Employee();

emp.setId(3L);

emp.setName("John");

session.save(emp);



Problem:

What if another record already has ID 3?

Primary Key Violation

To avoid this, Hibernate can generate IDs automatically.



@GeneratedValue

@Entity

@Table(name = "EMPLOYEE")

public class Employee {



@Id

@GeneratedValue

private Long id;

private String name;

}

Now:

Employee emp = new Employee();

emp.setName("Nandini");

session.save(emp);



Hibernate will generate the ID automatically.



Generation Strategies

There are 4 major strategies you'll encounter:

AUTO

IDENTITY

SEQUENCE

TABLE

Focus heavily on:

AUTO
IDENTITY
SEQUENCE



1. AUTO

@Id

@GeneratedValue(strategy = GenerationType.AUTO)

private Long id;



Meaning:

Hibernate decides which strategy to use based on the database.

Example:

MySQL → may use IDENTITY
Oracle → may use SEQUENCE

You don't control it.



2. IDENTITY

Most common with MySQL.

Database table:

CREATE TABLE EMPLOYEE (

ID BIGINT AUTO_INCREMENT,

NAME VARCHAR(100)

);



Entity:

@Id

@GeneratedValue(strategy = GenerationType.IDENTITY)

private Long id;



Insert:

Employee emp = new Employee();

emp.setName("Nandini");

session.save(emp);



Hibernate executes:

INSERT INTO EMPLOYEE(NAME)

VALUES('Nandini');



Database generates:

ID = 1

Hibernate reads it back and populates the object.



Think of IDENTITY As

Hibernate: "Database, you generate the ID."

Database: "Okay, here's ID = 1."



3. SEQUENCE

Very common in Oracle and PostgreSQL.



Database sequence:

CREATE SEQUENCE EMP_SEQ

START WITH 1

INCREMENT BY 1;



Entity:

@Id

@GeneratedValue(

strategy = GenerationType.SEQUENCE,

generator = "emp_seq")

@SequenceGenerator(

name = "emp_seq",

sequenceName = "EMP_SEQ",

allocationSize = 1)

private Long id;



Before insert, Hibernate asks:

SELECT EMP_SEQ.NEXTVAL

Suppose:

5

Then Hibernate inserts:

INSERT INTO EMPLOYEE(ID, NAME)

VALUES(5, 'Nandini');



Think of SEQUENCE As

Hibernate: "Give me next ID."

Sequence: "5" 

Hibernate: "Thanks."

Then Hibernate performs the insert.



4. TABLE Strategy

Uses a special table to store IDs.

Example:

ID_GENERATOR

----------------

NEXT_ID

Rarely used in modern projects.

Interview answer:

TABLE strategy exists but is less performant and rarely used today.



Which Strategy Is Most Common?

Oracle

GenerationType.SEQUENCE

Most common.



MySQL

GenerationType.IDENTITY

Most common.



Spring Boot Projects

Typically:

@Id

@GeneratedValue(strategy = GenerationType.IDENTITY)

private Long id;

or

Java

@Id

@GeneratedValue(strategy = GenerationType.SEQUENCE)

private Long id;

depending on database.



Interview Question

Difference Between IDENTITY and SEQUENCE

IDENTITY

Database generates ID during INSERT

Flow:

INSERT

|

Database generates id



SEQUENCE

Hibernate gets ID before INSERT

Flow:

Get next sequence value

|

v

INSERT with generated value



Example

IDENTITY

@Id

@GeneratedValue(strategy = GenerationType.IDENTITY)

private Long id;



Create object:

Employee emp = new Employee();

emp.setName("Nandini");



Save:

session.save(emp);



Result:

ID automatically becomes: 1

No manual ID assignment.

Why use @GeneratedValue?

To automatically generate primary key values and avoid manual ID management.



What does GenerationType.IDENTITY do?

Uses the database's auto-increment/identity column to generate IDs.



What does GenerationType.SEQUENCE do?

Uses a database sequence object to generate IDs.



Which is commonly used in Oracle? SEQUENCE



Which is commonly used in MySQL? IDENTITY



What You Should Remember

@Entity

@Table(name = "EMPLOYEE")

public class Employee {



@Id

@GeneratedValue(strategy = GenerationType.IDENTITY)

private Long id;



private String name;

}

@Entity → class is a Hibernate entity.
@Table → maps to a database table.
@Id → primary key.
@GeneratedValue → Hibernate/database generates the ID.



Hibernate Architecture:

Your Java Code

       |

       v

Configuration

       |

       v

SessionFactory

       |

       v

Session

       |

       v

Transaction

       |

       v

Database

Each component has a responsibility.



Configuration

Reads configuration details.



Database URL

Username

Password

Hibernate Properties



SessionFactory

Creates Session objects.



Think of it as a:

Session Factory

      |

Creates Sessions

Usually created once when the application starts.



Session

Most important Hibernate object.

You use it to:

save()

get()

update()

delete()

Think:

Session = Connection between Hibernate and DB

(not exactly a JDBC Connection, but close enough for now)



Transaction

Groups DB operations.

Transaction tx =

    session.beginTransaction();

session.save(emp);

tx.commit();

Either all operations succeed or none.



Real-Life Analogy

Imagine a bank.

SessionFactory

Bank Head Office

Creates branches.

Session

Bank Branch

You go there and perform work.

Deposit

Withdraw

Transfer



Transaction

The actual banking operation.

Transfer ₹1000

Either completes fully or rolls back.



SessionFactory → Session → Transaction

Employee emp = new Employee();

emp.setName("Nandini");

Hibernate cannot directly save it.



It needs:

SessionFactory

      ↓

    Session

      ↓

 Transaction

      ↓

   Database



SessionFactory

What is it?

A SessionFactory is responsible for creating Session objects.

Think of it as a factory machine.

Factory

   |

Creates

   |

Sessions

Real-Life Example

Imagine a car factory.

Car Factory

      |

Creates Cars

Similarly:

SessionFactory

        |

Creates Sessions



Creation

Traditionally:

SessionFactory sessionFactory =

    new Configuration()

        .configure()

        .buildSessionFactory();

Don't worry about the syntax now.

Just remember:

One SessionFactory

Many Sessions


Is SessionFactory Heavy or Light?

Heavy.

Why?

Because it loads:

Database configuration
Entity mappings
Hibernate settings
Cache settings

How Many SessionFactory Objects?

Usually:

1 per application

Example:

Application Start

       |

Create SessionFactory Once

       |

Reuse Entire Application Life



Session

This is the most important Hibernate object.



What is Session?

A Session represents a conversation between Hibernate and the database.

Using Session, you perform:

save()

get()

update()

delete()

operations.



Creating a Session

Session session =

    sessionFactory.openSession();

Factory creates Session.



Real-Life Analogy

Suppose SessionFactory is a bank head office.

SessionFactory creates bank branches:



Session

You don't do work at the head office.

You go to a branch.

Similarly:

CRUD Operations

      ↓

Performed using Session



Example

Save Employee:

Session session =

    sessionFactory.openSession();

session.save(emp);



Fetch Employee:

Employee emp =

    session.get(Employee.class, 1L);



Update Employee:

session.update(emp);



Delete Employee:

session.delete(emp);



Everything happens through Session.

Is Session Thread Safe?

No.

Usually:

One Request

      ↓

One Session



Transaction

Now suppose:

session.save(employee);

session.save(address);



What happens if:

employee saved

address failed

Database becomes inconsistent.



Solution: Transaction

A transaction groups operations together.

Transaction tx =

    session.beginTransaction();

Transaction tx =

    session.beginTransaction();

session.save(employee);

session.save(address);

tx.commit();



What Does Commit Mean?

tx.commit();

Make changes permanent in the database.

 

What Does Rollback Mean?

Suppose:

Transaction tx =

    session.beginTransaction();

try {

    session.save(employee);

    int x = 10/0;

    tx.commit();

}

catch(Exception e){

    tx.rollback();

}

Exception occurs.

Rollback executes.

Result:

Employee NOT saved

Database returns to previous state.

Transaction Analogy:

Imagine transferring money.

Account A -> Account B



Steps:

Withdraw from A

Deposit into B

If deposit fails after withdrawal:

Money lost

Not acceptable.



So, Transaction ensures:

Both succeed

OR

Both fail

Complete Flow

SessionFactory sessionFactory =

    new Configuration()

        .configure()

        .buildSessionFactory();

Session session =

    sessionFactory.openSession();

Transaction tx =

    session.beginTransaction();

Employee emp = new Employee();

emp.setName("Nandini");

session.save(emp);

tx.commit();

session.close();

Flow:

Create SessionFactory

          ↓

Open Session

          ↓

Begin Transaction

          ↓

Save Employee

          ↓

Commit

          ↓

Close Session

Visual Diagram

Application

    |

    v

SessionFactory

    |

    v

Session

    |

    v

Transaction

    |

    v

Database

Responsibilities

SessionFactory: 



Creates Sessions

Heavy Object

Thread Safe

One Per Application



Session: 

Performs CRUD

Not Thread Safe

Short Lived

Created By SessionFactory



Transaction: 

Groups Database Operations

Commit

Rollback

Maintains Data Integrity



What is SessionFactory?

A heavyweight, thread-safe Hibernate object used to create Session instances.



What is Session?

A Hibernate object used to interact with the database and perform CRUD operations.



What is Transaction?

A unit of work that ensures all database operations either complete successfully or roll back together.



Can we perform database operations without Session?

No.

All Hibernate CRUD operations are performed through a Session.



Why do we need Transaction?

To maintain consistency and prevent partial database updates.


SessionFactory creates Sessions.

Session performs CRUD operations.

Transaction commits or rolls back database changes.



CRUD operations:

CRUD stands for:

C = Create

R = Read

U = Update

D = Delete

These are the 4 basic operations performed on a database table.



Let's use this table throughout:

EMPLOYEE

--------------------------

ID | NAME     | SALARY

--------------------------

1  | Nandini  | 50000

2  | Ravi     | 60000



And this entity:

@Entity

@Table(name="EMPLOYEE")

public class Employee {

    @Id

    @GeneratedValue(strategy = GenerationType.IDENTITY)

    private Long id;

    private String name;

    private Double salary;

}


CREATE (save)



Suppose you want to insert a new employee.



emp.setName("Nandini");

emp.setSalary(50000.0);


Hibernate Code

Session session =

        sessionFactory.openSession();

Transaction tx =

        session.beginTransaction();

session.save(emp);

tx.commit();

session.close();


What Happens Behind the Scenes?

Hibernate generates SQL:

INSERT INTO EMPLOYEE

(NAME, SALARY)

VALUES

('Nandini', 50000);


Database generates:

ID = 1

and stores the row.


READ (get)

Suppose employee ID is 1.

Employee emp =

       session.get(Employee.class, 1L);

Hibernate generates:

SELECT *

FROM EMPLOYEE

WHERE ID = 1;


Result:

System.out.println(emp.getName());



Output:

Nandini

What Does Employee.class Mean?

"Fetch an Employee entity whose primary key is 1."

get(EntityType, PrimaryKey)


What If Record Doesn't Exist?

Employee emp = session.get(Employee.class, 100L);
Suppose ID 100 doesn't exist.

Result:

null


No exception.



UPDATE

Suppose current row:

1 | Nandini | 50000

You want:

1 | Nandini | 70000



Step 1: Fetch

Employee emp =

        session.get(Employee.class, 1L);

Step 2: Modify

emp.setSalary(70000.0);

Step 3: Commit

tx.commit();

 

Hibernate automatically executes:

UPDATE EMPLOYEE

SET SALARY = 70000

WHERE ID = 1;



Important Concept: Dirty Checking

Notice:

emp.setSalary(70000.0);



We didn't call:

session.update(emp);



Hibernate detects that the object changed.

This feature is called: Dirty Checking



Hibernate compares:

Old Value = 50000

New Value = 70000

and generates UPDATE automatically.



DELETE

Suppose employee ID 1 should be deleted.

Step 1

Employee emp = session.get(Employee.class, 1L);



Step 2

session.delete(emp);



Step 3

tx.commit();



Hibernate executes:

DELETE

FROM EMPLOYEE

WHERE ID = 1;



Complete CRUD Example

Session session =

        sessionFactory.openSession();

Transaction tx =

        session.beginTransaction();

Employee emp = new Employee();

emp.setName("Nandini");

emp.setSalary(50000.0);

session.save(emp);

tx.commit();

session.close();



Create complete.



Another Complete Example

Session session =

        sessionFactory.openSession();

Transaction tx =

        session.beginTransaction();

Employee emp =

        session.get(Employee.class, 1L);

emp.setSalary(80000.0);

tx.commit();

session.close();

Update complete.



Another Complete Example

Session session =

        sessionFactory.openSession();

Transaction tx =

        session.beginTransaction();

Employee emp =

        session.get(Employee.class, 1L);

session.delete(emp);

tx.commit();

session.close();



Delete complete.



What does save() do?

session.save(emp);

Inserts a new row into the database.



What does get() do?

session.get(Employee.class, 1L);

Fetches an entity by its primary key.

Returns object if found.

Returns null if not found.



What does delete() do?

session.delete(emp);

Deletes the corresponding database row.



What does update() do?

Traditionally:

session.update(emp);

Updates an existing entity.

However, in modern Hibernate/JPA, updates are often handled automatically through dirty checking.



The Lifecycle of a Create Operation

Create Java Object

↓

Open Session

↓

Begin Transaction

↓

session.save()

↓

Hibernate generates INSERT

↓

Commit

↓

Row saved in DB



Insert

session.save(emp);

→ INSERT

Read

session.get(Employee.class, id);

→ SELECT

Update

emp.setSalary(70000);

→ UPDATE (via dirty checking on commit)

Delete

session.delete(emp);

→ DELETE



Entity Lifecycle States:

Every Hibernate entity (object) is always in one state at any given time.



There are 4 main states:

Transient

    ↓

Persistent

    ↓

Detached

    ↓

Removed



Let's understand them with one Employee object.



1. Transient State

When an object is created using new, it is in the Transient state.



Employee emp = new Employee();

emp.setName("Nandini");

emp.setSalary(50000.0);



At this moment:

✅ Object exists in Java memory

❌ Not stored in database

❌ Not associated with Hibernate Session

❌ Hibernate doesn't track it



Visual:

Employee Object

       |

       v

Java Memory Only



Example

Employee emp = new Employee();

State:

Transient



Database:

EMPLOYEE

---------

(empty)


Nothing is saved yet.



2. Persistent State

When you save the object using a Session:

session.save(emp);

the object becomes Persistent.



Session session =

        sessionFactory.openSession();

Transaction tx =

        session.beginTransaction();

Employee emp = new Employee();

emp.setName("Nandini");

session.save(emp);



Now:

✅ Hibernate knows about the object

✅ Associated with Session

✅ Hibernate tracks all changes

✅ Will be synchronized with DB

State:

Persistent

Visual:

Employee Object

       |

       v

Session

       |

       v

 Database



Why Persistent State Is Powerful

Hibernate starts tracking changes automatically.

Imagine:

session.save(emp);

emp.setSalary(70000.0);



You don't explicitly call:

session.update(emp);



But when:

tx.commit();



Hibernate executes:

UPDATE EMPLOYEE

SET SALARY = 70000

This happens because the entity is Persistent.



Dirty Checking

This automatic detection of changes is called:

Dirty Checking



Example:

Employee emp =

        session.get(Employee.class, 1L);

emp.setSalary(90000.0);

tx.commit();



Hibernate remembers:

Old Salary = 50000

New Salary = 90000



and generates:

UPDATE EMPLOYEE

SET SALARY = 90000

WHERE ID = 1;

automatically.



3. Detached State

Suppose we close the Session.

session.close();



The entity still exists:

Employee emp =

       session.get(Employee.class, 1L);



session.close();



Now:

✅ Object exists

✅ Data still available

❌ Session closed

❌ Hibernate no longer tracks changes



State:

Detached



Visual:

Employee Object

Session Closed



Example

Employee emp =

       session.get(Employee.class, 1L);



session.close();



emp.setSalary(100000.0);



Will Hibernate update the DB?

No.

Why?

Because the object is detached.

Hibernate is no longer watching it.



What is a Detached Entity?

A detached entity is an entity that was previously persistent but is no longer associated with an active Session.



4. Removed State

Suppose:

session.delete(emp);

Hibernate marks the entity for deletion.

session.delete(emp);



State:

Removed


When:

tx.commit();



Hibernate executes:

DELETE FROM EMPLOYEE

WHERE ID = ?


and removes the row.



Complete Lifecycle Flow

Employee emp =

        new Employee();

State: Transient



session.save(emp);

State: Persistent



session.close();

State: Detached



session.delete(emp);

State: Removed



Easy Real-Life Analogy

Imagine an employee joining a company.

Transient

Person exists

Not yet hired



Persistent

Person hired

Employee record exists

HR tracks everything



Detached

Employee left office for the day

HR not actively monitoring



Removed

Employee resigned

Record scheduled for deletion



Interview Scenario

Consider:

Session session =

        sessionFactory.openSession();

Employee emp =

        session.get(Employee.class, 1L);

session.close();

emp.setSalary(70000.0);



Will database update?

❌ No

Reason:

Entity is Detached

Hibernate isn't tracking changes anymore.



Which state supports Dirty Checking?

✅ Persistent State

Only persistent entities are automatically monitored by Hibernate.



Quick Summary

Transient

Employee emp = new Employee();



Not in DB 
Not in Session 
Hibernate doesn't track it



Persistent

session.save(emp);



In Session 
Hibernate tracks changes 
Dirty checking works



Detached

session.close();



Object exists 
Session closed 
No tracking



Removed

session.delete(emp);



Marked for deletion
Deleted on commit



Transient = new object not managed by Hibernate. Persistent = managed by an active Session. Detached = previously persistent but Session is closed. Removed = marked for deletion and will be deleted when the transaction commits.



get() vs load():

session.get(Employee.class, 1L);

session.load(Employee.class, 1L);



Both are used to fetch an entity by its primary key.

But their behavior is very different.



The Simple Difference

get()

Immediately hits the database.

Employee emp = session.get(Employee.class, 1L);



Hibernate executes:

SELECT * FROM EMPLOYEE

WHERE ID = 1;

right away.



load()

Returns a proxy object first.



Employee emp = session.load(Employee.class, 1L);

No SQL is executed immediately.

Hibernate creates a proxy and delays the database call until the object is actually used.

This is called lazy loading.



Example 1: get()

Employee emp =

session.get(Employee.class, 1L);



System.out.println("Done");



The moment get() runs:

SELECT * FROM EMPLOYEE WHERE ID=1

already executes.

Even if you never use emp.



Example 2: load()

Employee emp =

session.load(Employee.class, 1L);



System.out.println("Done");



Output:

Done



No database hit yet.



Only when:

System.out.println(emp.getName());



Hibernate executes:

SELECT * FROM EMPLOYEE WHERE ID=1



What is a Proxy?

Suppose:

Employee emp =

session.load(Employee.class, 1L);

Hibernate doesn't return the real Employee immediately.

It returns something like:

EmployeeProxy



Think:

Fake Employee

↓

Real Employee loaded only when needed

This saves database calls.



What Happens If Record Exists?

Suppose:

ID | NAME

----------

1 | Nandini



get()

Employee emp =

session.get(Employee.class, 1L);



Result:

emp.getName();



Output:

Nandini

Works.



load()

Employee emp =

session.load(Employee.class, 1L);



Result:

emp.getName();



Output:

Nandini

Also works.

For existing records, both appear similar.



What Happens If Record Does NOT Exist?

Suppose:

Employee emp =

session.get(Employee.class, 999L);

and ID 999 doesn't exist.



Result:

null

No exception.



load()

Employee emp =

session.load(Employee.class, 999L);



Initially:

Employee proxy returned

Looks okay.



But when accessed:

emp.getName();

Hibernate tries to fetch the row.



Since it doesn't exist:

ObjectNotFoundException

(or a related Hibernate exception)



Interview Question

Which One Returns Null?

get() returns null when no row exists.



Which One Throws Exception?

load() throws exception when the entity is actually accessed.



Session Closed Scenario

Suppose:

Employee emp =

session.load(Employee.class, 1L);



session.close();



emp.getName();



Problem:

load() still hasn't fetched the data.

Now Session is closed.

Hibernate cannot load the row anymore.



Result:

LazyInitializationException



With get()

Employee emp =

session.get(Employee.class, 1L);



session.close();



emp.getName();

Works.

Why?

Because data was already fetched from the database before the session closed.



Performance Perspective

get()

Immediate Database Hit

Even if object is never used.



load()

Database Hit Only When Needed

Can improve performance in some situations.



Real-Life Analogy

Suppose someone asks for an employee file.



get()

Go to archive room

Fetch file immediately

Hand it over



load()

Give file reference number

Fetch actual file only when opened



Interview Comparison

Feature

get()

load()

DB hit

Immediate

Lazy

Returns

Actual object

Proxy object

Record not found

Returns null

Throws exception on access

Lazy loading

No

Yes

Session closed issue

Usually no

Can cause LazyInitializationException



Which Is Used More in Modern Applications?

Most developers use:

session.get()

or in JPA:

entityManager.find()

because it's simpler and less error-prone.

load() is mainly used when lazy-loading behavior is specifically desired.



Interview Answer to Memorize

get() immediately fetches the entity from the database and returns null if the record does not exist. load() returns a proxy object and fetches data only when accessed; if the record does not exist, it throws an exception when the proxy is initialized.



✅ So remember:

get() = Immediate Fetch + returns null

load() = Lazy Fetch + proxy + exception if missing



FetchType.LAZY vs FetchType.EAGER:

Many Hibernate performance issues, LazyInitializationExceptions, and interview questions revolve around:

FetchType.LAZY

And

FetchType.EAGER



Why Do We Need Fetch Types?

Let's say you have two entities.

@Entity

public class Department {

    @Id

    private Long id;

    private String name;

    @OneToMany

    private List<Employee> employees;

}


A department can have many employees.

Example:

Department: IT

Employees:

- Nandini

- Ravi



Now suppose you fetch a department:

Department dept =

    session.get(Department.class, 1L);



Question:

Should Hibernate also fetch all employees immediately?Or only fetch them when they're actually needed? 
That's what FetchType decides.



FetchType.EAGER

EAGER means:

Fetch related data immediately.

Example:

@OneToMany(fetch = FetchType.EAGER)

private List<Employee> employees;



Now:

Department dept =

    session.get(Department.class, 1L);


Hibernate immediately fetches:

Department

+

Employees

Even if you never use the employees.



Visual

session.get(Department.class, 1L)

        |

        V

Department Loaded

Employees Also Loaded



Example

Department dept =

        session.get(Department.class, 1L);



Hibernate may execute:

SELECT * FROM DEPARTMENT

WHERE ID = 1;



And also:

SELECT * FROM EMPLOYEE

WHERE DEPT_ID = 1;

immediately.



Problem with EAGER

Suppose:

Department

|

5000 Employees

You only want:

dept.getName();



But Hibernate still loads all 5000 employees.



Result:

More memory

More SQL

Slower performance



FetchType.LAZY

LAZY means:

Don't load related data now. Load it only when needed.



Example:

@OneToMany(fetch = FetchType.LAZY)

private List<Employee> employees;



Now:

Department dept =

session.get(Department.class, 1L);



Hibernate loads only:

Department

Employees are not loaded yet.



What Happens Later?

dept.getEmployees();

Now Hibernate loads employees.

Only at this moment.



Visual

Fetch Department

|

V

Department Loaded



Employees NOT Loaded

Later:

dept.getEmployees()

↓

Employees Loaded



Real-Life Analogy

Suppose you're viewing an Amazon order.



EAGER

Amazon shows:

Order Details

Product Details

Invoice

Payment History

Delivery History

Customer Profile

all at once.

Even though you only wanted the order number.



LAZY

Amazon first shows:

Order Details



When you click:

View Invoice

then the invoice is loaded.

Much more efficient.



Example with SQL

EAGER

Department dept =

session.get(Department.class, 1L);

Immediately:

SELECT * FROM DEPARTMENT WHERE ID=1;



SELECT * FROM EMPLOYEE WHERE DEPT_ID=1;



LAZY

Department dept =

session.get(Department.class, 1L);



Only:

SELECT * FROM DEPARTMENT WHERE ID=1;

Later:

dept.getEmployees();

Then:

SELECT * FROM EMPLOYEE WHERE DEPT_ID=1;



Why Does Hibernate Prefer LAZY?

Because loading everything immediately is expensive.

Imagine:

Department

|

Employees

|

Projects

|

Tasks

|

Comments



If everything were EAGER:

Huge amount of data loaded

even if you need only one department name.



Default Fetch Types (Very Important)

Collection Relationships

@OneToMany

@ManyToMany



Default:

LAZY



Single Object Relationships

@ManyToOne

@OneToOne



Default:

EAGER



Memorize this.

Collections → LAZY

Single Object → EAGER



LazyInitializationException

Very common interview question.

Example:

Department dept =

session.get(Department.class, 1L);



session.close();

Now:

dept.getEmployees();

Hibernate tries to load employees.



But:

Session already closed

Result:

LazyInitializationException



Why?

Because with LAZY loading, Hibernate waits until:

dept.getEmployees()

to fetch employees.

By then, the Session is gone.

Hibernate can no longer query the database.



Example That Works

Department dept =

session.get(Department.class, 1L);



dept.getEmployees().size();



session.close();

Here employees are loaded before closing the Session.

No exception.



Interview Questions

What is FetchType.LAZY?

Related data is fetched only when accessed for the first time.



What is FetchType.EAGER?

Related data is fetched immediately along with the parent entity.



Which is better for performance?

Generally:

LAZY

because unnecessary data is not loaded.



What causes LazyInitializationException?

Accessing a lazy-loaded association after the Session has been closed.



Default Fetch Types?

@OneToMany -> LAZY

@ManyToMany -> LAZY



@ManyToOne -> EAGER

@OneToOne -> EAGER



Easy Way to Remember

EAGER

Fetch Now

Example:

Department

+

Employees

loaded together.



LAZY

Fetch Later

Example:

Department first



Employees only when needed



Interview One-Liner

FetchType.EAGER loads related entities immediately, while FetchType.LAZY loads them only when they are accessed. LAZY is generally preferred for better performance and reduced memory usage.



Hibernate Relationships:

Let's use a simple business example throughout:

Department

Employee

Passport

Student

Course



There are 4 main relationships:

1. One-to-One

2. One-to-Many

3. Many-to-One

4. Many-to-Many



1. One-to-One (@OneToOne)

Example

A person has one passport.

Person 1 <---> 1 Passport



Example:

Nandini -> Passport P123

Ravi -> Passport P456

One person = one passport.

One passport = one person.



Database Tables:

PERSON

ID | NAME

------------

1 | Nandini



PASSPORT

ID | PASSPORT_NO | PERSON_ID

---------------------------

10 | P123 | 1



Entity Mapping

Person

@Entity

public class Person {



@Id

private Long id;



private String name;



@OneToOne

@JoinColumn(name = "PASSPORT_ID")

private Passport passport;

}



Passport

@Entity

public class Passport {



@Id

private Long id;



private String passportNo;

}



Access

Person person =

session.get(Person.class, 1L);



System.out.println(

person.getPassport().getPassportNo());



2. One-to-Many (@OneToMany)

Example

One Department has many Employees.

Department

|

+-- Nandini

+-- Ravi

+-- John



Relationship:

One Department

Many Employees



Database

DEPARTMENT

ID | NAME

---------

1 | IT



EMPLOYEE

ID | NAME | DEPT_ID

----------------------

1 | Nandini | 1

2 | Ravi | 1

3 | John | 1



Entity

Department

@Entity

public class Department {



@Id

private Long id;



private String name;



@OneToMany(mappedBy = "department")

private List<Employee> employees;

}



Employee

@Entity

public class Employee {



@Id

private Long id;



private String name;



@ManyToOne

private Department department;

}



Access

Department dept =

session.get(Department.class, 1L);



List<Employee> employees =

dept.getEmployees();



What is mappedBy?

This confuses beginners.



@OneToMany(mappedBy = "department")

means:

"The relationship is managed by the department field inside Employee."



Hibernate looks here:

private Department department;

to understand the relationship.



3. Many-to-One (@ManyToOne)

This is actually the reverse side of the previous example.



Example

Many employees belong to one department.

Nandini ---> IT

Ravi -----> IT

John -----> IT

Many Employees.

One Department.



Entity

@Entity

public class Employee {



@Id

private Long id;



@ManyToOne

@JoinColumn(name = "DEPT_ID")

private Department department;

}



Access

Employee emp =

session.get(Employee.class, 1L);



System.out.println(

emp.getDepartment().getName());



Output:

IT



One-to-Many vs Many-to-One

These are usually used together.

Department

|One

|

v

Employees

Many



From Department's viewpoint:

@OneToMany

From Employee's viewpoint:

@ManyToOne

Same relationship, different perspective.



4. Many-to-Many (@ManyToMany)

Example

Students and Courses.

Student A -> Java

Student A -> Hibernate



Student B -> Java

Student B -> Spring

One student can join many courses.

One course can have many students.



Relationship

Many Students

↕

Many Courses



How Database Stores It

Hibernate creates a join table.



STUDENT

ID | NAME

---------

1 | Nandini



COURSE

ID | NAME

---------

1 | Java

2 | Hibernate



STUDENT_COURSE

STUDENT_ID | COURSE_ID

----------------------

1 | 1

1 | 2



Entity

Student

@Entity

public class Student {



@Id

private Long id;



@ManyToMany

@JoinTable(

name = "STUDENT_COURSE",

joinColumns =

@JoinColumn(name = "STUDENT_ID"),

inverseJoinColumns =

@JoinColumn(name = "COURSE_ID")

)

private List<Course> courses;

}



Course

@Entity

public class Course {



@Id

private Long id;



@ManyToMany(mappedBy = "courses")

private List<Student> students;

}



Relationship Cheat Sheet

One-to-One

Person ↔ Passport

Annotation:

@OneToOne



One-to-Many

Department → Employees

Annotation:

@OneToMany



Many-to-One

Employees → Department

Annotation:

@ManyToOne



Many-to-Many

Students ↔ Courses

Annotation:

@ManyToMany



Most Important Interview Questions

Q1: Which side usually contains the foreign key?

The @ManyToOne side.

Example:

@ManyToOne

@JoinColumn(name="DEPT_ID")

private Department department;



Here:

DEPT_ID

is the foreign key.



Q2: Default Fetch Types?

@OneToMany -> LAZY

@ManyToMany -> LAZY



@ManyToOne -> EAGER

@OneToOne -> EAGER

Memorize this.



Q3: Which relationship creates a join table?

@ManyToMany

Example:

STUDENT_COURSE



Q4: Which relationship is used most in enterprise applications?

Very commonly:

@ManyToOne

@OneToMany

Example:

Order → Customer

Employee → Department

Account → Branch



What You Should Remember

@OneToOne

Person ↔ Passport



@OneToMany

Department → Employees



@ManyToOne

Employees → Department



@ManyToMany

Students ↔ Courses

Cascade Types:

What Problem Does Cascade Solve?

Suppose we have:

Department department = new Department();

department.setName("IT");



Employee emp1 = new Employee();

emp1.setName("Nandini");



Employee emp2 = new Employee();

emp2.setName("Ravi");



department.setEmployees(List.of(emp1, emp2));



Without cascade:

session.save(emp1);

session.save(emp2);

session.save(department);



You must save everything manually.

If you forget:

session.save(emp2);

you may get errors or missing data.



Cascade means:

"Whatever operation I perform on the parent entity, automatically perform it on the child entities too."

Example:

@OneToMany(cascade = CascadeType.ALL)

private List<Employee> employees;



Now:

session.save(department);



Hibernate automatically saves:

Department

Employee 1

Employee 2



Parent and Child

Example:

Department

|

+-- Employee 1

+-- Employee 2

Here:

Department = Parent



Employees = Children

Cascade allows operations to flow from Parent → Child.



CascadeType.PERSIST

Used during save/insert.



@OneToMany(cascade = CascadeType.PERSIST)

private List<Employee> employees;



Now:

session.persist(department);



Hibernate saves:

Department

Employees

automatically.



Without PERSIST

session.persist(department);

may fail because employees are not yet saved.



CascadeType.MERGE

Suppose entities are detached.

department.setName("IT Updated");

Later:

session.merge(department);

With:

cascade = CascadeType.MERGE



Hibernate merges:

Department

Employees

together.



CascadeType.REMOVE

Very important.

Suppose:

Department

|

+-- Employee 1

+-- Employee 2

You delete department:



session.remove(department);

With:

cascade = CascadeType.REMOVE



Hibernate deletes:

Department

Employee 1

Employee 2

automatically.



Without REMOVE

Only department gets deleted.

Employees may remain orphaned or foreign-key constraints may fail.



CascadeType.REFRESH

Used rarely.

Suppose database row changed externally.



session.refresh(department);

With cascade:

CascadeType.REFRESH

child entities are refreshed as well.



CascadeType.DETACH

Suppose:

session.detach(department);

Parent becomes detached.

With:

CascadeType.DETACH

children become detached too.



CascadeType.ALL

Most common for learning purposes.

@OneToMany(cascade = CascadeType.ALL)

private List<Employee> employees;



Equivalent to:

cascade = {

PERSIST,

MERGE,

REMOVE,

REFRESH,

DETACH

}

Everything cascades.



Example

@Entity

public class Department {



@Id

private Long id;



private String name;



@OneToMany(

mappedBy = "department",

cascade = CascadeType.ALL

)

private List<Employee> employees;

}



Now:

session.save(department);

saves employees automatically.



Real-Life Analogy

Think of a folder:



Department Folder

|

+-- Employee File 1

+-- Employee File 2



Without cascade:

Delete folder

Delete file 1

Delete file 2

all manually.



With cascade:

Delete folder

Everything inside is deleted automatically.



Difference Between Cascade and FetchType

Many beginners confuse these.

FetchType

Controls:

WHEN data is loaded



Example:

LAZY

EAGER



Cascade

Controls:

WHICH operations propagate



Example:

PERSIST

MERGE

REMOVE



Easy Memory Trick

FetchType:

Loading Problem



Cascade:

Operation Propagation Problem



What is Cascading?

Cascading allows operations performed on a parent entity to automatically propagate to its associated child entities.



What does CascadeType.PERSIST do?

Saving the parent automatically saves the children.



What does CascadeType.REMOVE do?

Deleting the parent automatically deletes the children.



What does CascadeType.ALL do?

Applies all cascade operations:

PERSIST

MERGE

REMOVE

REFRESH

DETACH



Difference Between CascadeType.REMOVE and orphanRemoval?

Good advanced interview question.

@OneToMany(

cascade = CascadeType.ALL,

orphanRemoval = true

)

REMOVE deletes children when parent is deleted.
orphanRemoval=true deletes a child when it is removed from the parent's collection.



Example:

department.getEmployees().remove(emp1);

With:

orphanRemoval = true

Employee row is deleted automatically.



What to Remember

PERSIST -> Save child automatically



MERGE -> Update child automatically



REMOVE -> Delete child automatically



REFRESH -> Refresh child automatically



DETACH -> Detach child automatically



ALL -> Everything automatically



HQL (Hibernate Query Language): 

HQL is where Hibernate becomes much more powerful than basic CRUD.



It looks like SQL, but there is one very important difference:

SQL works with table names and column names
HQL works with Entity names and field names



SQL Example

Database table:

EMPLOYEE

SELECT * FROM EMPLOYEE;

Uses:

Table Name = EMPLOYEE



HQL Example

Entity:

@Entity

@Table(name = "EMPLOYEE")

public class Employee {



@Id

private Long id;



private String name;



private Double salary;

}



HQL:

"FROM Employee"



Notice:

Employee

is the Java class name, not the table name.



Why HQL?

Without HQL:

SELECT * FROM EMPLOYEE WHERE SALARY > 50000

You need knowledge of table names and column names.



With HQL:

FROM Employee e

WHERE e.salary > 50000

You work entirely with Java entities.



Basic HQL Query

Fetch All Employees

String hql = "FROM Employee";



Query<Employee> query =

session.createQuery(hql, Employee.class);



List<Employee> employees =

query.getResultList();

Hibernate generates SQL internally.



Meaning of FROM Employee

FROM Employee

means:

Fetch all Employee entities



Equivalent SQL:

SELECT * FROM EMPLOYEE;



Using Alias

Very common.

FROM Employee e

Here:

e

is an alias.



Like SQL:

SELECT *

FROM EMPLOYEE e



Example:

FROM Employee e

WHERE e.salary > 50000



Notice we use:

salary

the Java field name.



Not:

SALARY

the column name.



WHERE Clause

Suppose:

Nandini 50000

Ravi 70000

John 90000

Get employees earning more than 60000.



String hql =

"FROM Employee e WHERE e.salary > 60000";



Equivalent SQL:

SELECT *

FROM EMPLOYEE

WHERE SALARY > 60000;



Named Parameters

Very important.

Instead of:

String hql =

"FROM Employee e WHERE e.salary > 60000";



Use:

String hql =

"FROM Employee e WHERE e.salary > :salary";



Set parameter:

Query<Employee> query =

session.createQuery(hql, Employee.class);



query.setParameter("salary", 60000.0);



Why Parameters?

Bad

"FROM Employee e WHERE e.salary > " + value



Good

"FROM Employee e WHERE e.salary > :salary"

More readable and safer.



Fetch by Name

String hql =

"FROM Employee e WHERE e.name = :name";



query.setParameter("name", "Nandini");



Equivalent SQL:

SELECT *

FROM EMPLOYEE

WHERE NAME='Nandini';



SELECT Specific Fields

Suppose you only need names.

String hql =

"SELECT e.name FROM Employee e";



Result:

Nandini

Ravi

John

Only names returned.



ORDER BY

Ascending salary.

String hql =

"FROM Employee e ORDER BY e.salary";



Descending salary.

String hql =

"FROM Employee e ORDER BY e.salary DESC";



Equivalent SQL:

ORDER BY SALARY DESC



Aggregate Functions

Same idea as SQL.



Count

String hql =

"SELECT COUNT(e) FROM Employee e";

Returns:

Total Employees



Maximum Salary

String hql =

"SELECT MAX(e.salary) FROM Employee e";



Minimum Salary

String hql =

"SELECT MIN(e.salary) FROM Employee e";



Average Salary

String hql =

"SELECT AVG(e.salary) FROM Employee e";



Update Query

Increase salary.

String hql =

"UPDATE Employee e " +

"SET e.salary = :salary " +

"WHERE e.id = :id";



Execute:

Query query =

session.createQuery(hql);



query.setParameter("salary", 80000.0);

query.setParameter("id", 1L);



query.executeUpdate();



Equivalent SQL:

UPDATE EMPLOYEE

SET SALARY = 80000

WHERE ID = 1;



Delete Query

String hql =

"DELETE FROM Employee e WHERE e.id = :id";



Execute:

query.setParameter("id", 1L);



query.executeUpdate();



Equivalent SQL:

DELETE FROM EMPLOYEE

WHERE ID = 1;



HQL Join

Very common in interviews.

Suppose:

Department

|

Employees



Entity:

@ManyToOne

private Department department;

Fetch employees belonging to IT department.

String hql =

"SELECT e " +

"FROM Employee e " +

"JOIN e.department d " +

"WHERE d.name = :dept";



Notice:

e.department

We are joining through entity relationships, not foreign-key columns.

This is one of the biggest strengths of HQL.



Q1. Difference Between SQL and HQL?

SQL

SELECT * FROM EMPLOYEE;

Uses:

Tables
Columns



HQL

FROM Employee

Uses:

Entities
Entity fields



Q2. Why Use HQL?

Because it is object-oriented and database-independent.

You write queries against Java entities instead of database tables.



Q3. What Does FROM Employee Mean?

Fetch all Employee entities.

Equivalent to:

SELECT * FROM EMPLOYEE;



Q4. How Do You Pass Parameters in HQL?

:salary



Example:

query.setParameter("salary", 50000);



Q5. Can HQL Perform Update and Delete?

Yes.

UPDATE Employee ...

DELETE FROM Employee ...



HQL vs SQL Example

SQL

SELECT *

FROM EMPLOYEE

WHERE SALARY > 50000;



HQL

FROM Employee e

WHERE e.salary > 50000



Notice:

EMPLOYEE -> Employee

SALARY -> salary

Table/column names become entity/field names.



Memorize These 5 Queries

Fetch all:

FROM Employee



Fetch by condition:

FROM Employee e

WHERE e.salary > :salary



Order:

FROM Employee e

ORDER BY e.salary DESC



Update:

UPDATE Employee e

SET e.salary = :salary

WHERE e.id = :id



Delete:

DELETE FROM Employee e

WHERE e.id = :id



Hibernate Performance

First-Level Cache
Second-Level Cache
N+1 Query Problem



1. First-Level Cache

What is a Cache?

A cache is temporary storage that avoids unnecessary database calls.



Instead of:

Application

|

Database Call

|

Application

every time, Hibernate can reuse already-fetched data.



What is First-Level Cache?

First-Level Cache is:

Session-level Cache



Every Hibernate Session automatically has a cache.

You do not need to configure it.

It's enabled by default.



Example

Database:

EMPLOYEE

ID | NAME

-----------

1 | Nandini



Code:

Session session =

sessionFactory.openSession();



Employee e1 =

session.get(Employee.class, 1L);



Employee e2 =

session.get(Employee.class, 1L);



What Happens?

First get()

session.get(Employee.class, 1L);



Hibernate executes:

SELECT * FROM EMPLOYEE

WHERE ID = 1;

and stores the object in Session cache.



Second get()

session.get(Employee.class, 1L);

Hibernate does not hit the database.

It returns the cached object.



Visual

1st Call

|

Database Hit

|

Stored in Session Cache



2nd Call

|

Returned from Cache



Is First-Level Cache Enabled by Default?

✅ Yes

Always.



Scope

One Session Only



If Session closes:

session.close();

cache disappears.



Example

Session 1:

Employee e1 =

session.get(Employee.class, 1L);

Database hit.



Session closed.



Session 2:

Employee e2 =

session2.get(Employee.class, 1L);

Database hit again.

Because first-level cache belongs only to Session 1.



Interview Answer

First-Level Cache is a mandatory Session-level cache enabled by default. Hibernate stores entities fetched within the same Session and avoids repeated database queries.



2. Second-Level Cache

Problem:

First-level cache works only inside one Session.



Suppose:

Request 1

|

Session 1

fetches Employee.



Later:

Request 2

|

Session 2

fetches the same Employee.

Database is queried again.



Solution

Second-Level Cache.



What is Second-Level Cache?

A cache shared across Sessions.

Session 1

\

\

Second Level Cache

/

/

Session 2



Scope

SessionFactory Level

Unlike first-level cache which is session level



Example

Session 1:

Employee emp =

session1.get(Employee.class, 1L);

Database hit.

Cache stores entity.



Session 1 closes.



Session 2:

Employee emp =

session2.get(Employee.class, 1L);

Returned from second-level cache.

No database hit.



Popular Providers

Hibernate uses external providers such as:

EhCache

Hazelcast

Infinispan



First-Level Cache

Session Scope

Default Enabled 

Cannot Be Disabled



Second-Level Cache

SessionFactory Scope 

Not Enabled By Default

Requires Configuration



Easy Memory Trick

First Level -> My Room

Second Level -> Entire Building



Room:

One Session

Building:

All Sessions



3. N+1 Query Problem

This is one of the most important Hibernate interview topics.



Scenario

Department:

IT

HR

Finance

Each department has employees.



Relationship:

Java

@OneToMany(fetch = FetchType.LAZY)

private List<Employee> employees;



Code

List<Department> departments =

session.createQuery(

"FROM Department",

Department.class)

.getResultList();



for (Department d : departments) {

System.out.println(

d.getEmployees().size());

}



What Happens?

Query 1

Fetch all departments.

SELECT *

FROM DEPARTMENT;



Suppose:

3 departments returned



Loop Begins

Department 1:

SELECT *

FROM EMPLOYEE

WHERE DEPT_ID = 1;



Department 2:

SELECT *

FROM EMPLOYEE

WHERE DEPT_ID = 2;



Department 3:

SELECT *

FROM EMPLOYEE

WHERE DEPT_ID = 3;

Total Queries:

1 + 3 = 4

For N departments:

1 + N queries

This is called:

N+1 Query Problem



Why Is It Bad?

Imagine:

1000 Departments



Results:

1 + 1000 Queries

Terrible performance.



Solution: Fetch Join

Instead of:

FROM Department

use:

SELECT d

FROM Department d

JOIN FETCH d.employees



What Happens?

Hibernate fetches:

Departments

+

Employees

in one query.



Example SQL:

SELECT *

FROM DEPARTMENT d

JOIN EMPLOYEE e

ON d.ID = e.DEPT_ID



Result

Instead of:

1 + N Queries

you get:

1 Query

much faster.



What is First-Level Cache?

Session-level cache enabled by default.



What is Second-Level Cache?

SessionFactory-level cache shared across multiple Sessions.



Which cache is enabled by default?

✅ First-Level Cache

❌ Second-Level Cache



What is the N+1 Query Problem?

Fetching a collection lazily causes one query for parent entities and additional queries for each child collection.



How do you solve the N+1 Query Problem?

Using:

JOIN FETCH



Example:

SELECT d

FROM Department d

JOIN FETCH d.employees



Quick Revision

First-Level Cache

Session Cache

Default Enabled

One Session



Second-Level Cache

SessionFactory Cache

Shared Across Sessions

Requires Configuration



N+1 Problem

1 Query For Parents

+

N Queries For Children



Solution

JOIN FETCH

Fetch Everything Needed

In A Single Query



Dirty Checking:

This is one of Hibernate's most powerful features.



Dirty Checking means:

Hibernate automatically detects changes made to a Persistent entity and updates the database during flush/commit.



Example

Assume Employee exists:

ID | NAME | SALARY

---------------------

1 | Nandini | 50000



Fetch it:

Session session = sessionFactory.openSession();



Transaction tx = session.beginTransaction();



Employee emp =

session.get(Employee.class, 1L);



At this moment:

Persistent State



Now change salary:

emp.setSalary(70000.0);



Notice:

session.update(emp);

is NOT called.



Commit:

tx.commit();



Hibernate detects:

Old Salary = 50000

New Salary = 70000



and automatically executes:

UPDATE EMPLOYEE

SET SALARY=70000

WHERE ID=1;

Why Does This Work?

Because the entity is:

Persistent

Hibernate keeps a snapshot of Initial Values

and compares it with Current Values

during flush.



Which entities participate in Dirty Checking?

✅ Persistent Entities

❌ Detached Entities

❌ Transient Entities



flush():

Many developers confuse: flush() and commit()

Let's fix that.



Flush means:

Synchronize changes from memory to the database.



Example:

emp.setSalary(70000);

Currently:

Java Memory Updated

Database Not Yet Updated



When:

session.flush();

Hibernate executes SQL:

UPDATE EMPLOYEE

SET SALARY=70000

WHERE ID=1

immediately.



Important

After flush:

SQL executed

BUT

Transaction not committed yet



Example

Transaction tx =

session.beginTransaction();



emp.setSalary(70000);



session.flush();

Database receives UPDATE.

But:

tx.rollback();

is still possible.



Result:

Salary reverts back



Visual

Entity Changed

|

v

flush()

|

v

SQL Executed

|

v

Still Inside Transaction



commit():

Commit means:

Permanently save transaction changes.

Example:

emp.setSalary(70000);



tx.commit();

Hibernate does:

flush()

+

commit()

internally.



Visual

Memory Change

|

v

Flush SQL

|

v

Commit Transaction

|

v

Permanent



flush()

Synchronizes changes with DB.

commit()

Makes transaction permanent.



Example Timeline

emp.setSalary(70000);



Database:

Still 50000



Java

session.flush();

Database receives:

UPDATE ...

But rollback is still possible.



tx.commit();

Now:

Permanent



Does commit() call flush()?

✅ Yes.

Before commit Hibernate flushes pending changes.



Easy Memory Trick

flush() = Send SQL

commit() = Finalize SQL



merge() vs update():

This is a famous Senior Java interview question.



First Understand Detached Entities

Suppose:

Session session =

sessionFactory.openSession();



Employee emp =

session.get(Employee.class, 1L);



session.close();

Now:

Detached Entity



Modify it:

emp.setSalary(90000);

Hibernate won't track it anymore.

Need to reattach.



Two approaches:

update()

merge()



update():

Session newSession =

sessionFactory.openSession();

newSession.update(emp);

Now emp becomes managed again.

Hibernate updates the row.



Problem with update()

Suppose Session already contains the same entity:



Employee managed =

newSession.get(Employee.class, 1L); 

newSession.update(emp);



Now two objects represent:

Employee ID = 1

Hibernate gets confused.

Result:

NonUniqueObjectException



merge():

Safer option.

Employee merged =

newSession.merge(emp);



Hibernate:

Finds existing managed entity
Copies values from detached object
Returns managed entity

No conflict.



Visual

Detached:

emp.setSalary(90000);

Then:

Employee managed =

session.merge(emp);



Hibernate copies values:

Detached Object

|

V

Managed Object



Important Detail

update()

Returns:

void



merge()

Returns:

Managed Entity



Example:

Employee managed =

session.merge(emp);

Use the returned object afterwards.



Interview Example

Employee detached =

oldSession.get(Employee.class, 1L);



oldSession.close();



detached.setSalary(90000);



Later:

newSession.merge(detached);

Recommended.



Why Is merge Preferred?

Because:

Safer

Handles Existing Managed Objects

Avoids NonUniqueObjectException

Widely Used With JPA



Interview Comparison

update()

Works with Detached Entities

Returns void

May throw NonUniqueObjectException

Older Style



merge()

Works with Detached Entities

Returns Managed Entity

Safer

Recommended Approach



What is Dirty Checking?

Hibernate automatically detects modifications to persistent entities and generates SQL updates during flush/commit.



What is flush()?

Synchronizes the persistence context with the database by executing pending SQL statements.



What is commit()?

Makes transaction changes permanent in the database.



Difference Between flush() and commit()?

flush() -> Executes SQL

commit() -> Permanently saves transaction

Rollback possible after flush but before commit.



Difference Between merge() and update()?

merge() -> Safe, returns managed entity

update() -> Can throw NonUniqueObjectException

Modern recommendation:

merge()



Quick Revision Sheet

Dirty Checking: Hibernate automatically detects changes to persistent entities.



flush(): Executes pending SQL statements.



commit(): flush() + permanently save transaction.



update(): Reattaches detached entity.

May throw NonUniqueObjectException.



merge(): Copies detached state into managed entity.

Safer and preferred.



Concurrency handling:

Optimistic Locking (@Version) and Pessimistic Locking



Why Do We Need Locking?

Imagine the database contains:

Employee

ID = 1

Salary = 50000



Two users open the same employee record at the same time.

User A reads Salary = 50000

User B reads Salary = 50000

Both modify it.



User A changes Salary to 60000 

User B changes Salary to 70000



Who wins?

Without locking, the last update overwrites the previous update.



Result:

Lost Update Problem



Hibernate provides two major solutions:

1. Optimistic Locking

2. Pessimistic Locking



Optimistic Locking

Basic Idea

Optimistic Locking assumes:

Conflicts are rare.

So:

Allow everyone to work freely

Check for conflicts during update

No row is locked while users are editing.



How Hibernate Implements It

Using:

@Version



Example:

@Entity

public class Employee {



@Id

private Long id;



private String name;



private Double salary;



@Version

private Integer version;

}



Database Table

ID | NAME | SALARY | VERSION

--------------------------------

1 | Nandini | 50000 | 1



Scenario

User A Reads

Salary = 50000

Version = 1



User B Reads

Salary = 50000

Version = 1



User A Updates

Salary = 60000



Hibernate executes:

UPDATE EMPLOYEE

SET SALARY = 60000,

VERSION = 2

WHERE ID = 1

AND VERSION = 1;

Success.



Database:

Salary = 60000

Version = 2



User B Updates

Hibernate tries:

UPDATE EMPLOYEE

SET SALARY = 70000,

VERSION = 2

WHERE ID = 1

AND VERSION = 1;



Problem:

Version is no longer 1

No rows updated.



Hibernate throws:

OptimisticLockException



Why Is This Good?

Instead of silently overwriting User A's changes:

User B gets an error



Application can:

Refresh data

Show warning

Ask user to retry



Visual

User A ------ Version 1

|

V

Update Success -> Version 2



User B ------ Version 1

|

V

Update Fails

What does @Version do?

@Version

private Integer version;

Adds version checking to detect concurrent updates.



What exception is thrown?

OptimisticLockException



Is the database row locked?

❌ No

Optimistic locking does NOT lock rows.



Pessimistic Locking

Basic Idea

Pessimistic Locking assumes:

Conflicts are likely.

So:

Lock the row immediately

Prevent others from modifying it



Real-Life Example

Think of a bank account.

Balance = 1000

User A starts transferring money.

The system may lock the account record.



Until User A finishes:

Nobody else can update it



Hibernate Example

Employee emp =

session.find(

Employee.class,

1L,

LockModeType.PESSIMISTIC_WRITE

);

Hibernate requests a database lock.



What Happens?

User A:

Locks Employee ID=1

User B:

Attempts Update

Result:

Wait

or

Timeout

depending on database settings.



Under the Hood

Hibernate may generate SQL like:

SELECT *

FROM EMPLOYEE

WHERE ID = 1

FOR UPDATE



FOR UPDATE tells the database:

Lock this row



Types of Pessimistic Locks

PESSIMISTIC_READ

LockModeType.PESSIMISTIC_READ

Allows reads.

Prevents modifications.



PESSIMISTIC_WRITE

LockModeType.PESSIMISTIC_WRITE

Most common.

Exclusive lock.

Others cannot modify.



PESSIMISTIC_FORCE_INCREMENT

LockModeType.PESSIMISTIC_FORCE_INCREMENT

Locks row and increments version.

Advanced usage.



Real World Comparison

Optimistic

No lock 

Everyone works

Conflict checked at save time



Example:

Online profile update

Employee information screen

Customer details page

Pessimistic

Lock immediately

Others wait



Example:

Money transfer

Stock trading

Inventory reservation



Optimistic vs Pessimistic

Optimistic

Pessimistic

No DB lock

DB lock

Uses @Version

Uses lock modes

Better performance

Slower

Detects conflicts later

Prevents conflicts immediately

Most commonly used

Used in critical transactions



Which One Is More Common?

In modern enterprise applications:

✅ Optimistic Locking

Why?

Higher Scalability

Better Performance

Less Database Blocking



Most Spring Boot / Hibernate applications use:

@Version

private Integer version;



What is Optimistic Locking?

Optimistic locking uses a version field (@Version) to detect concurrent updates without locking database rows.



What is Pessimistic Locking?

Pessimistic locking locks database records when they are read so that other transactions cannot modify them until the lock is released.



Which is more scalable?

Optimistic Locking



Which annotation is used for Optimistic Locking?

@Version



Which exception occurs during Optimistic Locking conflicts?

OptimisticLockException



Hibernate Criteria API and JPA Criteria API:

Criteria API is basically Hibernate/JPA's way of building queries in Java code instead of writing query strings.

Before learning it, understand the problem it solves.



Problem with HQL

Suppose you write:

String hql =

"FROM Employee e WHERE e.salary > :salary";

Looks fine.



But what if the query is dynamic?

Example:

Search Employees



Name -> Optional

Salary -> Optional

Department -> Optional



Sometimes user provides:

Name only

Sometimes:

Salary only

Sometimes:

Name + Department



Building HQL strings becomes messy:

String hql = "FROM Employee e WHERE 1=1";



if(name != null) {

hql += " AND e.name = :name";

}



if(salary != null) {

hql += " AND e.salary > :salary";

}

This gets ugly quickly.



What is Criteria API?

Criteria API allows you to build queries programmatically.



Instead of:

String hql =

"FROM Employee e WHERE e.salary > :salary";



you write Java objects:

CriteriaBuilder

CriteriaQuery

Root

Predicate



Think:

HQL = Query String

Criteria API = Query Objects



Advantages

Type Safe

Compiler can catch errors.



Bad HQL:

"FROM Employee e WHERE e.sallary > 1000"

Typo:

sallary

Error discovered at runtime.



Criteria:

root.get("salary");

Much easier to manage.



Dynamic Queries

Biggest advantage.

You can add conditions dynamically.



Core Objects

You'll see these everywhere:

CriteriaBuilder

CriteriaQuery

Root

Predicate

Let's learn them one by one.



1. CriteriaBuilder

Used to create query components.



CriteriaBuilder cb =

entityManager.getCriteriaBuilder();



Think:

Factory for Criteria Objects



2. CriteriaQuery

Represents the query.

CriteriaQuery<Employee> query =

cb.createQuery(Employee.class);



Equivalent to:

FROM Employee

in HQL.



3. Root

Represents the entity being queried.

Root<Employee> root =

query.from(Employee.class);



Equivalent to:

FROM Employee e



Here:

root

acts like alias e.



First Criteria Query

HQL:

FROM Employee



Criteria:

CriteriaBuilder cb =

entityManager.getCriteriaBuilder();



CriteriaQuery<Employee> query =

cb.createQuery(Employee.class);



Root<Employee> root =

query.from(Employee.class);



query.select(root);



List<Employee> result =

entityManager

.createQuery(query)

.getResultList();



WHERE Clause

HQL:

FROM Employee e

WHERE e.salary > 50000



Criteria:

query.where(

cb.gt(

root.get("salary"),

50000

)

);



Meaning:

salary > 50000



Full Example

CriteriaBuilder cb =

entityManager.getCriteriaBuilder();



CriteriaQuery<Employee> query =

cb.createQuery(Employee.class);



Root<Employee> root =

query.from(Employee.class);



query.select(root)

.where(cb.gt(

root.get("salary"),

50000));



List<Employee> employees =

entityManager

.createQuery(query)

.getResultList();

Equivalent HQL:

FROM Employee e

WHERE e.salary > 50000



Predicate

A Predicate represents a condition.



Example:

salary > 50000



Criteria:

Predicate salaryCondition =

cb.gt(

root.get("salary"),

50000

);



Apply it:

query.where(salaryCondition);



Multiple Conditions

HQL:

FROM Employee e

WHERE e.salary > 50000

AND e.name = 'Nandini'



Criteria:

Predicate p1 =

cb.gt(root.get("salary"), 50000);



Predicate p2 =

cb.equal(root.get("name"), "Nandini");



query.where(

cb.and(p1, p2)

);



OR Condition

HQL:

FROM Employee e

WHERE e.name='Nandini'

OR e.name='Ravi'



Criteria:

query.where(

cb.or(

cb.equal(root.get("name"), "Nandini"),

cb.equal(root.get("name"), "Ravi")

)

);



ORDER BY

HQL:

FROM Employee e

ORDER BY e.salary DESC



Criteria:

query.orderBy(

cb.desc(root.get("salary"))

);



Ascending:

query.orderBy(

cb.asc(root.get("salary"))

);



COUNT Query

HQL:

SELECT COUNT(e)

FROM Employee e



Criteria:

CriteriaQuery<Long> query =

cb.createQuery(Long.class);



Root<Employee> root =

query.from(Employee.class);



query.select(

cb.count(root)

);



Join Example

Suppose:

Employee

|

Department



Entity:

@ManyToOne

private Department department;



HQL:

SELECT e

FROM Employee e

JOIN e.department d

WHERE d.name='IT'



Criteria:

Join<Employee, Department> dept =

root.join("department");



query.where(

cb.equal(

dept.get("name"),

"IT"

)

);



Dynamic Query Example (Real Interview Scenario)

Search screen:

Name Optional

Salary Optional



Build conditions dynamically.

List<Predicate> predicates =

new ArrayList<>();



If name present:

predicates.add(

cb.equal(

root.get("name"),

name

)

);



If salary present:

predicates.add(

cb.gt(

root.get("salary"),

salary

)

);



Apply:

query.where(

predicates.toArray(

new Predicate[0])

);

This is the biggest reason Criteria API exists.



Criteria API vs HQL

HQL

FROM Employee e

WHERE e.salary > 50000

Pros:

Easy to read
Short
Good for fixed queries



Criteria API

query.where(

cb.gt(

root.get("salary"),

50000

)

);

Pros:

Dynamic query building
Type-safe
Excellent for search screens



What is Criteria API?

A programmatic, object-oriented way to create database queries without writing HQL or SQL strings.



Main Classes?

CriteriaBuilder

CriteriaQuery

Root

Predicate

Memorize these four.



When Should You Use Criteria API?

When queries are dynamic and conditions are added at runtime.



HQL vs Criteria API?

HQL

String Based

Easy To Read

Best For Static Queries



Criteria

Object Based

Dynamic

Best For Search Filters



What You Should Remember

CriteriaBuilder cb

Creates criteria components.



CriteriaQuery<T>

Represents a query.



Root<T>

Represents the entity.



Predicate

Represents conditions.



Hibernate Architecture:

Application

|

v

Configuration

|

v

SessionFactory

|

v

Session

|

v

Transaction

|

v

Database

Let's go through each component.



1. Application

This is your Java application.



Example:

Employee emp = new Employee();

emp.setName("Nandini");



Your code wants to:

Save employee
Update employee
Fetch employee
Delete employee

But it cannot directly talk to the database.

So it uses Hibernate.



2. Configuration

Configuration contains database details.



Example:

.properties

Database URL

Username

Password

Dialect

Entity Classes



Traditionally loaded from:

hibernate.cfg.xml

Example:

<hibernate-configuration>

<session-factory>



<property name="hibernate.connection.url">

jdbc:mysql://localhost:3306/test

</property>



<property name="hibernate.connection.username">

root

</property>



</session-factory>

</hibernate-configuration>



Hibernate reads this information during startup.



3. SessionFactory

After reading configuration, Hibernate creates:

SessionFactory



Example:

SessionFactory sessionFactory =

new Configuration()

.configure()

.buildSessionFactory();



Responsibility

Creates Session objects.



SessionFactory

|

Creates

|

Session



Important Characteristics

Heavyweight object
Thread-safe
Created once per application



How many SessionFactory objects should exist?

Usually one per database per application.



4. Session

The most important Hibernate object.



Created using:

Session session =

sessionFactory.openSession();



Think of Session as:

Connection between Application and Hibernate

(technically not JDBC Connection, but close enough for understanding)



Uses of Session

session.save(emp);



session.get(Employee.class, 1L);



session.delete(emp);



session.merge(emp);

All CRUD operations happen through Session.



Characteristics

Lightweight

Not Thread Safe

Short Lived



Usually:

One Request

|

One Session



5. Transaction

Used to group database operations.

Example:

Transaction tx =

session.beginTransaction();



Why Transaction?

Suppose:

session.save(employee);

session.save(address);



If second operation fails:

Employee Saved ✅



Address Failed ❌

Database becomes inconsistent.

Transaction solves this.



Example

Transaction tx =

session.beginTransaction();



try {



session.save(employee);

session.save(address);



tx.commit();



} catch(Exception e) {



tx.rollback();

}



Outcome:

Both Saved

OR

Both Rolled Back



6. Database

Finally Hibernate generates SQL and talks to the database.

Example:

session.save(emp);



Hibernate generates:

INSERT INTO EMPLOYEE

(NAME)

VALUES

('Nandini');

Database stores the row.



Complete Flow Example

Imagine this code:

Employee emp = new Employee();

emp.setName("Nandini");



Session session =

sessionFactory.openSession();



Transaction tx =

session.beginTransaction();



session.save(emp);



tx.commit();



session.close();



Flow:

Application Creates Employee

|

v

SessionFactory

|

v

Session

|

v

Transaction

|

v

Hibernate

|

v

Database



Architecture in Real Life

Imagine a bank.

Configuration

Bank rules:

Opening Hours

Policies

Database Info



SessionFactory

Head Office.

Creates branches.



Session

Bank branch where actual work happens.

Deposit

Withdraw

Transfer



Transaction

The banking operation itself.

Transfer ₹1000

Must complete fully.



Database

Bank records.



Sometimes architecture diagrams also include:

Application

|

v

Session

|

+---- First Level Cache

|

+---- Transaction

|

+---- JDBC

|

+---- Database



JDBC

Hibernate internally uses JDBC.



You write:

session.get(Employee.class, 1L);



Hibernate eventually executes:

PreparedStatement

ResultSet

Connection

using JDBC behind the scenes.



First-Level Cache

Every Session contains:

First-Level Cache



Example:

Employee e1 =

session.get(Employee.class, 1L);



Employee e2 =

session.get(Employee.class, 1L);

Only one DB hit.

Second call comes from cache.



Complete Architecture Diagram

Application

|

v

Configuration

|

v

SessionFactory

|

------------------

| |

v v

Session 1 Session 2

|

-----------------

| |

v v

Cache Transaction

|

v

JDBC

|

v

Database



What are the main components of Hibernate Architecture?

Configuration
SessionFactory
Session
Transaction
Database



Which object is responsible for creating Sessions?

SessionFactory



Which object performs CRUD operations?

Session



Which object ensures data consistency?

Transaction



Is SessionFactory Thread Safe?

✅ Yes



Is Session Thread Safe?

❌ No



One-Line Summary

Configuration loads Hibernate settings.

SessionFactory creates Sessions.

Session performs CRUD operations.

Transaction manages commit/rollback.

Hibernate generates SQL and interacts with the database.



Transactions Additional Concepts:

ACID Properties:

A - Atomicity

Either everything succeeds or nothing succeeds.



Debit Account A

Credit Account B

Both happen or both fail.



In Hibernate:

try {

   tx.commit();

} catch (Exception e) {

   tx.rollback();

}



C - Consistency

Database remains in a valid state.

No broken data

No invalid references



A transaction must move the database from one valid state to another valid state.



Before transfer:

Total Money = 15000

After transfer:

Total Money = 15000



Database rules remain valid.

Examples:

Primary key constraints
Foreign key constraints
Unique constraints

must remain satisfied.



I - Isolation

Multiple transactions should not interfere with each other.



Isolation means:

One transaction should not interfere with another.



Example:

User A updating salary

User B updating salary

at the same time.

Isolation ensures controlled interaction between those transactions.

This is where Isolation Levels come into play (we'll cover them shortly).



D – Durability

Durability means:

Once committed, data is permanently stored.



Example: 

tx.commit();



After commit:

Power failure

Server restart

Application crash



Data should still be present.



Definition: ACID stands for Atomicity, Consistency, Isolation, and Durability. These properties ensure reliable and consistent transaction processing.



Transaction States

Active

Committed

Rolled Back



Example:

tx.begin();

↓

Active

↓

tx.commit();

↓

Committed



Declarative Transactions (Spring)

In modern projects, you rarely see:

session.beginTransaction();



Most Spring Boot projects use:

@Transactional

public void createEmployee() {

...

}



Spring starts and commits the transaction automatically.



Example:

@Service

public class EmployeeService {



   @Transactional

   public void createEmployee() {



       employeeRepository.save(emp);



       addressRepository.save(address);

   }

}



What Happens Internally?

Spring automatically:

Start Transaction

        ↓

Execute Method

        ↓

Commit Transaction


If an exception occurs:

Start Transaction

        ↓

Exception

        ↓

Rollback Transaction



Example:

@Transactional

public void transferMoney() {

    debitAccount();

    creditAccount();

}



If

creditAccount();

fails,

Spring automatically rolls back everything.


Why Is It Popular?

Without @Transactional:

beginTransaction();

commit();

rollback();



everywhere.

With @Transactional:

@Transactional

public void saveData() {

}

Much cleaner.



Where should @Transactional be placed?

Usually:

@Service

Layer



Example:

@Service

public class EmployeeService {

    @Transactional

    public void saveEmployee() {

    }

}



How do you manage transactions in Spring Boot?

@Transactional



What happens if an exception occurs inside a @Transactional method?

Spring rolls back the transaction.

Transaction Propagation (Advanced Spring Topic)

Examples:

REQUIRED

REQUIRES_NEW

SUPPORTS

MANDATORY

For Hibernate alone you don't need this yet.

This belongs more to Spring Transaction Management.



Isolation Levels

Another senior-level topic.

READ_UNCOMMITTED

READ_COMMITTED

REPEATABLE_READ

SERIALIZABLE

These control concurrency issues like:

Dirty Reads
Non-Repeatable Reads
Phantom Reads



Now let's understand why Isolation exists.

Suppose:

Employee Salary = 50000



Two transactions run simultaneously.

Without safeguards, strange things can happen.

Isolation levels control this behavior.



Interesting fact:

The Optimistic/Pessimistic Locking topic we just covered is closely related to transaction isolation and concurrency control.



Explain Transactions in Hibernate.

A transaction is a unit of work that ensures database operations are executed atomically. In Hibernate, transactions are managed using the Transaction interface. Changes can be committed using commit() or reverted using rollback(). Hibernate automatically performs a flush before commit. Transactions help maintain ACID properties and ensure data consistency.



Dirty Read

Scenario

Transaction A:

Salary = 50000

Updates:

Salary = 70000

But has NOT committed.



Transaction B reads:

70000

Then

Transaction A rolls back

Final salary:

50000



Transaction B saw data that never actually existed.

This is called:

Dirty Read



READ_UNCOMMITTED

Lowest isolation level.

Allows:

✅ Dirty Reads

Performance is high.

Data consistency is low.

Rarely used.



READ_COMMITTED

Most commonly used by many databases.

Transaction can only read:

Committed Data



Dirty Reads are prevented.

Example:

Transaction A updates

Transaction B cannot see updates

until A commits



Non-Repeatable Read

Example:

Transaction A:

Reads Salary = 50000



Transaction B:

Updates Salary = 60000

Commits



Transaction A reads again:

Salary = 60000



Same query, different result.

This is called:

Non-Repeatable Read



REPEATABLE_READ

Prevents:

✅ Dirty Reads

✅ Non-Repeatable Reads

Transaction sees the same row value during its lifetime.

Commonly used in MySQL.



Phantom Read

Transaction A:

SELECT *

FROM EMPLOYEE

WHERE salary > 50000

Returns:

10 rows



Transaction B inserts:

New Employee

Salary = 70000

And commits.



Transaction A executes the same query again:

11 rows

A new row has appeared.

This is called:

Phantom Read



SERIALIZABLE

Highest isolation level.

Prevents:

✅ Dirty Reads

✅ Non-Repeatable Reads

✅ Phantom Reads

But:

Slowest

Most Locking

Least Concurrency



Isolation Level Summary

READ_UNCOMMITTED

    |

    | Allows Dirty Reads

    v

READ_COMMITTED

    |

    | Prevents Dirty Reads

    v

REPEATABLE_READ

    |

    | Prevents Non-Repeatable Reads

    v

SERIALIZABLE

    |

    | Prevents Phantom Reads



Using Isolation with @Transactional

Example:

@Transactional(

    isolation = Isolation.READ_COMMITTED

)

public void updateEmployee() {

}



Or

@Transactional(

    isolation = Isolation.REPEATABLE_READ

)

public void updateEmployee() {

}



What does @Transactional do?

Creates and manages database transactions automatically.



Where is @Transactional usually used?

@Service

Methods.



What is a Dirty Read?

Reading uncommitted data from another transaction.



Which isolation level prevents Dirty Reads?

READ_COMMITTED

And above.



Which isolation level is the safest?

SERIALIZABLE



Quick Revision Sheet

ACID

-----

A → Atomicity

C → Consistency

I → Isolation

D → Durability

@Transactional

--------------

Starts transaction automatically

Commits automatically

Rolls back on exception

Isolation Levels

----------------

READ_UNCOMMITTED

READ_COMMITTED

REPEATABLE_READ

SERIALIZABLE



Spring Data JPA (Hibernate in Real Projects)

Why?

Because in modern Spring Boot applications, developers rarely write:



Session session = sessionFactory.openSession();

Transaction tx = session.beginTransaction();

session.save(emp);



Instead, they write:

employeeRepository.save(emp);

and Hibernate works behind the scenes.



Where Does Hibernate Fit?

A common confusion is:

Spring Data JPA

      ↓

JPA

      ↓

Hibernate

      ↓

Database



Think of it like this:

JPA

JPA is a specification (set of rules).

Like:

Interface



It defines:

@Entity

@OneToMany

@ManyToOne

And many other annotations.



Hibernate

Hibernate is an implementation of JPA.

Think:

interface Animal {

}

class Dog implements Animal {

}



Similarly,

JPA = Interface

Hibernate = Implementation



Spring Data JPA

Spring Data JPA sits on top and reduces boilerplate.



Without Spring Data JPA

Session session =

        sessionFactory.openSession();

Employee emp =

        session.get(Employee.class, 1L);



With Spring Data JPA

employeeRepository.findById(1L);



much simpler.



Repository Concept

Suppose you have entity:

@Entity

public class Employee {

    @Id

    private Long id;

    private String name;

}



Create repository:

public interface EmployeeRepository

       extends JpaRepository<Employee, Long> {

}



Done.

No implementation needed.

Spring creates it automatically.



What is JpaRepository?

JpaRepository<Entity, PrimaryKeyType>

Example:

JpaRepository<Employee, Long>

Means:

Entity      -> Employee

Primary Key -> Long



CRUD Automatically Available

After extending JpaRepository:

public interface EmployeeRepository

extends JpaRepository<Employee, Long> {

}



You automatically get:

save() 

findById()

findAll()

 delete()

 count()

No code required.



save()

Insert:

Employee emp = new Employee();



emp.setName("Nandini");



employeeRepository.save(emp);



Hibernate generates:

INSERT ...



findById()

employeeRepository.findById(1L);

Equivalent to:

session.get(Employee.class, 1L);



findAll()

employeeRepository.findAll();



Equivalent to:

FROM Employee



deleteById()

employeeRepository.deleteById(1L);

Equivalent to:

DELETE



Custom Query Methods

One of the coolest features.

Entity:

private String name;



Repository:

List<Employee>

findByName(String name);



No implementation!

Spring generates query automatically.



Usage:

employeeRepository

.findByName("Nandini");



Generated query:

SELECT *

FROM EMPLOYEE

WHERE NAME='Nandini'

More Examples

Find by salary:

List<Employee>

findBySalary(Double salary);



Find by name and salary:

List<Employee>

findByNameAndSalary(

String name,

Double salary);



Find by salary greater than:

List<Employee>

findBySalaryGreaterThan(

Double salary);

Spring generates everything.



@Query

For complex queries.

Example:

@Query(

"SELECT e FROM Employee e WHERE e.salary > :salary"

)

List<Employee> getEmployees(

@Param("salary")

Double salary);

This is basically HQL inside Spring Data JPA.



Transaction Management

Service layer:

@Service

public class EmployeeService {



@Transactional

public void saveEmployee() {



employeeRepository.save(emp);

}

}

Spring:

Start Transaction

↓

Execute Method

↓

Commit

automatically.



Difference Between Hibernate and JPA?

JPA

Specification

Hibernate

Implementation of JPA



Difference Between Hibernate and Spring Data JPA?

Hibernate

Provides ORM functionality.

Spring Data JPA

Provides repositories and reduces boilerplate using JPA/Hibernate underneath.



Architecture in Modern Projects

Controller

↓

Service

↓

Repository

↓

Spring Data JPA

↓

Hibernate

↓

Database

This is what you'll see in almost every Spring Boot application.



What is JpaRepository?

A Spring Data JPA interface that provides CRUD and pagination functionality for entities.



Do we write implementations for JpaRepository?

No.

Spring generates the implementation automatically.



What is the advantage of Spring Data JPA?

Less code
Automatic CRUD operations
Derived query methods
Seamless Hibernate integration



Hibernate vs Spring Data JPA?

Hibernate = ORM Framework

Spring Data JPA = Abstraction on top of JPA/Hibernate



EntityManager vs Session:

This is a very common Spring Boot + Hibernate interview question.

First Understand Why Both Exist

Earlier we learned:

Session session =

sessionFactory.openSession();



Hibernate uses:

Session

for database operations.



Later, when you work with Spring Boot, you'll often see:

@PersistenceContext

private EntityManager entityManager;

instead of Session.



Naturally the question becomes:

Why do we have both Session and EntityManager?



Hibernate Way

Hibernate provides:

Session

Example:

Session session =

sessionFactory.openSession();



Employee emp =

session.get(Employee.class, 1L);

Session is a Hibernate-specific API.

JPA Way

JPA provides:

EntityManager



Example:

Employee emp =

entityManager.find(Employee.class, 1L);

EntityManager is a JPA API.



Relationship Between Them

Think:

JPA

|

| Interface

|

v

EntityManager



Implemented by:

Hibernate

|

v

Session



A simple analogy:

List<String> list = new ArrayList<>();



Here:

List -> Interface

ArrayList -> Implementation

Similarly:

EntityManager -> JPA Interface



Session -> Hibernate Implementation



CRUD Comparison

Insert

Session

session.save(employee);

EntityManager

entityManager.persist(employee);



Read

Session

session.get(Employee.class, 1L);

EntityManager

entityManager.find(Employee.class, 1L);



Update

Session

session.update(employee);

EntityManager

entityManager.merge(employee);



Delete

Session

session.delete(employee);

EntityManager

entityManager.remove(employee);



Why Do Spring Boot Projects Prefer EntityManager?

Suppose your project uses Hibernate today.

Tomorrow company decides to use another JPA implementation.

If your code is:

Session session;

you're tightly coupled to Hibernate.



If your code is:

EntityManager entityManager;

you're coding against the JPA standard.



This is why most enterprise projects prefer:

EntityManager

or

JpaRepository

instead of using Session directly.



Can We Get Session From EntityManager?

Yes.

Sometimes Hibernate-specific features are needed.

Session session =

entityManager.unwrap(Session.class);

Now you can use Hibernate APIs.



Which should you use in Spring Boot?

Preferred answer:

Use EntityManager or Spring Data JPA because they follow the JPA specification and keep the application implementation-independent.



Now the Bigger Concept: Persistence Context

This is one of the most important Hibernate/JPA concepts.

Many people use Hibernate for years without understanding it.



What is Persistence Context?

Simple definition:

Persistence Context is a container that manages entity objects and tracks their changes.



Think of it as:

Hibernate Memory Area

where entities are stored and monitored.



Example

Employee emp =

entityManager.find(Employee.class, 1L);



After fetching:

Database

|

v

Persistence Context

|

v

Employee Object

The Employee entity is now managed.



Managed Entity

Earlier we learned:

Transient

Persistent

Detached

Removed



In JPA terminology:

Persistent Entity

=

Managed Entity

Same idea.



Example

Employee emp =

entityManager.find(Employee.class, 1L);



emp.setSalary(80000.0);

Notice:

entityManager.merge(emp);

is not called.

Still, the update happens.

Why?

Because the entity lives inside the Persistence Context.



Persistence Context + Dirty Checking

Hibernate remembers:

Original Salary = 50000



You change:

emp.setSalary(80000);



At commit time:

Original = 50000



Current = 80000

Difference found.

Hibernate generates:

UPDATE EMPLOYEE

SET SALARY = 80000

WHERE ID = 1;



This is Dirty Checking.

And Dirty Checking works because of the Persistence Context.



Persistence Context and First-Level Cache

Remember:

First-Level Cache

That cache is actually inside the Persistence Context.



Example:

Employee e1 =

entityManager.find(Employee.class, 1L);



Employee e2 =

entityManager.find(Employee.class, 1L);

Only one SQL query executes.

Why?

Because the entity already exists in the Persistence Context.



Visual Diagram

Database

|

v

Persistence Context

|

+--- Employee(1)

|

+--- Department(10)

|

+--- Employee(2)

Hibernate monitors all managed entities here.



Common Interview Question

What is Persistence Context?

Persistence Context is a collection of managed entities maintained by EntityManager. It tracks entity changes, provides first-level caching, and enables dirty checking.



Difference Between Session and EntityManager

Session

Hibernate Specific

Provides Hibernate APIs



Examples:

save()

update()

delete()



EntityManager

JPA Standard

Portable Across Implementations

Examples:

persist()

merge()

remove()

find()



Easy Memory Trick

Session

↓

Hibernate Language



EntityManager

↓

JPA Language

Same job.

Different abstraction levels.



What is EntityManager?

A JPA interface used to manage entities and perform database operations.

What is Session?

A Hibernate-specific API used for CRUD operations and persistence management.

Which one is preferred in Spring Boot?

EntityManager

or

JpaRepository

What is Persistence Context?

A container that stores and manages entity instances and tracks changes.

Why does Dirty Checking work?

Because managed entities are stored inside the Persistence Context.



JPQL vs HQL:



What is JPQL?

JPQL = Java Persistence Query Language

It is the query language defined by JPA.

Think:

JPA -> JPQL

Hibernate -> HQL



The Big Interview Answer

HQL and JPQL are very similar. JPQL is the standard query language defined by JPA, while HQL is Hibernate's implementation and extension of JPQL.



Why Do Both Exist?

Remember:

JPA = Specification



Hibernate = Implementation

Same concept applies to query languages.

JPA

|

└── JPQL



Hibernate

|

└── HQL



Example

Entity:

@Entity

public class Employee {



@Id

private Long id;



private String name;



private Double salary;

}

JPQL

SELECT e

FROM Employee e

WHERE e.salary > 50000

HQL

SELECT e

FROM Employee e

WHERE e.salary > 50000

Looks identical.

And most of the time it is.



What Do They Query?

Neither JPQL nor HQL works on tables.

Both work on:

Entities

Fields

Relationships



Example:

Database Table

EMPLOYEE

Entity

Employee



Query:

FROM Employee

Not:

FROM EMPLOYEE



Why Most Developers Don't Notice the Difference

Because 90% of queries are exactly the same.

Example:

FROM Employee

SELECT e FROM Employee e

DELETE FROM Employee e

UPDATE Employee e

All valid in both.



Actual Difference

Hibernate adds some extra features to HQL.

These are Hibernate-specific.

JPQL

Supports only JPA-standard features.

HQL

Supports:

JPQL Features

+

Hibernate-Specific Extensions



Interview Analogy

Think:

List<String> list = new ArrayList<>();

Here:

List = Standard Interface



ArrayList = Specific Implementation

Similarly:

JPQL = Standard

HQL = Hibernate Implementation



EntityManager vs Session Connection

Usually:

JPQL

Used with:

EntityManager



Example:

TypedQuery<Employee> query =

entityManager.createQuery(

"SELECT e FROM Employee e",

Employee.class);



HQL

Traditionally used with:

Session



Example:

Query<Employee> query =

session.createQuery(

"FROM Employee",

Employee.class);



JPQL Examples

Fetch All

SELECT e

FROM Employee e



Filter by Salary

SELECT e

FROM Employee e

WHERE e.salary > :salary



Join

SELECT e

FROM Employee e

JOIN e.department d

WHERE d.name='IT'



Notice:

e.department

We join through object relationships.

Not through foreign-key columns.



Why JPQL Is Preferred in Spring Boot

Spring Data JPA is built on JPA.

Example:

@Query("""

SELECT e

FROM Employee e

WHERE e.salary > :salary

""")

This is JPQL.

You'll see this everywhere in Spring Boot projects.



Is JPQL database dependent?

No.

JPQL works on entities, not database-specific tables.



Does JPQL support joins?

Yes.

Example:

SELECT e

FROM Employee e

JOIN e.department d



What does JPQL use?

Entity Names

Field Names

Relationship Names

Not:

Table Names

Column Names



SQL vs JPQL

SQL

SELECT *

FROM EMPLOYEE

WHERE SALARY > 50000



Uses:

Table

Column



JPQL

SELECT e

FROM Employee e

WHERE e.salary > 50000



Uses:

Entity

Field



JPQL vs HQL Interview Chart

JPQL

HQL

JPA Standard

Hibernate Query Language

Defined by JPA

Provided by Hibernate

Portable

Hibernate-specific

Works with EntityManager

Often used with Session

Uses entities and fields

Uses entities and fields

Most common in Spring Boot

Common in Hibernate applications



Difference between JPQL and HQL?"

JPQL is the standard query language defined by JPA. HQL is Hibernate's query language and is largely compatible with JPQL. HQL provides some Hibernate-specific extensions, while JPQL remains vendor-independent. Both operate on entities and entity attributes rather than database tables and columns.



Quick Revision

SQL

----

Table Names

Column Names



JPQL

-----

JPA Standard

Entity Names

Field Names



HQL

----

Hibernate Query Language

Mostly same as JPQL

Includes Hibernate-specific features



OrphanRemoval:

First, What Is an Orphan?

Suppose we have:

Department

|

+-- Employee A

+-- Employee B



Entity:

@Entity

public class Department {



@OneToMany(

mappedBy = "department"

)

private List<Employee> employees;

}



An employee belongs to a department.

Now imagine:

department.getEmployees().remove(employeeA);



After removal:

Department

|

+-- Employee B



What should happen to Employee A?

Two possibilities:

Option 1

Remove relationship only

Employee A still exists in DB.



Option 2

Delete Employee A completely

Employee A becomes an "orphan".

This is where orphanRemoval comes in.



What Is orphanRemoval?

@OneToMany(

mappedBy = "department",

orphanRemoval = true

)

private List<Employee> employees;



Meaning:

If a child entity is removed from the parent's collection, Hibernate should automatically delete it from the database.



Example Without orphanRemoval

Entity:

@OneToMany(mappedBy = "department")

private List<Employee> employees;

Database:

Department

-----------

IT



Employees

-----------

Nandini

Ravi



Remove employee:

department.getEmployees().remove(employee);



Commit:

tx.commit();



Typically Hibernate just breaks the relationship.

Example:

UPDATE EMPLOYEE

SET DEPT_ID = NULL

WHERE ID = 1;



Employee row still exists.



Example With orphanRemoval

@OneToMany(

mappedBy = "department",

orphanRemoval = true

)

private List<Employee> employees;



Now:

department.getEmployees().remove(employee);



Commit:

tx.commit();



Hibernate executes:

DELETE FROM EMPLOYEE

WHERE ID = 1;

Employee is completely removed.



Visual Understanding

Without orphanRemoval:

Department

|

+-- Employee A

Remove employee:

Department

Employee A still exists



With orphanRemoval:

Department

|

+-- Employee A

Remove employee:

Department 

Employee A deleted



CascadeType.REMOVE vs orphanRemoval

This is the interview question.

Many developers answer incorrectly.



CascadeType.REMOVE

Parent deleted.

Children deleted automatically.



Example:

session.remove(department);

Then:

Department deleted



Employee A deleted

Employee B deleted

Why?

Because parent was deleted.



orphanRemoval

Parent still exists.

One child removed from collection.

Example:

department.getEmployees().remove(employeeA);

Then:

Department still exists

Employee A deleted

Why?

Because child became orphaned.



Easy Way to Remember

CascadeType.REMOVE

Delete Parent

↓

Delete Children



orphanRemoval

Remove Child From Collection

↓

Delete Child



Practical Example

@Entity

public class Department {



@OneToMany(

mappedBy = "department",

cascade = CascadeType.ALL,

orphanRemoval = true

)

private List<Employee> employees;

}



Now:

Delete Department

session.remove(department);



Result:

Department deleted

Employees deleted



because of:

CascadeType.REMOVE



Remove Employee From Department

department.getEmployees().remove(emp);

Result:

Employee deleted



because of:

orphanRemoval = true

Real-Life Analogy

Think of a school and students.



Cascade REMOVE

Delete School

↓

Delete all student records



orphanRemoval

Student removed from school roster

↓

Student record deleted

Even though the school still exists.



What is orphanRemoval?

orphanRemoval=true automatically deletes a child entity when it is removed from its parent's relationship collection.



Is orphanRemoval the same as CascadeType.REMOVE?

No.

CascadeType.REMOVE acts when the parent is deleted.

orphanRemoval acts when a child is removed from the parent's collection.



When is orphanRemoval useful?

When child entities should not exist independently of the parent.

Examples:

Order -> OrderItems

Invoice -> InvoiceLines

Department -> Employees (sometimes)



Quick Revision

@OneToMany(

mappedBy = "department",

cascade = CascadeType.ALL,

orphanRemoval = true

)

private List<Employee> employees;



CascadeType.REMOVE

Delete Parent

↓

Delete Children



orphanRemoval

Remove Child From Collection

↓

Delete Child



Pagination and Sorting:

These are used in almost every real-world application.

Think about Amazon, Flipkart, banking transactions, logs, employees, orders, etc.



Imagine a table with:

10,00,000 Employees

Will you load all employees at once?

employeeRepository.findAll();

❌ Terrible idea.



Instead:

Show 20 records

Next Page

Next Page

Next Page

That's Pagination.



What is Pagination?

Pagination means:

Fetching data in smaller chunks (pages) instead of loading everything at once.

Example:

Total Employees = 100



Page Size:

10

Pages:

Page 1 -> 1-10

Page 2 -> 11-20

Page 3 -> 21-30

...



Why Pagination?

Without pagination:

List<Employee> employees =

employeeRepository.findAll();



Suppose:

1 Million Records

Problems:

High memory usage
Increased response time
Poor performance



Spring Data JPA Pagination

Spring provides:

Pageable

and

Page



Repository

public interface EmployeeRepository

extends JpaRepository<Employee, Long> {

}

No extra code required.



Fetch First Page

Pageable pageable =

PageRequest.of(0, 10);



Meaning:

Page Number = 0

Page Size = 10



Then:

Page<Employee> employees =

employeeRepository.findAll(pageable);



Important

Spring page numbering starts from:

0

not

1



Example:

Page 0 = First Page

Page 1 = Second Page

Page 2 = Third Page



Understanding PageRequest

PageRequest.of(pageNumber, pageSize)



Example:

PageRequest.of(0, 20)

means:

First Page

20 Records



Page Object

Return type:

Page<Employee>

contains more than just records.



Get Records

List<Employee> employees =

page.getContent();



Total Records

page.getTotalElements();



Example:

1000



Total Pages

page.getTotalPages();

Example:

50



Current Page

page.getNumber();



Is Last Page?

page.isLast();



Example

Page<Employee> page =

employeeRepository.findAll(

PageRequest.of(0, 5)

);



System.out.println(

page.getTotalElements());



System.out.println(

page.getTotalPages());



System.out.println(

page.getContent());



Sorting

Suppose employees are:

Ravi 50000

Nandini 90000

John 70000



You want:

Highest Salary First

or

Name Ascending

That's Sorting.



Simple Sorting

Sort sort =

Sort.by("salary");



Usage:

employeeRepository.findAll(sort);



Result:

50000

70000

90000

Ascending by default.



Descending Sort

Sort sort =

Sort.by("salary")

.descending();

Result:

90000

70000

50000



Sort By Name

Sort.by("name")

Result:

John

Nandini..

Multiple Sorting

Suppose:

Sort by Department

Then Salary



Sort sort =

Sort.by("department")

.ascending()

.and(

Sort.by("salary")

.descending()

);



Pagination + Sorting Together

Very common in projects.

Pageable pageable =

PageRequest.of(

0,

10,

Sort.by("salary")

.descending()

);



Then:

Page<Employee> page =

employeeRepository.findAll(pageable);



Meaning:

First Page

10 Records

Salary Descending



Custom Query with Pagination

Repository:

Page<Employee>

findBySalaryGreaterThan(

Double salary,

Pageable pageable);



Usage:

Page<Employee> page =

employeeRepository

.findBySalaryGreaterThan(

50000.0,

PageRequest.of(0, 10)

);



SQL Generated

Pagination:

PageRequest.of(0,10)



may generate:

SELECT *

FROM EMPLOYEE

LIMIT 10 OFFSET 0



Second page:

PageRequest.of(1,10)

becomes:

LIMIT 10 OFFSET 10



What is Pageable?

An interface used to define pagination information such as page number, page size, and sorting.



Difference Between Page and Pageable?

Pageable

Input

Pageable pageable

Specifies:

Page Number

Page Size

Sort



Page

Output

Page<Employee>



Contains:

Data

Total Pages

Total Elements

Current Page



Does Pagination Improve Performance?

✅ Yes

Because only a subset of records is fetched.

What Does PageRequest.of(0,10) Mean?

First Page

10 Records Per Page



How to Sort Descending?

Sort.by("salary")

.descending();



Real Interview Scenario

Question:

There are 5 million transaction records. How would you display them in a UI?



Good answer:

I would use Spring Data JPA pagination with Pageable and fetch records page by page instead of loading all records. I would also combine it with sorting and proper indexing on frequently searched columns.



Quick Revision

Pagination

PageRequest.of(0, 10)



Page Number = 0

Page Size = 10



Get Page

Page<Employee> page =

repository.findAll(pageable);



Sort Ascending

Sort.by("salary")



Sort Descending

Sort.by("salary")

.descending()



Pagination + Sort

PageRequest.of(

0,

10,

Sort.by("salary").descending()

)



Batch Processing:

Batch Processing is where interviewers start checking whether you've worked on production systems with large amounts of data.

First: What Problem Does Batch Processing Solve?

Suppose you need to insert:

10 Employees

Normal Hibernate is fine.



But what if you need to insert:

1,000,000 Employees

Would this be good?



for(Employee emp : employees) {

session.save(emp);

}

Not really.



Problems:

Huge memory consumption
Slow execution
Session cache keeps growing
Possible OutOfMemoryError

This is where Batch Processing comes in.



What is Batch Processing?

Instead of sending one SQL statement at a time:

INSERT Employee 1

INSERT Employee 2

INSERT Employee 3

...

Hibernate groups operations into batches.



Example:

Batch 1 -> 100 records 

Batch 2 -> 100 records

Batch 3 -> 100 records

This significantly reduces database round trips.



Without Batch Processing

for(Employee emp : employees) {

session.save(emp);

}

For 1000 employees:

1000 INSERT statements

1000 DB interactions



With Batch Processing

Hibernate can group them:

Batch 1 -> 50 records 

Batch 2 -> 50 records

Batch 3 -> 50 records

Much more efficient.



Hibernate Batch Configuration

Common setting:

.properties

hibernate.jdbc.batch_size=50



Meaning:

Process SQL statements in groups of 50



Example

for(int i=1; i<=1000; i++) {



Employee emp = new Employee();

emp.setName("Emp-" + i);



session.save(emp);

}



With:

.properties

hibernate.jdbc.batch_size=50



Hibernate can send operations in batches instead of one-by-one.



Another Problem: First-Level Cache Growth

Remember:

Session

|

First-Level Cache



Every saved entity remains in the Session.

Example:

for(int i=1; i<=100000; i++) {

session.save(emp);

}

Now Session contains:

100000 managed entities

Huge memory consumption.



Solution: flush() and clear()

Real-world batch code:

for(int i=1; i<=100000; i++) {



session.save(emp);



if(i % 50 == 0) {



session.flush();

session.clear();

}

}



flush()

session.flush();

Forces pending SQL statements to execute.



clear()

session.clear();

Removes entities from the Persistence Context.

This frees memory.



Visual

Without clear():

Session

Employee1

Employee2

Employee3

...

Employee100000

Huge memory.



With clear():

50 saved

flush

clear



next 50 saved

flush

clear

Memory remains stable.



Batch Updates

Suppose:

500,000 Employees

Need salary increase.



Bad approach:

List<Employee> employees =

session.createQuery(

"FROM Employee")

.list();



for(Employee emp : employees) {



emp.setSalary(

emp.getSalary() + 1000

);

}

Loads everything into memory.



Better Approach

Use bulk update:

UPDATE Employee e

SET e.salary = e.salary + 1000



One query.

Much faster.



Batch Inserts vs Bulk Updates

Batch Insert

session.save(...)



many entities.



Bulk Update

UPDATE Employee ...



single query updates many rows.



Real-Life Example

Banking system:

5 million transactions

End-of-day settlement.



Bad idea:

Update one record at a time



Good idea:

Process in batches

to reduce:

memory
network traffic
database load



Performance Tuning Tip

Suppose interviewer asks:

How would you insert 10 lakh records using Hibernate?

Good answer:

1. Configure batch size.

2. Use flush() periodically.

3. Use clear() periodically.

4. Commit in reasonable chunks.

5. Avoid keeping all entities in memory.



What is Hibernate Batch Processing?

A technique that groups multiple database operations together to improve performance and reduce database round trips.



Why use flush() and clear()?

flush() sends pending SQL statements to the database.

clear() removes managed entities from the persistence context and prevents memory issues.



What happens if clear() is not used during large inserts?

The Session cache keeps growing and may consume excessive memory or cause OutOfMemoryError.



Which property enables batching?

.properties

hibernate.jdbc.batch_size=50



How would you update 1 million rows?

Prefer:

UPDATE Employee e ...

over loading every entity into memory.



Quick Revision

Enable Batching

.properties

hibernate.jdbc.batch_size=50



Large Inserts

session.save()

session.flush()

session.clear()



Why clear()?

Reduce Session memory usage



Best for Massive Updates

UPDATE Employee ...

instead of fetching all rows.



Hibernate Performance Tuning Best Practices:

Interviewers often ask:

"Your application is slow. How would you improve Hibernate performance?"

They're not looking for one answer. They're looking for a checklist.



1. Prefer LAZY Loading Over EAGER Loading

Bad

@OneToMany(fetch = FetchType.EAGER)

private List<Employee> employees;



Suppose:

Department

|

-> 5000 Employees



When you load Department:

departmentRepository.findById(1L);

Hibernate immediately loads all employees.

Huge performance impact.



Better

@OneToMany(fetch = FetchType.LAZY)

private List<Employee> employees;



Employees load only when needed.



Interview Answer

Use LAZY fetching by default and load associations only when required.



2. Solve N+1 Query Problems

One of the biggest Hibernate performance killers.

Bad

List<Department> departments =

repository.findAll();



for(Department d : departments) {



d.getEmployees().size();

}



Queries:

1 Query for Departments

+

N Queries for Employees



Example:

101 Queries 

for 100 departments.



Better

SELECT d

FROM Department d

JOIN FETCH d.employees



Now:

Single Query

loads everything.



Interview Answer

Detect N+1 queries using SQL logs and fix them using JOIN FETCH, Entity Graphs, or optimized queries.



3. Use Pagination

Bad

repository.findAll();



Imagine:

5 Million Employees

Everything loads into memory.

Better

Page<Employee> page =

repository.findAll(

PageRequest.of(0, 50)

);



Load only:

50 records

at a time.



Interview Answer

Never fetch large datasets completely. Use Pageable and PageRequest for pagination.



4. Use Batch Processing

For large inserts:

Bad

for(Employee e : employees) {



session.save(e);

}

Thousands of DB round trips.



Better

.properties

hibernate.jdbc.batch_size=50



And:

session.flush();

session.clear();

periodically.



Interview Answer

Configure Hibernate batch processing and clear the persistence context periodically during large bulk operations.



5. Avoid Unnecessary Select Statements

Example:

Employee emp = repository.findById(id);



repository.save(emp);

Sometimes developers fetch entities unnecessarily before making changes.



Instead:

UPDATE Employee

SET salary = salary + 1000

via bulk update if business logic allows.



6. Use Appropriate Indexes

Hibernate can't save you if the database is slow.



Suppose query:

findByAccountNumber(...)

and:

10 Million Rows



Without index:

Full Table Scan



With index:

Fast Lookup



Frequently Indexed Columns

Primary Keys

Foreign Keys

Account Number

Transaction Reference

Status

Created Date



Interview Answer

Frequently searched columns and foreign keys should be properly indexed.



7. Use First-Level Cache Effectively

Remember:

Session Cache

Example:

Employee e1 =

session.get(Employee.class, 1L);



Employee e2 =

session.get(Employee.class, 1L);

Only one database call.

Hibernate serves second request from cache.



8. Use Second-Level Cache Carefully

Suitable for:

Country Codes 

Currency Codes

States

Configuration Data 

Master Data



Data that rarely changes.



Bad candidates:

Frequently Updated Transactions

Live Balances

Real-Time Trading Data



Interview Answer

Use second-level cache only for frequently-read, rarely-changing data.



9. Fetch Only Required Columns

Bad

SELECT e

FROM Employee e

Loads entire entity.



Suppose UI needs:

Employee Name

Employee Email

only.



Use projection:

SELECT e.name,

e.email

FROM Employee e



Less memory.

Less network traffic.

Faster execution.



10. Avoid Excessive Cascade Types

Example:

cascade = CascadeType.ALL



Sometimes developers blindly add:

CascadeType.ALL

everywhere.

Then deleting one entity may trigger:

1000 Child Deletes

10000 Grandchild Deletes

unexpectedly.



Use cascade only when truly required.



11. Monitor Generated SQL

Enable SQL logs.

.properties

spring.jpa.show-sql=true



Or:

.properties

logging.level.org.hibernate.SQL=DEBUG



Review generated SQL regularly.

Many performance issues become obvious immediately.



12. Prefer Bulk Operations

Bad

for(Employee e : employees) {



e.setStatus("ACTIVE");

}

Loading thousands of entities.



Better

UPDATE Employee

SET status='ACTIVE'



Single query.

Huge improvement.



How would you optimize Hibernate performance?

A good answer:

I would use LAZY loading, avoid N+1 queries using JOIN FETCH, implement pagination for large datasets, enable batch processing for bulk operations, use appropriate indexes, leverage first and second-level caching where appropriate, prefer projections when full entities are not required, and monitor generated SQL to identify inefficiencies.



Top 10 Hibernate Performance Tips to Memorize

1. Prefer LAZY over EAGER

2. Fix N+1 queries

3. Use JOIN FETCH wisely

4. Implement pagination

5. Use batch processing

6. Use DB indexes

7. Leverage first-level cache

8. Use second-level cache selectively

9. Fetch only required columns

10. Use bulk updates instead of loading huge datasets



Spring Data JPA Integration is covered through:

JPA vs Hibernate
Spring Data JPA architecture
JpaRepository
CRUD methods (save, findById, findAll, deleteById)
Query methods (findByName, etc.)
@Query
@Transactional
Pagination & Sorting
How Spring Data JPA uses Hibernate underneath





A few important topics are still worth learning if your goal is Senior Java/Spring Boot interviews.

Must Learn Next (High Priority)

Hibernate & Spring Data JPA Interview Scenario Questions ⭐

Real interview discussions
Production issue troubleshooting
Performance questions
Spring Data JPA Specifications

Modern alternative to Criteria API
Dynamic search filters
Frequently used in enterprise applications
Entity Graphs

Alternative to JOIN FETCH
Used to solve N+1 issues elegantly
Common senior-level topic
Auditing

@CreatedDate
@LastModifiedDate
@CreatedBy
@LastModifiedBy
Inheritance Mapping

@Inheritance
SINGLE_TABLE
JOINED
TABLE_PER_CLASS
Composite Primary Keys

@EmbeddedId
@IdClass
Common in banking applications



Good to Know (Medium Priority)

Native Queries

@Query(nativeQuery = true)
When JPQL is insufficient
Stored Procedures

Calling DB procedures from JPA/Hibernate
Entity Listeners

@PrePersist
@PostPersist
@PreUpdate
@PreRemove
Soft Delete

@SQLDelete
@Where
Widely used in real projects



For Interviews Only (Not Used Daily)

Hibernate Interceptors
Event Listeners
Multi-Tenancy
Custom Identifier Generators



For Your Current Level

If I were creating a roadmap for you, I'd do:

✅ Hibernate Core (Completed)

✅ Spring Data JPA (Completed)

➡ Hibernate & Spring Data JPA Interview Scenarios 

➡ Spring Data JPA Specifications

➡ Entity Graphs

➡ Auditing

➡ Inheritance Mapping

➡ Composite Keys

➡ Native Queries

After these, you'll be at a very solid Senior Java Developer Hibernate/JPA interview level.





➡ Hibernate & Spring Data JPA Interview Scenarios: 



A typical interviewer won't ask:

"What is @Entity?"

They'll ask:

"Your application is slow. How will you troubleshoot Hibernate?"

or

"Why are you getting LazyInitializationException?"

or

"Why do users lose data when updating the same record?"

These are scenario-based questions.



Scenario 1: LazyInitializationException

Interviewer

You are getting:

LazyInitializationException

What could be the reason?

Code

Department dept =

repository.findById(1L).get();



session.close();



dept.getEmployees().size();

Answer

employees is configured as:

@OneToMany(fetch = FetchType.LAZY)



Hibernate tries to fetch employees only when:

dept.getEmployees()

is called.

But Session is already closed.

Therefore Hibernate cannot load the data and throws:

LazyInitializationException



Fixes

JOIN FETCH

or

@EntityGraph

or access the collection before session closes.



Scenario 2: Application is Very Slow

Interviewer

A page loads Departments and Employees and is extremely slow.

What would you check?

Answer

First suspect:

N+1 Query Problem

Example:

List<Department> departments =

repository.findAll();



for(Department d : departments) {



d.getEmployees().size();

}

Possible execution:

1 Query for Departments



100 Queries for Employees

Total:

101 Queries

Solution

Use:

JOIN FETCH

Example:

SELECT d

FROM Department d

JOIN FETCH d.employees



Scenario 3: Same Query Hitting DB Again and Again

Interviewer

You fetched the same Employee twice using the same Session. Does Hibernate hit DB twice?

Answer

No.

Because of:

First Level Cache

Example:

Employee e1 =

session.get(Employee.class, 1L);



Employee e2 =

session.get(Employee.class, 1L);

Only first call hits DB.

Second call comes from Session cache.



Scenario 4: Two Users Updating Same Record

Interviewer

User A and User B update the same Employee simultaneously.

How do you prevent one user's changes from overwriting another's?

Answer

Use:

@Version

Example:

@Version

private Integer version;



This enables:

Optimistic Locking

If another user modified the row first:

OptimisticLockException

is thrown.



Scenario 5: Need to Insert 1 Million Records

Interviewer

How would you insert 1 million records efficiently?

Bad Answer

for(...) {



session.save(obj);

}

Good Answer

Configure:

hibernate.jdbc.batch_size=50

And periodically:

session.flush();

session.clear();

This keeps memory usage under control.



Scenario 6: OutOfMemoryError During Data Load

Interviewer

A batch job loads lakhs of records and crashes with memory issues.

What might be wrong?

Answer

Persistence Context is growing continuously.

Developer forgot:

session.clear();

during batch processing.

All entities remain managed in memory.



Scenario 7: Which is Better? EAGER or LAZY?

Interviewer

What fetch type would you choose by default?

Answer

LAZY

Reason:

Only load data when required.

EAGER may fetch huge object graphs unnecessarily and degrade performance.



Scenario 8: save() vs persist()

Interviewer

Difference between:

session.save()

and

entityManager.persist()

Answer

persist() is the JPA standard.

save() is Hibernate-specific.



Modern Spring Boot applications usually prefer:

persist()

or

repository.save()



Scenario 9: merge() vs update()

Interviewer

You have a detached entity.

Which would you use?

Answer

Prefer:

merge()

because:

Safer

Handles managed instances properly

Avoids NonUniqueObjectException



Scenario 10: Hibernate Query Performance Issue

Interviewer

Query takes 20 seconds.

Where will you start troubleshooting?

Good Answer

Check:

Generated SQL

Indexes

N+1 Queries

Fetch Strategy

Pagination

Execution Plan



Many candidates answer:

Hibernate issue



Senior candidates check:

DB indexes first



Scenario 11: Why Use Pagination?

Interviewer

Why not simply call:

findAll()

?

Answer

Suppose:

5 Million Records

Loading all records:

High memory

Slow response

Network overhead

Instead:

PageRequest.of(0,50)

loads only required rows.



Scenario 12: Difference Between JPQL and SQL

Interviewer

Why use JPQL?

SQL

SELECT *

FROM EMPLOYEE

JPQL

SELECT e

FROM Employee e

JPQL works with:

Entities

Relationships

Fields

instead of:

Tables

Columns



Scenario 13: Difference Between EntityManager and Session

Interviewer

Which one would you use in Spring Boot?

Answer

EntityManager

Reason:

JPA Standard

Portable

Vendor Independent

Hibernate Session is implementation-specific.



Scenario 14: How Does Dirty Checking Work?

Interviewer

Why does this update DB?

Employee emp =

entityManager.find(Employee.class,1L);



emp.setSalary(90000);

commit();

No update call exists.

Answer

Because entity is managed inside:

Persistence Context

Hibernate compares old and new state during flush and automatically generates UPDATE SQL.



Scenario 15: CascadeType.REMOVE vs orphanRemoval

Interviewer

Difference?

Cascade REMOVE

Delete Parent

↓

Delete Children

orphanRemoval

Remove Child From Collection

↓

Delete Child



Top 10 Questions You Must Answer Instantly

What causes LazyInitializationException?
What is N+1 Query Problem?
How do you solve N+1 Query Problem?
LAZY vs EAGER?
First-Level vs Second-Level Cache?
Dirty Checking?
merge() vs update()?
@Version and Optimistic Locking?
CascadeType.REMOVE vs orphanRemoval?
EntityManager vs Session?

