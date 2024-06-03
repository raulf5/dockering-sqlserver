# Library script

```sql
-- Connect to the 'master' database to run this snippet
USE master
GO
-- Create the new database if it does not exist already
IF NOT EXISTS (
    SELECT [name]
        FROM sys.databases
        WHERE [name] = N'Library'
)
CREATE DATABASE Library
GO

USE library;

DROP TABLE IF EXISTS customers;
CREATE TABLE customers (
    customer_id INT IDENTITY(1,1),
    customer_name VARCHAR(50) NOT NULL,
    customer_address VARCHAR(100) NOT NULL,
    PRIMARY KEY (customer_id)
);

DROP TABLE IF EXISTS publishers;
CREATE TABLE publishers (
    publisher_id INT IDENTITY(1,1),
    publisher_name VARCHAR(50) NOT NULL,
    PRIMARY KEY (publisher_id)
);

DROP TABLE IF EXISTS authors;
CREATE TABLE authors (
    author_id INT IDENTITY(1,1),
    author_name VARCHAR(50) NOT NULL,
    PRIMARY KEY (author_id)
);

DROP TABLE IF EXISTS librarians;
CREATE TABLE librarians (
    librarian_id INT IDENTITY(1,1),
    librarian_name VARCHAR(50),
    PRIMARY KEY (librarian_id)
);

DROP TABLE IF EXISTS books;
CREATE TABLE books (
    book_id INT IDENTITY(1,1),
    book_name VARCHAR(50) NOT NULL,
    book_isbn VARCHAR(20) NOT NULL,
    book_edition INT NOT NULL,
    PRIMARY KEY (book_id),
    author_id INT,
    CONSTRAINT fk_author_id FOREIGN KEY (author_id) REFERENCES authors (author_id),
    publisher_id INT,
    CONSTRAINT fk_publisher_id FOREIGN KEY (publisher_id) REFERENCES publishers (publisher_id)
);

DROP TABLE IF EXISTS loans;
CREATE TABLE loans (
    loan_date DATETIME NOT NULL,
    loan_is_active CHAR(1) NOT NULL,
    customer_id INT,
    CONSTRAINT fk_customer_id FOREIGN KEY (customer_id) REFERENCES customers (customer_id),
    book_id INT,
    CONSTRAINT fk_book_id FOREIGN KEY (book_id) REFERENCES books (book_id),
    librarian_id INT,
    CONSTRAINT fk_librarian_id FOREIGN KEY (librarian_id) REFERENCES librarians (librarian_id)
);

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

INSERT INTO publishers (publisher_name) VALUES
('Harper Collins'),
('Simon & Schuster'),
('Wiley');

INSERT INTO authors (author_name) VALUES
('Cay S. Horstmann'),
('John Paul Mueller'),
('Mike Chapple'),
('Barbara Abercrombie');

INSERT INTO librarians (librarian_name) VALUES
('Julia Roosevelt'),
('Tom White'),
('Kate Bilden');

INSERT INTO books (book_name, book_isbn, book_edition, publisher_id, author_id) VALUES
('Pilgrim Souls', '9780684843117', 1, 1, 1),
('Pilgrim Souls', '9780684843117', 2, 1, 1),
('Python for Data Science', '9781119547662', 1, 2, 2),
('Python for Data Science', '9781119547662', 1, 2, 2),
('Python for Data Science', '9781119547662', 1, 2, 2),
('C# 7.0 All-in-One', '9781119428107', 1, 3, 3),
('C# 7.0 All-in-One', '9781119428107', 2, 3, 3),
('C# 7.0 All-in-One', '9781119428107', 3, 3, 3),
('C# 7.0 All-in-One', '9781119428107', 3, 3, 3),
('C# 7.0 All-in-One', '9781119428107', 3, 3, 3),
('Reality Check', '9780061227684', 1, 2, 4),
('Cultivating Delight', '9780060505363', 1, 1, 4),
('Cultivating Delight', '9780060505363', 1, 1, 4),
('Pearls of wisdom', '9780060962005', 1, 2, 4),
('Magnificent Machines', '9781471122477', 1, 1, 1),
('Magnificent Machines', '9781471122477', 1, 1, 1),
('Magnificent Machines', '9781471122477', 1, 1, 1),
('Magnificent Machines', '9781471122477', 1, 1, 1),
('CompTIA Complete Cybersecurity Study Guide', '9781119483663', 2, 2, 3),
('CompTIA Complete Cybersecurity Study Guide', '9781119483663', 2, 2, 3),
('Java For Everyone', '9781119186717', 1, 3, 4),
('Java For Everyone', '9781119186717', 1, 3, 4),
('Java For Everyone', '9781119186717', 1, 3, 4),
('Java For Everyone', '9781119186717', 1, 3, 4),
('The Energy Equation', '9781119638681', 1, 1, 1),
('The Energy Equation', '9781119638681', 1, 1, 1),
('The Energy Equation', '9781119638681', 2, 1, 1);

INSERT INTO loans (loan_date, loan_is_active, customer_id, book_id, librarian_id) VALUES
('2019-01-03 11:15:00', 1, 1, 2, 1),
('2019-01-03 11:15:00', 1, 1, 2, 1),
('2019-01-03 11:22:11', 1, 2, 2, 1),
('2019-01-03 11:34:34', 1, 1, 2, 1),
('2019-01-03 11:45:00', 1, 3, 2, 1),
('2019-01-03 11:45:00', 1, 3, 20, 1),
('2019-01-15 16:29:13', 1, 4, 1, 2),
('2019-01-15 16:29:13', 1, 4, 5, 2),
('2019-01-15 16:29:13', 1, 4, 15, 2),
('2019-01-15 16:32:13', 1, 5, 10, 2),
('2019-01-15 16:32:13', 1, 5, 21, 2),
('2019-01-15 16:40:13', 1, 1, 3, 2),
('2019-01-16 09:11:25', 1, 6, 4, 3),
('2019-01-16 09:13:25', 1, 7, 5, 3),
('2019-01-16 09:15:25', 1, 8, 6, 3),
('2019-01-17 10:16:25', 1, 2, 2, 2),
('2019-01-17 10:11:23', 1, 3, 2, 2),
('2019-01-17 10:11:43', 1, 5, 2, 2),
('2019-01-17 10:22:25', 1, 8, 6, 2),
('2019-01-17 10:22:25', 1, 8, 14, 2),
('2019-01-19 08:59:12', 1, 9, 17, 1);
```
