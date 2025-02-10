Login to PostgreSQL
```bash
psql csc343h-chenw313
```
Loading Data
```bash
\i coursesDDL.txt
\i coursesData.txt
``` 

DDL = Data Definition Language
coursesData.txt contains data manipulation

```bash
set search_path to university
```
for loading up the schema

`\dn` shows which database I am working on

# SQL Exercises: GROUP BY and HAVING

![[Screenshot 2025-02-09 at 8.18.54 PM.png]]

Write a query to find the average grade, minimum grade, and maximum grade for each offering.
```sql
SELECT AVG(grade), MIN(grade), MAX(grade) FROM Took GROUP BY oid;
```

Which of these queries is legal?

```sql
-- (a)  illegal
SELECT surname, sid  -- sid ambiguous, need to insert Student.sid  to resolve problem 
FROM Student, Took  
WHERE Student.sid = Took.sid  
GROUP BY sid; -- here we need Student.sid, surname 
-- (b)  
SELECT instructor, max(grade),  
	count(Took.oid)  
FROM Took, Offering  
WHERE Took.oid = Offering.oid  
GROUP BY instructor;  
-- (c)  illegal, if you have to use something in select, then you have to use it in an aggregator
SELECT surname, Student.sid  
FROM Student, Took  
WHERE Student.sid = Took.sid  
GROUP BY campus; -- its okay to use campus in GROUP BY, since its not in SELECT, but surname and Student.sid has to be in GROUP BY 
-- (d)  legal
SELECT Course.dept, Course.cnum,  
	count(oid), count(instructor)  
FROM Course, Offering  
WHERE Course.dept = Offering.dept and  
	Course.cnum = Offering.cnum  
GROUP BY Course.dept, Course.cnum  
ORDER BY count(oid);
```

Write a query to find the `sid`and minimum grade of each student with an average over 80

```sql
SELECT sid, MIN(grade) FROM Took GROUP BY sid HAVING AVE(grade) > 80;
```

Averages for each offering and how many took it

```sql
SELECT oid, avg(grade), count(*)
FROM Took
GROUP BY oid ORDER BY oid;
```

Averages for each offering and how many took it and `oid` > 10

```sql
SELECT oid, avg(grade), count(*)
FROM Took
GROUP BY oid HAVING oid > 10 ORDER BY oid;
```

Find the `sid`, `surname`, and average grade of each student, but report the results only for those students who  have taken at least 10 courses.

```sql
SELECT Took.sid, surname, avg(grade)
FROM Student, Took
WHERE Student.sid = Took.sid
GROUP BY Took.sid, surname -- make sure Took.sid and surname must be used in an aggregator or GROUP BY
HAVING count(oid) >= 10;
```

For each student who has passed at least 10 courses, report their `sid` and average grade on the courses that  they passed.

```sql
SELECT sid,avg(grade)
FROM Took
WHERE grade >= 50
GROUP BY sid
HAVING count(oid) >= 10;
```

For each student who has passed at least 10 courses, report their `sid` and average grade on all of their courses.

```sql
CREATE VIEW PassedTen AS SELECT sid -- using CREATE VIEW is like creating intermediate relations
FROM Took
WHERE grade >= 50
GROUP BY sid
HAVING count(oid) >= 10;
```
```sql
SELECT Took.sid, avg(grade)
FROM PassedTen, Took 
WHERE PassedTen.sid = Took.sid 
GROUP BY Took.sid
```

Which of these queries is legal?

```sql
-- (a)  illegal
SELECT dept, max(grade)  
FROM Took, Offering  
WHERE Took.oID = Offering.oID  
GROUP BY dept  
HAVING cnum >= 200;  -- each group can have more than 1 cNum
-- (b)  legal
SELECT Took.oID, avg(grade)  
FROM Took, Offering  
WHERE Took.oID = Offering.oID  
GROUP BY Took.oID  
HAVING avg(grade) > 75;  
-- (c)  legal
SELECT cnum, count(dept)  
FROM Course  
GROUP BY cnum  
HAVING cnum >= 200;  
-- (d)  illegal
SELECT oID, avg(grade)  
FROM Took  
GROUP BY sID  -- oID needs to be in an aggregator or in GROUP BY
HAVING avg(grade) > 75;
```

