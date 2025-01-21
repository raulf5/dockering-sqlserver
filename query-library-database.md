# Query Library Database

#### Example 1 - Selecting All Columns and Records from a Table

To display all the information of the books in the store, you can do:

```sql
  SELECT * FROM books;
```

where the star is a wildcard that represents all the columns in the given table. You can view the result in Fig. 2, where the output has been truncated for brevity:

![Figure 2 - Using a database and retrieving all records from a table](https://i.imgur.com/g2bPobX.png)

This approach, when used without restrictions or filters, may impact the query performance negatively. More often than not, it is wiser to specify the list of fields you are interested in.

#### Example 2 - Selecting One or More Fields from a Table

At this point, retrieving all fields from the **books** table may not make much sense since we are still not in a position to derive meaning from the **author\_id** and **publisher\_id** columns. Thus, if you wish to see the list of names and ISBNs, you can do:

````sql
    SELECT book_name, book_isbn FROM books;
```Or, alternatively:

```sql
    SELECT DISTINCT book_name, book_isbn FROM books;
````

While the first query returns all the book records and gives you a rough idea of the number of copies of each book, the second one will allow you to identify all the distinct book name and ISBN combinations, as illustrated in Fig. 3:

![Figure 3 - Using DISTINCT in a SELECT statement](https://i.imgur.com/KcxlK2C.png)

As you can see above, there are 10 different titles in the **books** table.

#### Example 3 - Creating Column and Table Aliases

Aliases are alternate names for tables and columns that come in handy when writing more complex queries or presenting the results, respectively.

To create an alias, we will use the `AS` keyword followed by its name. The query below shows an alias called **b** for the **books** table. This means that we can simply use **b** each time we need to refer to **books**. Also, instead of **book\_name** and **book\_isbn** we want the output to display _Title_ and _ISBN_ as headers instead (see Fig. 4):

```sql
    SELECT DISTINCT
    b.book_name AS Title,
    b.book_isbn AS ISBN
    FROM books AS b;
```

![Creating table and column aliases](https://i.imgur.com/ajZ0wAI.png)

In the above query, it is not strictly necessary to use the table alias as a prefix for the columns since we are using only one table. However, it is good practice to do so since it will be required as we include more tables in our queries.

While the benefits of this technique may not seem obvious at first, they will become evident as the query complexity grows.

#### Example 4 - Joining Tables: Part 1

There's at least a couple of good reasons why we use integer numbers as foreign keys to reference unique identifiers from other tables.

First, it allows us to avoid duplicate information and gives us more control over our data. From a data integrity perspective, it makes more sense to store the authors' actual names in **authors**, respectively, than doing the same in **books**.

Second, storing integers is - generally speaking - less expensive than a full string, in terms of disk usage. According to the MySQL official [docs](https://dev.mysql.com/doc/refman/8.0/en/storage-requirements.html), storing an integer requires only four bytes, as opposed to strings where **L + 1** bytes are needed (with **L** being the length of the string). Thus, referencing authors by their IDs (1, 2, 3, or 4) in **books** is preferred over duplicating the names that are already available in **authors**, as you can see in Fig. 5:

![Figure 5 - Using integers as foreign keys](https://i.imgur.com/4L5D6t0.png)

With that said, let us leverage the `JOIN` keyword to query the **books** and **authors** tables simultaneously and retrieve the list of titles with their respective authors, as observed in Fig. 6:

![Figure 6 - Using JOINs](https://i.imgur.com/odzxROV.png)

```sql
    SELECT DISTINCT
    b.book_name AS Title,
    a.author_name AS Author
    FROM books AS b JOIN authors AS a
    ON b.author_id = a.author_id;
```

Where:

* **a** and **b** are aliases for **authors** and **books**, respectively.
* **Title** and **Author** are aliases for **book\_name** and **author\_name**, respectively.
* `JOIN` (or its equivalent `INNER JOIN`) lets us manipulate **a** and **b** using basic set theory. This particular type of `JOIN` will return the records where the **author\_id** exists on both tables.
* Based on how they are presented in the query, **b** is the _left_ table and **a** is the _right_ one since the former appears first after the `FROM` keyword. This distinction is important when using other types of `JOIN`s (`LEFT JOIN` or `RIGHT JOIN`). Fig. 7 shows what to expect in these two cases.

![Figure 7 - LEFT and RIGHT JOINs](https://i.imgur.com/TyIczbz.png)

In short, `LEFT JOIN` and `RIGHT JOIN` return all records common to both sets, and those only present on the left and right tables, respectively.

#### Example 5 - Joining Tables: Part 2

It is important to note that you can join as many tables as necessary. For example, the output of the following query (as shown in Fig. 8) will indicate who borrowed which book from each librarian and when:

```sql
    SELECT
    c.customer_name AS Customer,
    b.book_name AS Title,
    l.loan_date AS Date,
    lb.librarian_name AS Librarian
    FROM customers c JOIN loans l
    ON c.customer_id = l.customer_id
    JOIN books b ON b.book_id = l.book_id
    JOIN librarians lb ON lb.librarian_id = l.librarian_id;
```

![Figure 8 - Joining multiple tables](https://i.imgur.com/ftcQsNE.png)

The above result allows us to identify a customer (James Benson) who has borrowed three copies of the same book (Pilgrim Souls) from the same librarian (Julia Roosevelt) on the same date. This means it might be time for an audit.

By now you should be comfortable using `JOIN`s and creating aliases. Otherwise, please review the previous examples before you proceed.

#### Example 6 - Filtering Results: Part 1

Now that we have spotted a customer who has borrowed multiple copies of the same book, wouldn't it be interesting to list all the books that have been loaned to him?

To accomplish that goal, we can append the `WHERE` clause to the above query and indicate a condition that we expect the result set to meet. In this case, we are interested in all the records where the customer name is James Benson (see Fig. 9):

![Figure 9 - Using the WHERE clause](https://i.imgur.com/KIJGc8m.png)

```sql
    SELECT
    c.customer_name AS Customer,
    b.book_name AS Title,
    l.loan_date AS Date,
    lb.librarian_name AS Librarian
    FROM customers c JOIN loans l
    ON c.customer_id = l.customer_id
    JOIN books b ON b.book_id = l.book_id
    JOIN librarians lb ON lb.librarian_id = l.librarian_id
    WHERE c.customer_name = 'James Benson';
```

If you are unsure about how the customer name is actually written but are certain it contains the letters **son** somewhere, you can use `LIKE` as follows:

```sql
    SELECT
    c.customer_name AS Customer,
    b.book_name AS Title,
    l.loan_date AS Date,
    lb.librarian_name AS Librarian
    FROM customers c JOIN loans l
    ON c.customer_id = l.customer_id
    JOIN books b ON b.book_id = l.book_id
    JOIN librarians lb ON lb.librarian_id = l.librarian_id
    WHERE c.customer_name LIKE '%son%';
```

The `LIKE` clause, when followed by the text enclosed within percent signs and single quotes, will return the records where the customer name contains **son** - regardless of its location in the string. If you want to restrict the results to the records where **son** is found **exactly** at the beginning or the end of the string, use `LIKE` followed by `'son%'` or `'%son'`, respectively.

#### Example 7 - Filtering Results: Part 2

You can add as many conditions and as much logic to the `WHERE` clause as needed. Suppose you want to list all copies of second editions by author Cay S. Horstmann:

```sql
    SELECT
    b.book_name AS Title,
    a.author_name AS Author
    FROM books AS b JOIN authors AS a
    ON b.author_id = a.author_id
    WHERE b.book_edition = 2
    AND a.author_name = 'Cay S. Horstmann';
```

As you can see, it is possible to build on top of previous conditions by using [boolean algebra](https://en.wikipedia.org/wiki/Boolean_algebra). In this example, we use `AND` to filter the results to second editions of the given author only.

Another query will help us illustrate this concept further:

```sql
    SELECT
    b.book_name AS Title,
    c.customer_name AS Customer,
    c.customer_address AS Address,
    l.loan_date AS Date
    FROM books AS b JOIN loans AS l
    ON b.book_id = l.book_id
    JOIN customers AS c ON c.customer_id = l.customer_id
    JOIN authors AS a ON b.author_id = a.author_id
    WHERE b.book_edition = 1
    AND (a.author_name = 'Cay S. Horstmann'
    OR a.author_name = 'Barbara Abercrombie');
```

![Figure 10 - Adding conditions and logic to filter results](https://i.imgur.com/42MH5Kl.png)

As observed in Fig. 10, the above query returns the list of customers who have borrowed first edition books of authors Cay S. Horstmann or Barbara Abercrombie. Note that the parentheses surrounding the author names are necessary to preserve the operator precedence.

#### Example 8 - Sorting Results

The previous query can be improved by sorting the results in ascending (default) or descending order on a given field very easily. To sort by descending loan date, all we need to do is append the `ORDER BY` clause as follows:

```sql
    SELECT
    b.book_name AS Title,
    c.customer_name AS Customer,
    c.customer_address AS Address,
    l.loan_date AS Date
    FROM books AS b JOIN loans AS l
    ON b.book_id = l.book_id
    JOIN customers AS c ON c.customer_id = l.customer_id
    JOIN authors AS a ON b.author_id = a.author_id
    WHERE b.book_edition = 1
    AND (a.author_name = 'Cay S. Horstmann'
    OR a.author_name = 'Barbara Abercrombie')
    ORDER BY l.loan_date DESC;
```

To sort in ascending order, remove the `DESC` keyword. Alternatively, you can add further sorting criteria using a comma-separated list of field names. For example,

```sql
    SELECT
    b.book_name AS Title,
    c.customer_name AS Customer,
    c.customer_address AS Address,
    l.loan_date AS Date
    FROM books AS b JOIN loans AS l
    ON b.book_id = l.book_id
    JOIN customers AS c ON c.customer_id = l.customer_id
    JOIN authors AS a ON b.author_id = a.author_id
    WHERE b.book_edition = 1
    AND (a.author_name = 'Cay S. Horstmann'
    OR a.author_name = 'Barbara Abercrombie')
    ORDER BY l.loan_date DESC, b.book_name DESC;
```

will produce essentially the same result as before but it will sort first by loan date and then by book names - both in descending order.

#### Example 9 - Grouping and Aggregating: Part 1

For reporting purposes, it may be necessary to count how many books have been borrowed by each customer. To achieve that objective we can use the `COUNT` aggregate function and the `GROUP BY` clause. When used in conjunction, they allow us to group by a given field and count the number of records in each group.

```sql
  SELECT COUNT(l.customer_id) AS Loans,
  c.customer_name AS Customer
  FROM loans AS l JOIN customers AS c
  ON l.customer_id = c.customer_id
  GROUP BY l.customer_id
  ORDER BY COUNT(l.customer_id) DESC;
```

Other aggregate functions included in the ANSI SQL standard are `MIN`, `MAX`, `AVG`, and `SUM`, often used with numeric fields.

As a rule of thumb, you can group by one (or more) of the fields to which an aggregate function has been applied.

#### Example 10 - Grouping and Aggregating: Part 2

In a previous example, we explained the use of `WHERE` to filter the results of a query. Unfortunately, it cannot be used on an aggregated field. Instead, you can narrow down the search using the `HAVING` clause followed by the aggregated field and the desired condition:

```sql
  SELECT COUNT(l.customer_id) AS Loans,
  c.customer_name AS Customer
  FROM loans AS l JOIN customers AS c
  ON l.customer_id = c.customer_id
  GROUP BY l.customer_id
  HAVING COUNT(l.customer_id) > 2
  ORDER BY COUNT(l.customer_id) DESC;
```

You can think of `HAVING` as the equivalent of `WHERE` for use on aggregate columns.

Note that the above query is almost the same as before, except we have plugged in the line

```sql
  HAVING COUNT(l.customer_id) > 2
```

after grouping by customer ID, to only return those clients who have borrowed more than two books. Strictly speaking, we are talking about the customers whose ID appears more than twice in the **loans** table.
