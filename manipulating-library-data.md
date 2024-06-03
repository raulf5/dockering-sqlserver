# Manipulating library data

### Introduction

Before you can aspire to make data-driven decisions, it is imperative that such data is stored somewhere and is accessible by you. Furthermore, you will want to ensure that it is updated when required or discarded when no longer needed.

This guide explains how to insert, update, and delete records from tables using SQL statements.&#x20;

In practice, data is inserted, updated, and deleted from tables using a wide variety of methods. These range from manual operations to external applications and everything in between. Regardless of the method, under the hood, it all boils down to SQL queries being executed either by a person or automatically in response to an event. For simplicity, we will use manual entry throughout this guide but the same statements apply in other scenarios.

### Inserting Data

The syntax for inserting a new record to a table is very straightforward:

```sql
  INSERT INTO table_name (field1, field2, ...) 
    VALUES (value1, value2, ...);
```

Where

* **field1** and **field2** are fields from **table\_name**.
* **value1** and **value2** are the values for **field1** and **field2**, respectively. SQL gives you the flexibility to list the fields in the order that you wish, as long as you specify the corresponding values accordingly. Thus, the following code is equivalent to the above query:

```sql
INSERT INTO table_name (field2, field1, ...) 
    VALUES (value2, value1, ...);
```

It is worth noting that the data type and length should match the table declaration. At best, boolean values (0 or 1) _may_ fit into a field declared as an integer but not necessarily the other way around. At worst, attempting to enter 100-character strings in VARCHAR(50) fields will simply end in an error or in data getting truncated. Thus, it is best to be as accurate as possible in the data type declaration and its use.

* The ellipsis (**...**) is not part of the SQL code. It indicates that other fields and their corresponding values may also be included in the statement.

A variation of **INSERT** allows several comma-separated records to be inserted at once as follows:

```sql
INSERT INTO table_name (field1, field2, ...) 
    VALUES (value3, value4, ...), (value5, value6, ...), (value7, value8, ...);
```

Fig. 1 illustrates this approach (commonly referred to as _bulk insert_), which was used to populate the **customers** table in the previous guide. Since **customer\_id** is an auto increment column, it is populated automatically upon each insert.

```sql
INSERT INTO customers (customer_name, customer_address) VALUES
('James Benson', '6649 N Blue Gum St'),
('Gladys Rim', '12270 Caton Center Dr'),
('Bette Stenseth', '81 Norris Ave #525'),
('Alicia Saylors', '03 N Radcliffe St'),
('Eric Bowley', '287 Youngstown Warren Rd'),
('Richard Emerson', '70 Euclid Ave #722'),
('Robert Lipke', '9939 N 14th St'),
('Bonnie Klusman', '6 Middlegate Rd #106'),
('Sue Kullmann', '8739 Hudson St'),
('Tim Murphy', '2140 Diamond Blvd'),
('Roger Dell', '3958 S Dupont Hwy #7'),
('Scott Butchers', '96263 Greenwood Pl'),
('Mary Maybury', '33 Lewis Rd #46'),
('Jill Devera', '60 Fillmore Ave'),
('Jack Lundren', '57254 Brickell Ave #372');
```

![Figure 1 - Inserting data](https://i.stack.imgur.com/TYI5pHn.png)

> Auto increment fields are better known as identity columns in SQL Server. They are declared using the **IDENTITY** property: **customer\_id INT IDENTITY(1,1)** where **(1,1)** indicates that it will start at 1 using steps of the same value.

Additionally, you can also insert rows to a table using the results of a **SELECT** query. For instance,

```sql
INSERT INTO table_name (field1, field2, ...) 
    SELECT fieldX, fieldY FROM other_table;
```

will insert all values from **fieldX** and **fieldY** in **other\_table** to **table\_name**. As you may well expect, the **SELECT** can be simple as above or complex, involving two or more tables.

To illustrate, let us add a new book called _Kicking In the Wall_ written by Barbara Abercrombie and published by Harper Collins to our library.

```sql
INSERT INTO books (book_name, book_isbn, book_edition, author_id, publisher_id) 
    SELECT 'Kicking in the wall' AS book_name, 
           '9781608681563' AS book_isbn, 
           1 AS book_edition, 
           a.author_id, 
           p.publisher_id 
    FROM authors AS a, publishers p 
        WHERE a.author_name = 'Barbara Abercrombie' AND 
              p.publisher_name = 'Harper Collins';
```

Fig. 2 shows the output of the **SELECT** on its own before performing the actual insertion:

![Figure 2 - Inserting rows through select](https://i.stack.imgur.com/nDtq9dL.png)

> The above example also highlights that you can select fixed values (**book\_name**, **book\_isbn**, and **book\_edition**) along with values from the database (**author\_id** and **publisher\_id**) in the same query.

Now that we have learned how to insert data to a table, let us explore how to update or delete the information that is already available in the database.

> A word of caution before you proceed. In the following two sections we will learn how to update and delete existing data. In any event, _be careful_ because you will be modifying the content of one or more tables. Depending on the number of affected rows, the only way to reverse the change may be restoring a recent backup!

### Updating Data

Whenever you need to change the value of certain fields in one or more rows, you will come across the **UPDATE** statement. In its most simple form, the syntax is the following:

```sql
UPDATE table_name SET field1 = X, field2 = Y WHERE field1 = Z;
```

Where **field1** and **field2** are two fields from **table\_name** whose values will be changed to **X** and **Y**, respectively - but only on the record where the current value of **field1** is **Z**.

> You can add as many **field = value** pairs as needed, as long as they are separated by commas.

If you omit the **WHERE** clause, those fields will be updated for all the records in the table. To minimize the likelihood of human error during this operation, you can follow a simple rule of thumb: do a **SELECT** first. If you utilize the same **WHERE** clause and it returns the expected row(s), you can go ahead with the **UPDATE**.

For example, let's say we need to correct customer Jill Devera's name to _Jack and Jill Devera_ and the address to _62 Fillmore Ave_. To begin, let us execute the query below:

```sql
SELECT * FROM customers WHERE customer_name = 'Jill Devera';
```

As we can see in Fig. 3, we can proceed to update the customer's name and address on file if we are sure it is the right record. To do so, let us begin by deleting everything to the left of the **WHERE** clause:

```sql
WHERE customer_name = 'Jill Devera';
```

Then add on top:

```sql
UPDATE customers SET customer_name = 'Jack and Jill Devera', customer_address = '62 Fillmore Ave' WHERE customer_name = 'Jill Devera';
```

![Figure 3 - Best practices for update queries](https://i.stack.imgur.com/o963lWM.png)

Fig. 3 also shows another **SELECT** after the **UPDATE** took place that confirms it was successful. Note that since the name was changed, we used **customer\_id** in the **WHERE** clause.

### Deleting Data

Before we attempt to delete any records, it is worthy and well to keep the same precautions as with the update operation. In short, we are only safe to proceed if the **SELECT** returns the correct row(s) when you apply the same filter condition.

Most of the scenarios where you might want to delete data are already familiar to you. Whenever you unsubscribe from an email newsletter, sell a product (either online or in-person), or donate office furniture, the **DELETE** statement is likely involved. In the first example, you request that your address be removed from a distribution list. The second and third scenarios assume that you keep track of the products you offer and the office assets under your care using a relational database (as you should!).

To delete the row(s) of **table\_name** where the current value of **field1** is **Z**, do as follows:

```sql
DELETE FROM table_name WHERE field1 = Z;
```

Sad news - our customer Tim Murphy has canceled his membership so now we need to remove his name from the **customers** table. First off, we need to make sure he has returned all the books he ever borrowed from the library:

```sql
SELECT customer_id FROM customers WHERE customer_name = 'Tim Murphy'; SELECT COUNT(customer_id) FROM loans  WHERE customer_id = 10;
```

Since the result is 0, as shown in Fig. 4, we can write the **DELETE** query with confidence using what we have just learned:

```sql
DELETE FROM customers WHERE customer_id = 10;
```

![Figure 4 - Deleting records](https://i.stack.imgur.com/ixQCsx7.png)

> Note that the **WHERE** clauses in the **SELECT** and **DELETE** queries are identical.

The fact that we checked the **loans** table before deleting the customer was not just to avoid losing books. Remember how the **customer\_id** foreign key in **loans** points to the same field in **customers** (where it is a primary key)? This is what we call a _constraint_ in SQL, and its purpose is to prevent data integrity issues, which is what would have occurred if we had a **customer\_id = 10** in **loans** but not in **customers**.
