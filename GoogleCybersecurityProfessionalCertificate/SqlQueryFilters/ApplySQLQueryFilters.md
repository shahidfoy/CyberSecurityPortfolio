# Apply Filters to SQL Queries

## Project Description
Our organization is working on making our systems more secure. The following exercise will be used to 
investigate and identify potenial security issues. The following examples are SQL queries used to 
perform security-related tasks.

The Table formats.pdf file can be viewed to understand the tables being queried.

## Retrieve after hours failed login attempts
There was a potential security incident that occurred after business hours (after 18:00). All after
hours login attempts that failed need to be investigated.

The following query is used to filter for failed login attempts that occured after business hours.

```sql
SELECT *
FROM log_in_attempts
WHERE login_time > '18:00' AND success = FALSE;
```

This query will output information on log in attempts that were made after 18:00 and were not successful.

## Retrieve login attempts on specific dates
A suspicious event occurred on 2022-05-09. Any login activity that happened on 2022-05-09
or on the day before needs to be investigated.

The following query is used to filter login attempts that occured on the specified dates.

```sql
SELECT *
FROM log_in_attempts
WHERE login_date = '2022-05-09' OR login_date = '2022-05-08';
```

This query returns all login attempts that occured on 2022-05-09 or 2022-05-08.

## Retrieve login attempts outside of Mexico
After investigating the organization’s data on login attempts, There is an issue with the
login attempts that occurred outside of Mexico. These login attempts should be investigated.

The following query is used to filter login attempts that occurred outside of Mexico.

```sql
SELECT *
FROM log_in_attempts
WHERE NOT country LIKE 'MEX%';
```

This query returns all login attempts outside of Mexico the where clause uses
`LIKE 'MEX%'` as the pattern to match records can come as either 'MEX' or 'MEXICO'

## Retrieve employees in Marketing
The organization wants to update the computers for certain employees in the Marketing department.
To do this, A query will have to be created to get information on which employee machines to update.

The following query filters for employees and employee machines in the marketing department in the East building.

```sql
SELECT *
FROM employees
WHERE department = 'Marketing' AND office LIKE 'East%';
```

This query returns all employees in the marketing department who work in the East building.

## Retrieve employees in Finance or Sales
The machines for employees in the Finance and Sales departments also need to be updated.
Since a different security update is needed, A new query is needed to get information on employees only from
these two departments.

The following query filters for employees in the Finance and Sales departments.

```sql
SELECT *
FROM employees
WHERE department = 'Finance' OR department = 'Sales';
```

This query returns all employees working in Finance or Sales departments.

## Retrieve all employees not in IT
One more security update on employees who are not in the
Information Technology department. To make the update, A query has to be made to get information on
these employees.

The following query filters for employees not in the IT department.

```sql
SELECT *
FROM employees
WHERE NOT department = 'Information Technology';
```

This query will return all employees who are not in the Information Technology department.

## Summary
By applying filters to SQL queries specific information on login attemptts and employee machines can be obtained to get concise data needed for each task.