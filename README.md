# SQL Project On Library Management-Part-1

This project showcases a complete Library Management System built using SQL. It covers structured table creation, data insertion, updates, deletions, and the use of CTAS (Create Table As Select) for generating derived tables. The main objective is to demonstrate solid understanding of relational database design, data handling, and effective SQL querying for real-world library operations.

## Project Structure

### 1. Database Setup
![ERD](https://github.com/SAI01-05/DataScience_SQL_LibraryManagementProject-3_Part-1/blob/main/ER_Diagram_For_LibraryManagementProject-Part1.png)

- **Database Creation**: Created a database named `Project3_Sql`.
- **Table Creation**: Created tables such as branch, employees, members, books, issued_status, and return_status. Each table includes relevant columns and relationships.

```sql
-- [TO CREATE DATABASE] --
CREATE DATABASE Project3_Sql;

-- [TO USE DATABASE] --
USE  Project3_Sql;

-- 			[CREATING TABLES]

CREATE TABLE branch
			(
				branch_id VARCHAR(10) PRIMARY KEY,
				manager_id VARCHAR(10),
				branch_address VARCHAR(30),
				contact_no VARCHAR(15)
			);

CREATE TABLE employees
			(
				emp_id VARCHAR(10) PRIMARY KEY,
				emp_name VARCHAR(30),
				position VARCHAR(30),
				salary INT,
				branch_id VARCHAR(10)  -- FK
			);

CREATE TABLE members
			(
				member_id VARCHAR(10) PRIMARY KEY,
				member_name VARCHAR(30),
				member_address VARCHAR(30),
				reg_date DATE
			);

CREATE TABLE books
			(
				isbn VARCHAR(20) PRIMARY KEY,
				book_title VARCHAR(80),
				category VARCHAR(30),
				rental_price FLOAT,
				status VARCHAR(10),
				author VARCHAR(30),
				publisher VARCHAR(30)
			);

CREATE TABLE issued_status
			(
				issued_id VARCHAR(10) PRIMARY KEY,
				issued_member_id VARCHAR(10),  -- FK
				issued_book_name VARCHAR(80),
				issued_date DATE,
				issued_book_isbn VARCHAR(20),  -- FK
				issued_emp_id VARCHAR(10)      -- FK
			);

CREATE TABLE return_status
			(
				return_id VARCHAR(10) PRIMARY KEY,
				issued_id VARCHAR(10),      -- FK
				return_book_name VARCHAR(80),
				return_date DATE,
				return_book_isbn VARCHAR(50)
			);

```
- **Displaying And Adding Foreign Key To Tables**
```sql

-- 			[DISPLAYING ALL TABLES]
SELECT * FROM branch;
SELECT * FROM employees;
SELECT * FROM books;
SELECT * FROM members;
SELECT * FROM issued_status;
SELECT * FROM return_status;

-- 			[ADDING FOREIGN KEYS TO THE TABLES ISSUED_STATUS]
ALTER TABLE issued_status
ADD CONSTRAINT fk_members
FOREIGN KEY (issued_member_id)
REFERENCES members(member_id);

ALTER TABLE issued_status
ADD CONSTRAINT fk_books
FOREIGN KEY (issued_book_isbn)
REFERENCES books(isbn);

ALTER TABLE issued_status
ADD CONSTRAINT fk_employees
FOREIGN KEY (issued_emp_id)
REFERENCES employees(emp_id);

-- 			[ADDING FOREIGN KEYS TO THE TABLES RETURN_STATUS]
ALTER TABLE return_status
ADD CONSTRAINT fk_issued_status
FOREIGN KEY (issued_id)
REFERENCES issued_status(issued_id);

-- 			[ADDING FOREIGN KEYS TO THE TABLES EMPLOYEES]
ALTER TABLE employees
ADD CONSTRAINT fk_branch
FOREIGN KEY (branch_id)
REFERENCES branch(branch_id);
```

### 2. CRUD Operations

**T1.Create a New Book Record ('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')**

```sql
INSERT INTO books(isbn,book_title,category,rental_price,status,author,publisher) 
VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
```

**T2.Update an Existing Member's Address**

```sql
UPDATE members
SET member_address='143 Main Road'
WHERE member_id='C101';
```

**T3.Delete a Record from the Issued Status Table**
-- Objective: Delete the record with issued_id = 'IS106' from the issued_status table.
-- Hint:Check the return_status table if there is ref to FK from issued_status table PK then we cannot delete.

```sql
DELETE FROM issued_status
WHERE
	issued_id='IS106';
```

**T4.Retrieve All Books Issued by a Specific Employee**
-- Objective: Select all books issued by the employee with emp_id = 'E101'.

```sql
SELECT issued_book_name  
FROM issued_status
WHERE issued_emp_id="E101";
```


**T5: List Members Who Have Issued More Than One Book**
-- Objective: Use GROUP BY to find members who have issued more than one book.

```sql
SELECT 
	issued_emp_id AS id,
    COUNT(*) AS total
FROM issued_status
GROUP BY 1
HAVING total >1;
```

### 3. CTAS (Create Table As Select)

- **T1.Create Summary Tables: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**

```sql
CREATE TABLE book_count_CTAS
AS
SELECT 
	B.isbn,
    B.book_title,
    COUNT(Ist.issued_id) AS no_issued
FROM books AS B
JOIN
issued_status AS Ist
ON
B.isbn=Ist.issued_book_isbn
GROUP BY 1,2;

SELECT * FROM book_count_ctas;
```

- **T2.Create a Table of Books with Rental Price Above a Certain Threshold**

```sql
CREATE TABLE Book_with_CertainPrice
AS
SELECT * 
	FROM books
WHERE 
	rental_price > 5.0;

SELECT * FROM Book_with_CertainPrice;
```


### 4. Data Analysis & Findings

The following SQL queries were used to address specific questions:

**T1.Retrieve All Books in a Specific Category**:
```sql
SELECT 
	book_title
FROM books 
WHERE category="Horror";
```

**T2.Find Total Rental Income by Category**:
```sql
SELECT
	B.category,
    SUM(B.rental_price) AS Total_Income 
FROM 
issued_status as ist
JOIN
books as B
ON B.isbn = ist.issued_book_isbn
GROUP BY 1 
ORDER BY 2 DESC;
```

**T3.List Members Who Registered in the Last 1180 Days**:
```sql
SELECT member_name
FROM 
	members
WHERE reg_date >= current_date() - INTERVAL 1180 DAY ;
```

**T4.List Employees with Their Branch Manager's Name and their branch details**:
```sql
SELECT 
	E1.*,
    B.*,
	E2.emp_name as manager
FROM employees AS E1
JOIN
branch AS B
ON E1.branch_id=B.branch_id
JOIN
employees as E2
ON E2.emp_id = B.manager_id;
```

**T5.Retrieve the List of Books Not Yet Returned**:
```sql
SELECT
	* FROM 
issued_status as ist
LEFT JOIN
return_status as rs
ON
rs.issued_id = ist.issued_id
WHERE 
	rs.return_id IS NULL;
```

## Implementation

- Table creation (DDL)
- Insertion queries (DML)
- Constraints (PK, FK)
- Select queries for testing

## Conclusion

This project provides a basic and well-structured Library Management System created using SQL. It shows how tables, relationships, and queries can be used to manage books, members, employees, and issuing/return processes. The system is simple, organized, and demonstrates important database concepts in an easy way.
