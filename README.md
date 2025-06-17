# Library Management System using SQL Project --P2

## Project Overview

**Project Title**: Library Management System  
**Database**: `library_db`

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.

![Library_project](https://github.com/najirh/Library-System-Management---P2/blob/main/library.jpg)

## Objectives

1. **Set up the Library Management System Database**: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. **CRUD Operations**: Perform Create, Read, Update, and Delete operations on the data.
3. **CTAS (Create Table As Select)**: Utilize CTAS to create new tables based on query results.
4. **Advanced SQL Queries**: Develop complex queries to analyze and retrieve specific data.

## Project Structure

### 1. Database Setup
![ERD](https://github.com/najirh/Library-System-Management---P2/blob/main/library_erd.png)

- **Database Creation**: Created a database named `library_db`.
- **Table Creation**: Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

```sql
create database library;
-- creating table named branch
 
 create table branch (
					branch_id	varchar(15) primary key,
                    manager_id	varchar(15),
                    branch_address	varchar(25),
                    contact_no	varchar(15)
                    );
select * from branch;
drop table branch;

 create table book (
					isbn		varchar(55) primary key,
                    book_title	varchar(55),
                    category	varchar(55),
                    rental_price	float,
                    status		varchar(10),
                    author		varchar(55),
                    publisher	varchar(55)
                    );
select * from book;
drop table book;

 create table member(
					member_id	varchar(15) primary key ,
                    member_name	varchar(25),
                    member_address	varchar(55),
                    reg_date		date
                    );
select * from member;
drop table member;

create table employee	 ( 	emp_id		varchar(15) primary key,
							emp_name	varchar(55),
                            position	varchar(25),
                            salary		float,
                            branch_id	varchar(15)
							);
select * from employee;
drop table employee;

create table return_book  ( return_id		varchar(15) primary key,
							issued_id		varchar(15),
                            return_book_name	varchar(15),
                            return_date			date,
                            return_book_isbn	varchar(15)
							);
select * from return_book;
drop table return_book;


 create table issued_stat ( issued_id			varchar(25) primary key,
							issued_member_id	varchar(25),
                            issued_book_name	varchar(55),
                            issued_date			date,
                            issued_book_isbn	varchar(55),
                            issued_emp_id		varchar(25)
							);
select * from issued_stat;
drop table issued_stat;

ALTER TABLE issued_stat
ADD CONSTRAINT fk_member_ID
FOREIGN KEY (issued_member_id) REFERENCES member(member_id);

ALTER TABLE issued_stat
ADD CONSTRAINT fk_book
FOREIGN KEY (issued_book_isbn) REFERENCES book(isbn);

ALTER TABLE issued_stat
ADD CONSTRAINT fk_employee
FOREIGN KEY (issued_emp_id) REFERENCES employee(emp_id);

ALTER TABLE employee
ADD CONSTRAINT fk_branch
FOREIGN KEY (branch_id) REFERENCES branch(branch_id);

```

### 2. CRUD Operations

- **Create**: Inserted sample records into the `books` table.
- **Read**: Retrieved and displayed data from various tables.
- **Update**: Updated records in the `employees` table.
- **Delete**: Removed records from the `members` table as needed.

**Task 1. Create a New Book Record**
-- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

```sql
insert into 	book (isbn,book_title,category,rental_price,status,author,publisher)
values			('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
select * from book;
```
**Task 2: Update an Existing Member's Address**

```sql
update		member
set			member_address ="koonammakkal house"
where		member_id = "C101";
select * from member;

```

**Task 3: Delete a Record from the Issued Status Table**
-- Objective: Delete the record with issued_id = 'IS121' from the issued_status table.

```sql
delete from		issued_stat
where			issued_id = 'IS104';

```

**Task 4: Retrieve All Books Issued by a Specific Employee**
-- Objective: Select all books issued by the employee with emp_id = 'E101'.
```sql
select		issued_emp_id,issued_book_name
from		issued_stat
where		issued_emp_id = 'E101';
```


**Task 5: List Members Who Have Issued More Than One Book**
-- Objective: Use GROUP BY to find members who have issued more than one book.

```sql
select		issued_member_id, count(issued_member_id) as number_of_members
from		issued_stat
group by 	issued_member_id
having		count(*) > 1
order by 	number_of_members desc;

```

### 3. CTAS (Create Table As Select)

- **Task 6: Create Summary Tables**: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**

```sql
create		table book_summery
as
select		bk.isbn,bk.book_title,
			count(iss.issued_id) as count
from		book as bk
left join
issued_stat as iss
on			bk.isbn = iss.issued_book_isbn
group by 	bk.isbn
order by 	count desc;	
select * from book_summery;		

```


### 4. Data Analysis & Findings

The following SQL queries were used to address specific questions:

Task 7. **Retrieve All Books in a Specific Category**:

```sql
select		book_title,category 
 from		book
 group by	book_title,category
 order by	category asc;
```

8. **Task 8: Find Total Rental Income by Category**:

```sql
select		category, sum(rental_price)as total_sum,
			count(*)  AS issued
from		book
group by	category
order by	issued asc
```

9. **List Members Who Registered in the Last 180 Days**:
```sql
select * from member;
select		member_name		
from 		member 
where		reg_date >= curdate() - interval 365 day;
```

10. **List Employees with Their Branch Manager's Name and their branch details**:

```sql
SELECT 
    e1.emp_id,
    e1.emp_name,
    e1.position,
    e1.salary,
    b.*,
    e2.emp_name as manager
FROM employees as e1
JOIN 
branch as b
ON e1.branch_id = b.branch_id    
JOIN
employees as e2
ON e2.emp_id = b.manager_id
```

Task 11. **Create a Table of Books with Rental Price Above a Certain Threshold**:
```sql
create table bookss
select		book_title
from		book
where 		rental_price >8;
```

Task 12: **Retrieve the List of Books Not Yet Returned**
```sql
select		*
from		issued_stat as iss
left join
return_book as ret
on			iss.issued_id=ret.issued_id
where		ret.return_id is null;
```

## Reports

- **Database Schema**: Detailed table structures and relationships.
- **Data Analysis**: Insights into book categories, employee salaries, member registration trends, and issued books.
- **Summary Reports**: Aggregated data on high-demand books and employee performance.

## Conclusion

This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.
