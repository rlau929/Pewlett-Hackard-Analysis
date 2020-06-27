# Pewlett-Hackard-Analysis

## ERD PNG File
![ERD Image Mapping Out Database](EmployeeDB.png)

## Challenge: Since there are so many csv files in the folder, below are the main CSV Files to review for Challenge Deliverable 1 and Deliverable 2.
1) new_mentorship.csv
2) new_retire.csv

These two files contain the final versions that includes partition and removing duplicates.

## Delivering Results
The purpose of this module is to use SQL to analyze data that have multiple csv files and creating new tables. Since the data is all over the place in multiple files, we can consolidate or manipulate the data in a specific way to analyze certain parts of the data.

This module was both easy and difficult. It was easy since we learned similar functions in Python. However, I did run into issues when I was using the partition function. For example, the code showed an error when I used this code:

```
-- Deliverable 1: Number of Retiring Employees by Title
SELECT e.emp_no,
	e.first_name,
	e.last_name,
	ti.title,
	sa.from_date,
	sa.salary
INTO silver_tsunami
FROM employees as e
INNER JOIN titles as ti
ON e.emp_no = ti.emp_no
INNER JOIN salaries as sa
ON e.emp_no = sa.emp_no
WHERE (e.birth_date BETWEEN '1952-01-01' AND '1955-12-31');
SELECT * FROM silver_tsunami
--TOP PORTION WORKS, BOTTOM PARTITION DOES NOT WORK
-- Partition the data to show only most recent title per employee
SELECT e.emp_no,
	e.first_name,
	e.last_name,
	ti.title,
	sa.from_date,
	sa.salary
INTO new_retire
FROM silver_tsunami
 (SELECT e.emp_no,
	e.first_name,
	e.last_name,
	ti.title,
	sa.from_date,
	sa.salary, ROW_NUMBER() OVER
 (PARTITION BY (emp_no)
 ORDER BY to_date DESC) rn
 FROM silver_tsunami
 ) 
tmp WHERE rn = 1
ORDER BY emp_no;
```
I've went to Slack to ask questions about my problem and found out that I was not referncing the table correctly. For example, rather than using "e.first_name", I removed the "e." and only use "first_name". The final code is below:

```
SELECT emp_no,
	first_name,
	last_name,
	title,
	from_date,
	salary
INTO new_retire
FROM
 (SELECT emp_no,
	first_name,
	last_name,
	title,
	from_date,
	salary, ROW_NUMBER() OVER
 (PARTITION BY (emp_no)
 ORDER BY from_date DESC) rn
 FROM silver_tsunami
 ) 
tmp WHERE rn = 1
ORDER BY emp_no;
SELECT * FROM new_retire
```

Finally, after completing Deliverable 1 and Deliverable 2, we can conclude that there are 90,398 employees who are planning to retire soon and 1,940 employees who are eligible for mentorship. There are a few limitations in the analysis because we did not analyze each department such as Marketing, Finance, Human Resources, and etc. If we include the department, we can separate the retirement data and see how many employees are leaving each department and if we need to fill in more spots for each department. We can use the COUNT function to calculate the total number of people retiring in each department. We can also analyze the mentorship eligibility of each departments and use the COUNT function to figure out how many mentors we will have in each department and weather we have enough or need to expand our criteria to include additional mentors.

The next step is to create two new tables and use INNER JOIN to include department data into the retirement data and mentorship data. Then, we can use the COUNT function to calculate how many people are retiring in each department and how many mentors are in each department.
