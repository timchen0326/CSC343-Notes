![[Screenshot 2025-02-09 at 8.18.54 PM.png]]
Which of these queries is legal?  
```sql
(a) --legal
SELECT count(distinct dept), count(distinct instructor)  
FROM Offering  
WHERE term >= 20089;  
(b) --illegal
SELECT distinct dept, distinct instructor  
FROM Offering  
WHERE term >= 20089;  
(c) --legal
SELECT distinct dept, instructor  
FROM Offering  
WHERE term >= 20089;
```

Under what conditions could these two queries give different results? If that is not possible, explain why.  
```sql
SELECT surName, campus  
FROM Student;

SELECT distinct surName, campus  
FROM Student;


-- they are not equivalent queries

-- equivalent queries - these two queries return the exact same results for every instance (for every possible dataset)
```

![[Screenshot 2025-02-09 at 11.00.45 PM.png]]

![[Screenshot 2025-02-09 at 11.01.00 PM.png]]
right join

![[Screenshot 2025-02-09 at 11.01.10 PM.png]]

left join