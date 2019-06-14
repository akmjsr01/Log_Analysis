# Log Analysis Project

The project is to to use SQL database knowledge to extract various data from a large database with over a million rows using both the command line and code. I built and refined complex queries to to draw business conclusions from data in this case answer questions related to News Paper articles.

A PostgreSQL database has been used and task was to create a reporting tool which prints out reports based on the data in the database. The reporting tool is a Python program that uses the psycopg2 module to connect to the database.


# Running the program
To run the files in this project following are required:
- Python 3.6.x
- PostgreSQL 
- Vagrant
- VirtualBox

To execute the program, run ```python3 newsreportingtool.py``` from the command line.



# Data used in the project
The database contains newspaper articles and the web server log for the site. Each row in the log represents a user fetched a web page, the number of rows will help answer questions about the site's user activity.


# Questions for this assignment

1. What are the most popular three articles of all time? 

```SQL
SELECT title, author, count(title) AS views
FROM articles, log
WHERE log.path=CONCAT('/article/',articles.slug) AND log.status LIKE '%200%'
GROUP BY articles.title, articles.author
ORDER BY views DESC
LIMIT 3;
```

Output:
```
title                            | author | views
----------------------------------+--------+--------
Candidate is jerk, alleges rival |      2 | 338647
Bears love berries, alleges bear |      1 | 253801
Bad things gone, say good people |      3 | 170098

```


2. Who are the most popular article authors of all time? 

```SQl
SELECT authors.name, count(*) as views FROM articles inner
JOIN authors on articles.author = authors.id inner
JOIN log on log.path LIKE concat('%', articles.slug, '%')
WHERE log.status LIKE '%200%'
GROUP BY authors.name
ORDER BY views DESC
```

Output:
```
name                   | views
------------------------+--------
Ursula La Multa        | 507594
Rudolf von Treppenwitz | 423457
Anonymous Contributor  | 170098
Markoff Chaney         |  84557
```


3. On which days did more than 1% of requests lead to errors? 

```SQL
SELECT * FROM (SELECT date(time),round(100.0*sum(CASE log.status
WHEN '200 OK'  THEN 0 ELSE 1 END)/count(log.status),3)
AS error FROM log
GROUP BY date(time)
ORDER BY error DESC) AS subquery
WHERE error > 1;
```

Output:
```
date       | error
------------+-------
2016-07-17 | 2.263

```


# REFERENCES


- https://www.w3schools.com/sql/sql_view.asp
- https://stackoverflow.com
- https://app.pluralsight.com/library/courses/introduction-to-sql/table-of-contents
