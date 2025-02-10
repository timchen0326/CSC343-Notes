Now let’s write some queries! For each course, that is, each department and course number combination,  find the instructor who has taught the most offerings of it. If there are ties, include them all. Report the  course (eg “csc343”), instructor and the number of offerings of the course by that instructor.  
(a) First, create a view called Counts to hold, for each course, and each instructor who has taught it, their  number of offerings.  

```sql
create view counts as  
SELECT dept || cnum as course, instructor, count(oid)  
from Offering  
group by dept, cnum, instructor;  
SELECT course, instructor, count  
from counts c1  
WHERE count = (  
	SELECT max(count)  
	from counts c2  
	WHERE c1.course = c2.course);
```
(b) Now solve the problem. Do not use any joins. (This will force you to use a subquery.)  




Use EXISTS to find the surname and email address of students who have never taken a CSC course.  

```sql
SELECT surname, email  
from student  
WHERE not exists (  
	SELECT *  
	from took, offering  
	WHERE took.oid = offering.oid and  
	student.sid = took.sid and  
	offering.dept = 'CSC');
```


Use EXISTS to find every instructor who has given a grade of 100.  

 
 
 
 Let’s say that a course has level “junior” if its cNum is between 100 and 299 inclusive, and has level “senior”  if its cNum is between 300 and 499 inclusive. Report the average grade, across all departments and course  offerings, for all junior courses and for all senior courses. Report your answer in a table that looks like this:  
 
level | levelavg  
---------|-----------  
junior |  
senior |  


Each average should be an average of the individual student grades, not an average of the course averages.

```sql
-- Subqueries worksheet Q10, in the style of what you will submit for A2  
-- You must not change the next 2 lines or the table definition.  
SET SEARCH_PATH TO University;  
DROP TABLE IF EXISTS q10 CASCADE;  
CREATE TABLE q10 (  
level CHAR(20) NOT NULL,  
levelavg FLOAT NOT NULL  
);  
-- Do this for each of the views that define your intermediate steps.  
-- (But give them better names!) The IF EXISTS avoids generating an error  
-- the first time this file is imported.  
-- If you do not define any views, you can delete the lines about views.  
DROP VIEW IF EXISTS Grades CASCADE;  
-- Define views for your intermediate steps here:  
CREATE VIEW Grades as  
SELECT cnum, dept, grade  
from offering, took  
WHERE offering.oid = took.oid;  
-- Your query that answers the question goes below the "insert into" line:  
INSERT INTO q10  
(SELECT 'junior' as level, avg(grade) as levelavg from grades WHERE cnum >= 100 and  
cnum <= 299)  
union  
(SELECT 'senior' as level, avg(grade) as levelavg from Grades WHERE cnum >= 300 and cnum <=499);
```