![[Screenshot 2025-02-09 at 8.18.54 PM.png]]

What does this query do? (Recall that the || operator concatenates two strings.)
```sql
SELECT sid, dept || cnum as course, grade  -- || is concateation;  as course renames a column
FROM Took,  
	(SELECT *  
	FROM Offering  
	WHERE instructor = ’Horton’) Hoffering  
WHERE Took.oid = Hoffering.oid;
```
![[Screenshot 2025-02-09 at 10.12.33 PM.png]]

What does this query do?

```sql
SELECT sid, surname  
FROM Student  
WHERE cgpa >  
	(SELECT cgpa  
	FROM Student  
	WHERE sid = 99999);
```
![[Screenshot 2025-02-09 at 10.14.47 PM.png]]