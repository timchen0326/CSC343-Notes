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

---
Which of these queries is legal?