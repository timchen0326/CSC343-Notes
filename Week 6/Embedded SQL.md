## Overview

Embedded SQL refers to the integration of SQL queries within a conventional programming language. This approach allows for greater flexibility and control over database interactions, addressing the limitations of interactive SQL.

### Problems with Interactive SQL

- Standard SQL is **not Turing-complete**:
    - Example: Finding all colleagues of a professor based on co-teaching or shared colleagues requires loops or recursion, which SQL lacks.
- No control over **output format**.
- **Users shouldn't write SQL queries directly**:
    - Instead, queries should be generated dynamically based on user input.

## Approaches to Embedding SQL

There are three main approaches for integrating SQL with a general-purpose language:

### 1. Stored Procedures

- **Definition**: Predefined SQL routines stored in the database that can execute multiple SQL commands with logic.
- **Features**:
    - Can have **parameters** and **return values**.
    - Supports **local variables, conditionals, loops**.
    - Executes SQL queries efficiently.
- **Example: `QuietYear` Function** (Boolean function to check if a studio produced no movies or at most 10 comedies in a given year):
    
    ```sql
    CREATE FUNCTION QuietYear(y INT, s CHAR(15)) RETURNS BOOLEAN
    IF NOT EXISTS (
        SELECT * FROM Movies WHERE year = y AND studioName = s
    )
    THEN RETURN TRUE;
    
    ELSIF 10 <= (
        SELECT COUNT(*) FROM Movies 
        WHERE year = y AND studioName = s AND genre = 'comedy'
    )
    THEN RETURN TRUE;
    ELSE RETURN FALSE;
    END IF;
    ```
    
- **Usage**:
    
    - Can be called from:
        - SQL queries
        - Other stored procedures
        - Triggers
    
    ```sql
    SELECT StudioName FROM Studios WHERE QuietYear(2010, StudioName);
    ```
    
- **Standardization Issues**:
    - SQL/PSM (Persistent Stored Modules) was introduced in **SQL3 (1999)**.
    - Many DBMS vendors developed proprietary stored procedure languages (e.g., **PL/pgSQL in PostgreSQL**).

---

### 2. Statement-Level Interface (SLI)

- **Definition**: Embedding SQL statements directly into conventional programming languages (e.g., C, Java) using a **preprocessor**.
- **How It Works**:
    - SQL statements are identified with special syntax.
    - The **preprocessor** converts SQL statements into function calls before compilation.
- **Example (C Code)**:
    
    ```c
    void printNetWorth() {
      EXEC SQL BEGIN DECLARE SECTION;
        char studioName[50];
        int presNetWorth;
        char SQLSTATE[6];
      EXEC SQL END DECLARE SECTION;
      
      /* Get value for studioName from the user. */
      
      EXEC SQL SELECT netWorth INTO :presNetWorth  
             FROM Studio, MovieExec 
             WHERE Studio.name = :studioName;
      
      /* Report back to the user */
    }
    ```
    
- **Benefits**:
    - Avoids direct string manipulation for SQL.
    - Queries are **structured and safer**.
- **Downsides**:
    - Requires a **preprocessor**, adding complexity.
    - Less flexible than CLI.

---

### 3. Call-Level Interface (CLI)

- **Definition**: Instead of using a preprocessor, programmers use a database-specific **API** to execute SQL queries.
- **Examples**:
    - **C**: SQL/CLI
    - **Java**: JDBC
    - **Python**: psycopg2
- **Advantages**:
    - No preprocessor required.
    - More flexible than SLI.
    - Supports dynamic queries.
- **Using psycopg2 with Python**:
    
    ```python
    import psycopg2
    
    conn = psycopg2.connect("dbname=test user=postgres")
    cur = conn.cursor()
    cur.execute("SELECT * FROM Movies WHERE year = %s", (2020,))
    rows = cur.fetchall()
    for row in rows:
        print(row)
    ```
    
- **Security Considerations**:
    - Avoid raw SQL string concatenation (SQL Injection risk!).
    - Use parameterized queries instead.

---

## SQL Injection and Security

- **What is SQL Injection?**
    - A malicious technique where user input is manipulated to alter SQL queries.
    - Example:
        
        ```python
        user_input = "' OR 1=1 --"
        query = "SELECT * FROM Users WHERE name = '" + user_input + "'"
        ```
        
    - This results in:
        
        ```sql
        SELECT * FROM Users WHERE name = '' OR 1=1 --'
        ```
        
    - The `OR 1=1` clause makes the query return all users!
- **How to Prevent It**:
    - Use **parameterized queries** instead:
        
        ```python
        cur.execute("SELECT * FROM Users WHERE name = %s", (user_input,))
        ```
        

---

## When to Use Each Approach?

|Approach|Pros|Cons|
|---|---|---|
|Stored Procedures|Efficient, precompiled, modular|Not very portable, tied to DBMS|
|Statement-Level Interface (SLI)|Structured, safer SQL execution|Requires preprocessor, less flexible|
|Call-Level Interface (CLI)|Most flexible, dynamic queries|Higher risk of SQL injection if not careful|

### Why Use psycopg2 Instead of Other Approaches?

- **Flexibility**: Works with **multiple databases** (PostgreSQL, MySQL, SQLite, etc.).
- **Dynamic Queries**: Supports queries based on user input.
- **Security**: Supports parameterized queries to **prevent SQL injection**.
- **Downside**: Requires proper error handling and connection management.

---

## References

- Ullman and Widom, **Database Systems: The Complete Book**, Chapter 9.
- PostgreSQL Documentation: [PL/pgSQL Overview](https://www.postgresql.org/docs/14/plpgsql-overview.html)
- psycopg2 Documentation: [psycopg.org/docs](https://www.psycopg.org/docs/)

---

These notes should help you understand Embedded SQL in-depth and apply the concepts in projects or exams!