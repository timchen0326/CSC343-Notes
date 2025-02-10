![[Screenshot 2025-02-09 at 8.18.54 PM.png]]

Use a set operation to find all terms when Jepson and Suzuki were both teaching. Include every occurrence  of a term from the result of both operands.

```sql
(SELECT term FROM Offering where instructor='Suzuki')
INTERSECT ALL
(SELECT term FROM Offering where instructor='Jepson')
```

Find the sID of students who have earned a grade of 85 or more in some course, or who have passed a course  taught by Atwood. Ensure that no sID occurs twice in the result.

```sql
(SELECT sid FROM Took, Offering where Offering.oid = Took.oid and instructor = 'Atwood')
 UNION
(SELECT sid FROM Took where grade >= 85);
```

Find all terms when csc369 was not offered.

```sql
(SELECT term FROM Offering) EXCEPT (SELECT term FROM Offering WHERE dept = 'CSC' and cNum = 369);
```

Make a table with two columns: oID and results. In the results column, report either “high” (if that offering  had an average grade of 80 or higher), or “low” (if that offering had an average under 60). Offerings with  an average in between will not be included.  
Hints:  
• Surprise surprise, use a set operation.  
• You can use the SELECT clause to put a literal value into a column. For example:  
SELECT ’high’ as results ....

```sql
(SELECT oid, 'high' as results FROM Took GROUP BY oid HAVING avg(grade) >= 80) UNION (SELECT oid, 'low' as results FROM Took GROUP BY oid HAVING avg(grade) < 60);
```