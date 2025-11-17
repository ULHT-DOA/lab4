# Lab 4 
## Topics: UML, Relational Model and SQL

## Learning Objectives

Upon completion of this laboratory exercise, students will be able to:

1. **Design UML Class Diagrams:**
   - Create class diagrams with proper attributes and data types
   - Define multiplicity and cardinality in relationships
   - Model association classes for many-to-many relationships
   - Apply composition and aggregation appropriately

2. **Transform UML to Relational Model:**
   - Map classes to relational tables
   - Define primary keys and foreign keys
   - Implement one-to-many and many-to-many relationships
   - Apply referential integrity constraints

3. **Write SQL Queries:**
   - Perform JOIN operations (INNER, LEFT, multi-table)
   - Use aggregate functions with GROUP BY and HAVING
   - Write subqueries for complex data retrieval
   - Create and utilize database views

4. **Understand Database Design Principles:**
   - Recognize the progression from conceptual to logical to physical design
   - Apply normalization concepts
   - Understand the role of associative entities

## Introduction

### Laboratory Context

In this laboratory session, you will work through the complete database design process, from conceptual modeling to query implementation. You will use a simplified **DOA Bookstore** domain to understand how object-oriented design concepts translate into relational database structures.

### Design Process Flow

The laboratory follows the standard database design methodology:

1. **Conceptual Design (UML):** Model the domain using UML class diagrams, focusing on entities, attributes, and relationships
2. **Logical Design (Relational Model):** Transform the UML diagram into a relational schema with tables, keys, and constraints
3. **Physical Implementation (SQL):** Implement the design in SQL and write queries to retrieve and manipulate data

### Domain Overview: DOA Bookstore

The DOA Bookstore system manages information about:
- **Authors** who write books
- **Books** available in the catalog
- **Orders** placed by customers
- **OrderItems** representing individual books within orders

**Key Business Rules:**
- Each book is written by exactly one author
- An author can write multiple books
- Each order can contain multiple books
- Each book can appear in multiple orders
- OrderItem serves as the link between orders and books, storing quantity and price at time of purchase

---

## Exercise 1: UML Class Diagram Design

### Goal

Create a comprehensive UML class diagram that models the DOA Bookstore domain with proper relationships, multiplicities, and attributes.

### Task 1.1: Define Classes and Attributes

Design UML classes for the following entities with their attributes:

**Class: Author**
- `id` : Integer
- `firstName` : String
- `lastName` : String
- `nationality` : String

**Class: Book**
- `id` : Integer
- `title` : String
- `price` : Decimal
- `genre` : String
- `authorId` : Integer

**Class: Order**
- `id` : Integer
- `orderDate` : Date
- `totalAmount` : Decimal

**Class: OrderItem**
- `id` : Integer
- `orderId` : Integer
- `bookId` : Integer
- `quantity` : Integer
- `bookPriceAtPurchase` : Decimal

### Task 1.2: Define Relationships

Model the following relationships in your UML diagram:

**1. Author to Book Relationship**
- Type: One-to-Many
- Multiplicity: One Author writes 0..* Books
- Navigation: Author → Book
- Representation: Association line with arrow pointing to Book
- Multiplicity labels: 
  - Near Author: `1`
  - Near Book: `0..*` or `*`

**2. Order to OrderItem Relationship**
- Type: Composition (strong ownership)
- Multiplicity: One Order contains 1..* OrderItems
- Navigation: Order → OrderItem
- Representation: Filled diamond on Order side
- Multiplicity labels:
  - Near Order: `1`
  - Near OrderItem: `1..*`
- Note: OrderItem cannot exist without an Order

**3. Book to OrderItem Relationship**
- Type: Association
- Multiplicity: One Book can appear in 0..* OrderItems
- Navigation: Bidirectional or OrderItem → Book
- Multiplicity labels:
  - Near Book: `1`
  - Near OrderItem: `0..*`

**4. Order to Book Relationship (through OrderItem)**
- Type: Many-to-Many (via association class)
- Association Class: OrderItem
- This is the key modeling challenge: OrderItem is both a class and an association

### Task 1.3: UML Diagram Structure

Your diagram must include:

**For each class:**
- Class name in bold at top of rectangle
- Attribute section with:
  - Visibility indicator (- for private, + for public)
  - Attribute name
  - Data type
  - Example: `- id : Integer`

**For relationships:**
- Clear association lines between classes
- Multiplicity indicators at both ends
- Association names where helpful (e.g., "writes", "contains")
- Composition diamonds where appropriate
- Dashed line connecting OrderItem to the Order-Book association

**Visual Layout Recommendations:**
- Author on the left
- Book in the center-left
- Order on the right
- OrderItem connecting Order and Book in the center
- Use clear spacing and alignment

### Expected Diagram Components Checklist

- [ ] Four classes with all attributes defined
- [ ] All attributes have data types specified
- [ ] Author → Book relationship with 1 to * multiplicity
- [ ] Order → OrderItem composition with filled diamond
- [ ] Book → OrderItem association
- [ ] OrderItem shown as association class between Order and Book
- [ ] All multiplicity labels present and correct
- [ ] Proper UML notation used throughout

### Deliverable

Create a UML class diagram using:
- Drawing tool (draw.io, Lucidchart, PlantUML, Visual Paradigm)
- Or hand-drawn diagram 


---

## Exercise 2: Relational Model Design

### Goal

Transform your UML class diagram into a relational database schema, defining tables, keys, data types, and constraints.

### Task 2.1: Define Table Structures

For each entity in your UML diagram, create a relational table definition. Specify:
- Table name
- Column names
- Data types
- Primary keys (PK)
- Foreign keys (FK)
- Constraints (NOT NULL, UNIQUE, etc.)

**Table Format Example:**
```
Table: TableName
┌─────────────────┬──────────────┬────────────┬─────────────────────────┐
│ Column Name     │ Data Type    │ Constraint │ Description             │
├─────────────────┼──────────────┼────────────┼─────────────────────────┤
│ id              │ INT          │ PK         │ Unique identifier       │
│ column_name     │ VARCHAR(100) │ NOT NULL   │ Description             │
│ foreign_id      │ INT          │ FK → Table │ References other table  │
└─────────────────┴──────────────┴────────────┴─────────────────────────┘
```

### Task 2.2: Create Authors Table

Define the `Authors` table:

**Requirements:**
- Primary key: `id`
- All name fields should be mandatory (NOT NULL)
- `firstName` and `lastName`: up to 100 characters each
- `nationality`: up to 50 characters

**Table Structure to Define:**
```
Table: Authors
┌──────────────┬──────────────┬────────────┬─────────────────────┐
│ Column       │ Data Type    │ Constraint │ Description         │
├──────────────┼──────────────┼────────────┼─────────────────────┤
│ ?            │ ?            │ ?          │ ?                   │
└──────────────┴──────────────┴────────────┴─────────────────────┘
```

### Task 2.3: Create Books Table

Define the `Books` table:

**Requirements:**
- Primary key: `id`
- Foreign key: `author_id` references `Authors(id)`
- `title`: mandatory, up to 200 characters
- `price`: decimal with 2 decimal places (e.g., DECIMAL(10,2))
- `genre`: up to 50 characters
- Referential integrity: ON DELETE RESTRICT (cannot delete author if books exist)


### Task 2.4: Create Orders Table

Define the `Orders` table:

**Requirements:**
- Primary key: `id`
- `order_date`: DATE type, mandatory
- `total_amount`: DECIMAL(10,2), mandatory
- Consider default value for `order_date` (current date)

### Task 2.5: Create OrderItems Table

Define the `OrderItems` table:

**Requirements:**
- Primary key: `id`
- Foreign key: `order_id` references `Orders(id)`
- Foreign key: `book_id` references `Books(id)`
- All fields mandatory (NOT NULL)
- `quantity`: positive integer (consider CHECK constraint)
- `book_price_at_purchase`: DECIMAL(10,2) - stores price at time of order
- Referential integrity: ON DELETE CASCADE for orders (if order deleted, items deleted too)
- Referential integrity: ON DELETE RESTRICT for books (cannot delete book in past orders)

**Special Considerations:**
- Why store `book_price_at_purchase` when we have `price` in Books table?
- What constraint ensures quantity is always positive?
- Why use CASCADE for orders but RESTRICT for books?

### Task 2.6: Relational Schema Diagram

Create a visual representation of your relational schema showing:

**For each table:**
- Table name
- All columns
- Data types
- Primary keys (underlined or marked with PK)
- Foreign keys (marked with FK)

**Between tables:**
- Lines connecting foreign keys to primary keys
- Cardinality indicators (1 or ∞)
- Relationship names if helpful

**Example Format:**
```
┌─────────────────┐       1        ∞      ┌─────────────────┐
│ Authors         │────────writes─────────>│ Books           │
├─────────────────┤                        ├─────────────────┤
│ PK: id          │                        │ PK: id          │
│    firstName    │                        │    title        │
│    lastName     │                        │    price        │
│    nationality  │                        │    genre        │
└─────────────────┘                        │ FK: author_id   │
                                           └─────────────────┘
```

### Deliverable

Create a document named `Relational_Schema_DOA_Bookstore.pdf` containing:
1. All four table definitions with complete specifications
2. Relational schema diagram showing relationships
3. Completed UML-to-Relational mapping table
4. The diagram image

---

## Exercise 3: SQL Query Practice 

### Goal

Implement the relational schema in SQL and write queries to demonstrate data retrieval, joins, aggregations, and views.

### Task 3.1: Data Insertion

Write SQL INSERT statements to populate your database with sample data:

**Requirements:**

**Authors (at least 2):**
- Jose Saramago (Portuguese)
- Machado de Assis (Brazilian)
- (Optional: add more)

**Books (at least 3):**
- "Ensaio sobre a Cegueira" by Saramago, €24.99, Novel
- "Memorial do Convento" by Saramago, €22.50, Historical Fiction
- "Dom Casmurro" by Machado de Assis, €19.99, Romance
- (Optional: add more)

**Orders (at least 2):**
- Order 1: Date 2024-11-01, will contain 2 books
- Order 2: Date 2024-11-15, will contain 2 books

**OrderItems (at least 3-5):**
- Link books to orders with realistic quantities
- Use book prices at purchase (can be same as current price)
- Calculate order totals correctly

**Note:** Ensure referential integrity - insert in correct order (Authors → Books → Orders → OrderItems)

---

### Task 3.2: Basic SELECT Queries

Write SQL queries for the following requirements:

#### Query 1: List All Books with Their Authors

**Requirement:** Display book title and author full name using INNER JOIN

**Expected Output Format:**
```
title                          | author_name
-------------------------------+------------------
Ensaio sobre a Cegueira        | Jose Saramago
Memorial do Convento           | Jose Saramago
Dom Casmurro                   | Machado de Assis
```

**Concepts Used:** INNER JOIN, String concatenation

---

#### Query 2: Order Item Counts

**Requirement:** Show each order ID with the total number of items (quantity sum) in that order

**Expected Output Format:**
```
order_id | total_items
---------+-------------
1        | 3
2        | 5
```

**Concepts Used:** GROUP BY, SUM aggregate function

---

#### Query 3: Most Expensive Book

**Requirement:** Find and display the single most expensive book with its title and price

**Expected Output Format:**
```
title                    | price
-------------------------+-------
Ensaio sobre a Cegueira  | 24.99
```

**Concepts Used:** ORDER BY, LIMIT (or MAX)

---

#### Query 4: Prolific Authors

**Requirement:** List authors who have written more than one book, showing author name and book count

**Expected Output Format:**
```
author_name      | book_count
-----------------+------------
Jose Saramago    | 2
```

**Concepts Used:** GROUP BY, COUNT, HAVING

---

### Task 3.3: Intermediate JOIN Queries

#### Query 5: Multi-Table JOIN - Complete Order Details

**Requirement:** Display order ID, order date, book title, and author name for all items in all orders

**Expected Output Format:**
```
order_id | order_date | book_title              | author_name
---------+------------+-------------------------+----------------
1        | 2024-11-01 | Ensaio sobre a Cegueira | Jose Saramago
1        | 2024-11-01 | Dom Casmurro            | Machado de Assis
2        | 2024-11-15 | Memorial do Convento    | Jose Saramago
```

**Concepts Used:** Multi-table JOIN (Orders → OrderItems → Books → Authors)

---

#### Query 6: LEFT JOIN - All Books Including Unsold

**Requirement:** List all books with their order count. Books never ordered should show 0 or NULL.

**Expected Output Format:**
```
book_title               | times_ordered
-------------------------+---------------
Ensaio sobre a Cegueira  | 2
Memorial do Convento     | 1
Dom Casmurro             | 1
A Cidade e as Serras     | NULL
```

**Concepts Used:** LEFT JOIN, COUNT with DISTINCT

**Note:** This requires a book that hasn't been ordered to demonstrate the LEFT JOIN properly.

---

#### Query 7: Subquery - Author of Most Expensive Book

**Requirement:** Use a subquery to find the author (first and last name) of the most expensive book

**Expected Output Format:**
```
author_name
-----------------
Jose Saramago
```

**Concepts Used:** Subquery, MAX function, JOIN

**Challenge:** Can you write this two ways - with and without JOIN?

---

#### Query 8: Revenue per Author

**Requirement:** Calculate total revenue generated by each author (sum of bookPriceAtPurchase × quantity for all their books)

**Expected Output Format:**
```
author_name      | total_revenue
-----------------+---------------
Jose Saramago    | 72.48
Machado de Assis | 39.98
```

**Concepts Used:** Multi-table JOIN, GROUP BY, SUM with multiplication

**Calculation:** Sum of (quantity × book_price_at_purchase) from OrderItems for each author's books

---

#### Query 9: Top-Selling Book

**Requirement:** Find the book that has been ordered the most times (by total quantity across all orders)

**Expected Output Format:**
```
book_title               | total_quantity_sold
-------------------------+--------------------
Dom Casmurro             | 5
```

**Concepts Used:** GROUP BY, SUM, ORDER BY, LIMIT

---

### Task 3.4: View Creation

Create database views for common queries that can be reused:

#### View 1: AuthorBooksView

**Requirement:** Create a view that shows each author with a list of their books

**View Structure:**
- `author_id`
- `author_name` (concatenated first and last name)
- `book_id`
- `book_title`
- `book_price`
- `genre`

**Expected Output When Querying View:**
```
author_name      | book_title              | book_price | genre
-----------------+-------------------------+------------+--------------------
Jose Saramago    | Ensaio sobre a Cegueira | 24.99      | Novel
Jose Saramago    | Memorial do Convento    | 22.50      | Historical Fiction
Machado de Assis | Dom Casmurro            | 19.99      | Romance
```

**Purpose:** Quickly see all books by each author without writing JOIN repeatedly

---

#### View 2: OrderSummaryView

**Requirement:** Create a view that summarizes each order

**View Structure:**
- `order_id`
- `order_date`
- `total_items` (sum of quantities)
- `total_amount` (sum of subtotals)
- `number_of_different_books` (count distinct books)

**Expected Output When Querying View:**
```
order_id | order_date | total_items | total_amount | different_books
---------+------------+-------------+--------------+-----------------
O001     | 2024-11-01 | 3           | 44.98        | 2
O002     | 2024-11-15 | 5           | 67.48        | 2
```

**Purpose:** Provide quick order overview without complex joins

---

#### View 3: BookSalesView

**Requirement:** Create a view showing sales statistics per book

**View Structure:**
- `book_id`
- `book_title`
- `author_name`
- `times_ordered` (count of orders containing this book)
- `total_quantity_sold` (sum of quantities across all orders)
- `total_revenue` (sum of quantity × price_at_purchase)

**Purpose:** Sales analysis and reporting


### Deliverable

Create a multiple files named `DOA_Bookstore_Queries_<operations: create | insert | select | view>.sql` containing:

1. **CREATE TABLE statements** for all four tables
2. **INSERT statements** with sample data
3. **All SELECT queries** numbered and commented:
   - Query 1: Books with Authors
   - Query 2: Order Item Counts
   - Query 3: Most Expensive Book
   - Query 4: Prolific Authors
   - Query 5: Complete Order Details
   - Query 6: All Books Including Unsold
   - Query 7: Author of Most Expensive Book
   - Query 8: Revenue per Author
   - Query 9: Top-Selling Book
4. **CREATE VIEW statements** for all views
5. **Sample SELECT statements** from each view to demonstrate their use


## Final Deliverables

Place all generated files in a single folder named Lab_YourName_StudentID and deliver it in moodle in a .zip file under Lab 4.

## Additional Resources

**UML Resources:**
- UML Class Diagram Tutorial: https://www.visual-paradigm.com/guide/uml-unified-modeling-language/uml-class-diagram-tutorial/
- UML Association vs Composition: https://www.uml-diagrams.org/association.html

**Database Design:**
- Database Normalization: https://en.wikipedia.org/wiki/Database_normalization
- Relational Model: https://en.wikipedia.org/wiki/Relational_model

**SQL Resources:**
- SQL Tutorial: https://www.w3schools.com/sql/
- PostgreSQL Documentation: https://www.postgresql.org/docs/
- MySQL Documentation: https://dev.mysql.com/doc/

**Tools:**
- UML: draw.io, Lucidchart, PlantUML
- Database: MySQL Workbench, pgAdmin, DBeaver
- SQL Practice: SQLite (lightweight for learning)


**Good luck with your database design!**
